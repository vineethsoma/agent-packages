---
applyTo: "**/*log*.{ts,js,py},**/*audit*.{ts,js,py},**/*search*.{ts,js,py}"
description: PII protection guidelines for logging and data handling
---

# PII Protection and Secure Logging

Apply these standards when logging user data or handling personally identifiable information.

## Core Principle: Hash PII, Log Metadata

**NEVER log raw user inputs that may contain personally identifiable information (PII).**

---

## What is PII?

### Always PII
- Full names
- Email addresses
- Phone numbers
- Physical addresses
- Social Security Numbers (SSN)
- Credit card numbers
- Medical records
- Biometric data
- Government-issued IDs

### Context-Dependent PII
- **Search queries** - May contain names, locations, medical conditions
- **User-generated content** - Comments, messages may reference PII
- **IP addresses** - GDPR considers IP addresses PII
- **Device IDs** - Can uniquely identify users
- **Session tokens** - Can be used to impersonate users
- **Geolocation data** - Precise location is PII

### NOT PII (Safe to Log)
- User ID (internal, non-reversible identifier)
- Timestamps
- Request method (GET, POST)
- HTTP status codes
- Result counts
- Aggregate statistics
- Feature flags

---

## PII Protection Patterns

### Pattern 1: Hash Sensitive Data

**Use Case**: Need to correlate same queries/inputs across sessions

**Implementation**:
```typescript
import { createHash } from 'crypto';

function hashPII(data: string): string {
  return createHash('sha256')
    .update(data)
    .digest('hex')
    .substring(0, 16);  // First 16 chars sufficient
}

// Example: Search query logging
logger.info({
  event: 'search',
  queryHash: hashPII(userQuery),  // ✅ Hashed
  queryLength: userQuery.length,  // ✅ Metadata
  resultCount: results.length,
  userId: req.user?.id  // ✅ Internal ID
});
```

**Why Hashing > Redaction**:
- Can still trace same query across multiple requests
- Irreversible - cannot recover original from hash
- Collision-resistant (SHA256)

### Pattern 2: Redact Structured Data

**Use Case**: Logging objects with mixed sensitive/non-sensitive fields

**Implementation**:
```typescript
function redactPII(obj: any): any {
  const REDACTED = '[REDACTED]';
  const PII_FIELDS = ['email', 'phone', 'ssn', 'creditCard', 'address'];
  
  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [
      key,
      PII_FIELDS.includes(key) ? REDACTED : value
    ])
  );
}

// Example: User profile logging
logger.info({
  event: 'profile_update',
  user: redactPII(userProfile)  // email, phone become [REDACTED]
});
```

### Pattern 3: Log Metadata Instead of Content

**Use Case**: Need to track activity without storing sensitive content

**Implementation**:
```typescript
// ❌ NEVER log raw message content
logger.info({ event: 'message_sent', content: message });

// ✅ Log metadata only
logger.info({
  event: 'message_sent',
  messageId: message.id,
  fromUserId: message.fromUserId,
  toUserId: message.toUserId,
  length: message.content.length,
  containsAttachment: message.attachments.length > 0
});
```

---

## GDPR Compliance

### Right to be Forgotten

**Challenge**: Logs may contain user data that must be deleted on request

**Solution**: Use hashed identifiers that can be regenerated

```typescript
// ❌ Using sequential user IDs in logs
logger.info({ userId: 12345, action: 'login' });
// Cannot delete if user exercises right to be forgotten

// ✅ Using hashed identifiers
const userHash = hashPII(user.email);
logger.info({ userHash, action: 'login' });
// Can search and delete all logs for this hash
```

### Data Minimization

**Principle**: Collect and log only what is necessary

```typescript
// ❌ Over-logging
logger.info({
  user: {
    id: user.id,
    email: user.email,  // Not needed!
    name: user.name,    // Not needed!
    phone: user.phone,  // Not needed!
  },
  action: 'page_view'
});

// ✅ Minimal logging
logger.info({
  userId: user.id,
  action: 'page_view'
});
```

---

## Error Messages and Stack Traces

### Pattern 4: Sanitize Error Messages

**Problem**: Errors may leak PII in messages or stack traces

