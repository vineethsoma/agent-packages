---
applyTo: "**/*search*.{ts,js,py},**/*similarity*.{ts,js,py},**/*embedding*.{ts,js,py}"
description: Guidelines for similarity metrics in vector search implementations
---

# Similarity Metrics for Vector Search

Apply these standards when implementing similarity-based search or ranking.

## Core Principle: Know Your Metric's Range

**Every similarity/distance metric has a mathematical range. Validation and filtering MUST respect this range.**

---

## Similarity Metrics Reference

### 1. Cosine Similarity

**Range**: [-1, 1]

**Formula**: `cos(θ) = (A · B) / (||A|| × ||B||)`

**Interpretation**:
- `1.0` = Identical direction (perfect match)
- `0.0` = Orthogonal (unrelated)
- `-1.0` = Opposite direction (complete mismatch)

**When to Use**:
- Text embeddings (OpenAI, BERT, Sentence Transformers)
- When direction matters more than magnitude
- Normalized vectors

**Implementation**:
```typescript
function cosineSimilarity(a: number[], b: number[]): number {
  const dotProduct = a.reduce((sum, val, i) => sum + val * b[i], 0);
  const magnitudeA = Math.sqrt(a.reduce((sum, val) => sum + val * val, 0));
  const magnitudeB = Math.sqrt(b.reduce((sum, val) => sum + val * val, 0));
  return dotProduct / (magnitudeA * magnitudeB);
}

// Validation MUST allow negative scores
function searchBySimilarity(
  queryEmbedding: number[], 
  minScore: number = -1  // ✅ Default to -1, not 0
): Result[] {
  if (minScore < -1 || minScore > 1) {
    throw new Error('MinScore must be between -1 and 1 for cosine similarity');
  }
  
  return results.filter(r => r.score >= minScore);
}
```

**Common Thresholds**:
- `0.8-1.0`: Very similar (good matches)
- `0.5-0.8`: Moderately similar
- `0.0-0.5`: Weak similarity
- `< 0.0`: Dissimilar (filter out)

---

### 2. Euclidean Distance

**Range**: [0, ∞)

**Formula**: `d = √(Σ(a_i - b_i)²)`

**Interpretation**:
- `0.0` = Identical (perfect match)
- Larger values = More different
- No upper bound

**When to Use**:
- Spatial data (coordinates, physical measurements)
- When magnitude matters
- Clustering algorithms (K-means)

**Implementation**:
```typescript
function euclideanDistance(a: number[], b: number[]): number {
  return Math.sqrt(
    a.reduce((sum, val, i) => sum + Math.pow(val - b[i], 2), 0)
  );
}

// Search by distance (lower is better)
function searchByDistance(
  queryEmbedding: number[],
  maxDistance: number = Infinity  // ✅ No upper limit by default
): Result[] {
  if (maxDistance < 0) {
    throw new Error('MaxDistance must be non-negative');
  }
  
  return results
    .filter(r => r.distance <= maxDistance)
    .sort((a, b) => a.distance - b.distance);
}
```

**Common Thresholds** (depends on embedding dimensions):
- For 128-dim embeddings: `< 0.5` (very similar), `< 1.0` (similar)
- For 1536-dim embeddings: `< 5.0` (similar), `< 10.0` (moderately similar)

---

### 3. Dot Product

**Range**: (-∞, ∞)

**Formula**: `A · B = Σ(a_i × b_i)`

**Interpretation**:
- Larger positive values = More similar
- Near zero = Unrelated
- Negative values = Opposite

**When to Use**:
- Fast similarity (no normalization needed)
- When vectors are already normalized (equivalent to cosine)
- Neural network output layers

**Implementation**:
```typescript
function dotProduct(a: number[], b: number[]): number {
  return a.reduce((sum, val, i) => sum + val * b[i], 0);
}

// No default bounds - depends on data
function searchByDotProduct(
  queryEmbedding: number[],
  minScore: number = -Infinity
): Result[] {
  return results
    .filter(r => r.score >= minScore)
    .sort((a, b) => b.score - a.score);
}
```

---

### 4. Jaccard Similarity

**Range**: [0, 1]

**Formula**: `J(A,B) = |A ∩ B| / |A ∪ B|`

**Interpretation**:
- `1.0` = Perfect overlap
- `0.0` = No overlap

**When to Use**:
- Set similarity (tags, categories)
- Binary features (present/absent)
- Recommendation systems (common items)

**Implementation**:
```typescript
function jaccardSimilarity(a: Set<string>, b: Set<string>): number {
  const intersection = new Set([...a].filter(x => b.has(x)));
  const union = new Set([...a, ...b]);
  return intersection.size / union.size;
}

function searchByJaccard(
  queryTags: Set<string>,
  minScore: number = 0  // ✅ Range [0, 1]
): Result[] {
  if (minScore < 0 || minScore > 1) {
    throw new Error('MinScore must be between 0 and 1 for Jaccard');
  }
  
  return results.filter(r => r.score >= minScore);
}
```