**Implementation**:
```typescript
try {
  await db.query('SELECT * FROM users WHERE email = ?', [userEmail]);
} catch (error) {
  // ❌ Leaks email in error message
  logger.error(`Failed to find user ${userEmail}`, error);
  
  // ✅ Sanitized error
  logger.error('Failed to find user', {
    errorCode: error.code,
    userId: user.id,  // Non-PII identifier
    stack: error.stack  // OK if no PII in stack
  });
}
```

### Pattern 5: Redact URLs with PII

**Problem**: URLs may contain sensitive parameters

**Implementation**:
```typescript
function sanitizeUrl(url: string): string {
  const parsed = new URL(url);
  const SENSITIVE_PARAMS = ['email', 'ssn', 'token'];
  
  SENSITIVE_PARAMS.forEach(param => {
    if (parsed.searchParams.has(param)) {
      parsed.searchParams.set(param, '[REDACTED]');
    }
  });
  
  return parsed.toString();
}

// Example
logger.info({
  event: 'api_request',
  url: sanitizeUrl(req.url),  // email=user@example.com → email=[REDACTED]
  method: req.method
});
```

---

## Secure Logging Checklist

Before logging any user data:
- [ ] Is this PII? (consult reference above)
- [ ] Can I log hash instead of raw value?
- [ ] Can I log metadata (length, count) instead?
- [ ] Is this necessary for debugging/audit?
- [ ] Does this comply with GDPR/privacy policy?
- [ ] Can this be deleted if user requests?

---

## Log Levels and Sensitivity

**Guideline**: Higher log levels should contain LESS sensitive data

```typescript
// DEBUG (development only) - May contain more detail
logger.debug({ query: sanitizedQuery, params: redactedParams });

// INFO (production) - Business events, no PII
logger.info({ event: 'search', queryHash, resultCount });

// WARN (production) - Potential issues, minimal data
logger.warn({ event: 'rate_limit', userId, requestCount });

// ERROR (production) - Errors, no PII in message
logger.error({ event: 'db_error', errorCode, userId });
```

---

## Regulatory Compliance Summary

| Regulation | Key Requirements | Implementation |
|------------|------------------|----------------|
| **GDPR** | Right to access, deletion, portability | Hash identifiers, provide data export |
| **CCPA** | Consumer right to know, delete, opt-out | Similar to GDPR |
| **HIPAA** | Protected health info (PHI) encryption | Never log PHI, encrypt at rest |
| **PCI-DSS** | Never log credit card data | Redact card numbers, use tokens |

---

## Example: Production-Ready Logger

```typescript
import { createHash } from 'crypto';

class SecureLogger {
  private hash(data: string): string {
    return createHash('sha256')
      .update(data)
      .digest('hex')
      .substring(0, 16);
  }
  
  logSearch(query: string, userId: string, results: number) {
    console.log(JSON.stringify({
      timestamp: new Date().toISOString(),
      event: 'search',
      queryHash: this.hash(query),
      queryLength: query.length,
      userId,  // Internal ID, not email
      resultCount: results
    }));
  }
  
  logAuth(success: boolean, identifier: string) {
    console.log(JSON.stringify({
      timestamp: new Date().toISOString(),
      event: 'auth',
      success,
      identifierHash: this.hash(identifier),  // Hash email/username
    }));
  }
  
  logError(message: string, error: Error, context: Record<string, any>) {
    console.error(JSON.stringify({
      timestamp: new Date().toISOString(),
      level: 'error',
      message,  // Generic, no PII
      errorCode: error.name,
      stack: error.stack,
      context: this.redactContext(context)
    }));
  }
  
  private redactContext(obj: Record<string, any>): Record<string, any> {
    const PII_KEYS = ['email', 'phone', 'ssn', 'password', 'token'];
    return Object.fromEntries(
      Object.entries(obj).map(([key, value]) => [
        key,
        PII_KEYS.some(pii => key.toLowerCase().includes(pii)) 
          ? '[REDACTED]' 
          : value
      ])
    );
  }
}
```

---

**Remember**: When in doubt, hash or omit. You can never "un-log" PII once it's written to disk.