---

## Choosing the Right Metric

| Scenario | Recommended Metric | Rationale |
|----------|-------------------|-----------|
| Text search (embeddings) | Cosine Similarity | Direction matters, magnitude normalized |
| Image similarity | Cosine or Euclidean | Both work; cosine if embeddings normalized |
| Spatial coordinates | Euclidean Distance | Physical distance meaningful |
| Tag-based matching | Jaccard Similarity | Set overlap |
| Fast approximate search | Dot Product | No sqrt/normalization overhead |

---

## Database Integration

### PostgreSQL with pgvector

```sql
-- Install pgvector extension
CREATE EXTENSION vector;

-- Create table with vector column
CREATE TABLE items (
  id serial PRIMARY KEY,
  embedding vector(1536)
);

-- Cosine similarity (lower distance = more similar)
SELECT id, 1 - (embedding <=> '[0.1, 0.2, ...]'::vector) as similarity
FROM items
WHERE 1 - (embedding <=> '[0.1, 0.2, ...]'::vector) >= 0.3
ORDER BY embedding <=> '[0.1, 0.2, ...]'::vector
LIMIT 10;

-- Euclidean distance (L2)
SELECT id, embedding <-> '[0.1, 0.2, ...]'::vector as distance
FROM items
ORDER BY embedding <-> '[0.1, 0.2, ...]'::vector
LIMIT 10;

-- Dot product (inner product)
SELECT id, (embedding <#> '[0.1, 0.2, ...]'::vector) * -1 as similarity
FROM items
ORDER BY embedding <#> '[0.1, 0.2, ...]'::vector
LIMIT 10;
```

### SQLite with BLOBs

```typescript
// Store embedding as BLOB
const embedding = new Float32Array([0.1, 0.2, ...]);
db.prepare('INSERT INTO items (embedding) VALUES (?)')
  .run(Buffer.from(embedding.buffer));

// Retrieve and calculate similarity in application
const items = db.prepare('SELECT id, embedding FROM items').all();
const scored = items.map(item => {
  const itemEmbedding = Array.from(new Float32Array(item.embedding.buffer));
  return {
    id: item.id,
    score: cosineSimilarity(queryEmbedding, itemEmbedding)
  };
});
```

---

## Threshold Selection Guidelines

### Setting minScore/maxDistance

**Process**:
1. **Understand the metric range** (consult reference above)
2. **Run queries on sample data** to see score distribution
3. **Set threshold based on desired precision/recall**

**Example**:
```typescript
// Test query on sample data
const results = search('test query', minScore = -1); // No filtering
console.log('Score distribution:', {
  min: Math.min(...results.map(r => r.score)),
  max: Math.max(...results.map(r => r.score)),
  median: results[Math.floor(results.length / 2)].score
});

// Based on distribution, set threshold
// If scores range from -0.2 to 0.8:
// - Use 0.3 for high precision (fewer, better matches)
// - Use 0.0 for high recall (more matches, some weak)
const SIMILARITY_THRESHOLD = 0.3;
```

**Default Recommendations**:
- **Cosine Similarity**: Default to `-1` (no filtering), let client-side decide
- **Euclidean Distance**: Default to `Infinity` (no filtering), or set based on dimension
- **Dot Product**: No default (unbounded), require explicit threshold

---

## Common Pitfalls

### ❌ Pitfall 1: Wrong Range for Cosine Similarity

```typescript
// ❌ Filters out negative scores
if (minScore < 0 || minScore > 1) { ... }

// ✅ Correct range
if (minScore < -1 || minScore > 1) { ... }
```

### ❌ Pitfall 2: Using Distance as Similarity

```typescript
// ❌ Euclidean distance sorted descending (wrong direction)
results.sort((a, b) => b.distance - a.distance);

// ✅ Distance sorted ascending (smaller = more similar)
results.sort((a, b) => a.distance - b.distance);
```

### ❌ Pitfall 3: Comparing Metrics Across Queries

```typescript
// ❌ Storing absolute scores for ranking
saveScore(item, score: 0.65);

// ✅ Scores only meaningful within single query
// Use relative ranking: position 1, 2, 3...
saveRanking(item, rank: 1, queryId: 'abc123');
```

---

## Validation Checklist

Before deploying vector search:
- [ ] Metric range matches validation constraints
- [ ] Default threshold includes full range (no premature filtering)
- [ ] Sort direction correct (similarity descending, distance ascending)
- [ ] Edge cases tested (empty results, all negative scores, ties)
- [ ] Performance acceptable for dataset size (indexing if needed)

---

**Remember**: Similarity metrics have different mathematical ranges. Always validate against the correct domain, and test with real data to set appropriate thresholds.
