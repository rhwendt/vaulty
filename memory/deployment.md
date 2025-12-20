#memory/deployment #workflow

# Deployment Best Practices

## Deployment Philosophy

- **Automate everything** - Manual steps lead to errors
- **Deploy frequently** - Small, incremental changes
- **Make it reversible** - Be able to rollback quickly
- **Monitor closely** - Know when something goes wrong
- **Test in production-like environments** - Catch issues early

## Environments

### Standard Environment Progression

```
Local → Development → Staging → Production
```

**Local**
- Developer's machine
- Rapid iteration
- May use mocks/stubs

**Development/Integration**
- Shared development environment
- Integration testing
- May have synthetic data

**Staging**
- Production mirror
- Final testing before release
- Production-like data (anonymized)

**Production**
- Live environment
- Real users and data
- Highest stability requirements

## Pre-Deployment Checklist

### Code Quality
- [ ] All tests pass
- [ ] Code review approved
- [ ] No linter/formatter errors
- [ ] Security scan completed
- [ ] Dependencies updated and vetted

### Documentation
- [ ] CHANGELOG updated
- [ ] API docs updated (if applicable)
- [ ] README updated (if needed)
- [ ] Deployment notes documented

### Database
- [ ] Migration scripts tested
- [ ] Migration rollback plan ready
- [ ] Backup created
- [ ] Schema changes backward compatible

### Configuration
- [ ] Environment variables set
- [ ] Secrets rotated if needed
- [ ] Feature flags configured
- [ ] Config validated

### Communication
- [ ] Team notified of deployment
- [ ] Stakeholders informed
- [ ] Maintenance window scheduled (if needed)
- [ ] On-call engineer identified

## Deployment Strategies

### 1. Blue-Green Deployment
```
Blue (current) → Green (new)
Switch traffic: Blue → Green
Keep Blue for quick rollback
```

**Pros**: Zero downtime, instant rollback
**Cons**: Requires double resources temporarily

### 2. Canary Deployment
```
Deploy to small subset (5-10%)
Monitor metrics
Gradually increase (25%, 50%, 100%)
```

**Pros**: Limited blast radius, gradual validation
**Cons**: More complex, longer deployment time

### 3. Rolling Deployment
```
Update instances one-by-one
Instance 1 → 2 → 3 → N
```

**Pros**: No extra resources needed
**Cons**: Mixed versions running temporarily

### 4. Feature Flags
```
Deploy code with feature disabled
Enable for % of users
Full rollout or rollback via flag
```

**Pros**: Separate deployment from release, easy rollback
**Cons**: Added code complexity

## Deployment Pipeline

### CI/CD Pipeline Stages

```yaml
1. Trigger (commit/PR merge)
   ↓
2. Build
   - Install dependencies
   - Compile/bundle code
   - Generate artifacts
   ↓
3. Test
   - Unit tests
   - Integration tests
   - Security scans
   ↓
4. Deploy to Staging
   - Run migrations
   - Deploy application
   - Smoke tests
   ↓
5. Approval Gate (manual or automatic)
   ↓
6. Deploy to Production
   - Run migrations
   - Deploy application
   - Health checks
   ↓
7. Post-Deployment
   - Smoke tests
   - Monitor metrics
   - Update status
```

### Example GitHub Actions Workflow

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run tests
        run: npm test

  deploy-staging:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        run: ./deploy.sh staging

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to production
        run: ./deploy.sh production
```

## Database Migrations

### Migration Best Practices

**Safe Migration Pattern**
```
1. Make schema changes backward compatible
2. Deploy code that works with both schemas
3. Run migration
4. Deploy code using new schema
5. Clean up old schema (later)
```

**Example: Adding a Required Column**
```sql
-- Step 1: Add column as nullable
ALTER TABLE users ADD COLUMN email VARCHAR(255);

-- Step 2: Backfill data
UPDATE users SET email = CONCAT(username, '@example.com') WHERE email IS NULL;

-- Step 3: Make column required (later deployment)
ALTER TABLE users ALTER COLUMN email SET NOT NULL;
```

### Migration Checklist
- [ ] Test migration on production-like data
- [ ] Estimate migration duration
- [ ] Plan for long-running migrations
- [ ] Have rollback script ready
- [ ] Backup database before migration

## Secrets Management

### Best Practices
- **Never commit secrets** to version control
- **Use secret management tools** (AWS Secrets Manager, Vault, etc.)
- **Rotate secrets regularly**
- **Limit secret access** to necessary services only
- **Audit secret access**

### Environment Variables
```bash
# Development (.env.development)
DATABASE_URL=localhost:5432
API_KEY=dev-key-12345

# Production (from secret manager)
DATABASE_URL=${SECRET:prod-db-url}
API_KEY=${SECRET:prod-api-key}
```

## Monitoring & Alerts

### Key Metrics to Monitor

**Application Health**
- Error rate
- Response time
- Request rate
- Success rate

**Infrastructure**
- CPU usage
- Memory usage
- Disk space
- Network I/O

**Business Metrics**
- User signups
- Transactions
- Active users
- Revenue

### Alerting Strategy

**Critical Alerts** (page on-call)
- Service down
- Error rate > 5%
- Database unavailable
- Security breach

**Warning Alerts** (notify team)
- Error rate > 1%
- Response time > 2s
- Disk usage > 80%
- Memory usage > 85%

## Rollback Procedures

### When to Rollback
- Critical bugs in production
- Significant performance degradation
- Security vulnerabilities introduced
- Data corruption or loss

### Rollback Process
```
1. Identify issue and decide to rollback
2. Notify team and stakeholders
3. Execute rollback procedure
4. Verify rollback successful
5. Monitor for issues
6. Conduct post-mortem
```

### Rollback Strategies

**Code Rollback**
```bash
# Revert to previous version
git revert <commit-hash>
git push origin main

# Or redeploy previous version
./deploy.sh --version=v1.2.3
```

**Database Rollback**
```bash
# Run rollback migration
./migrate.sh down

# Or restore from backup (last resort)
./restore-db.sh backup-timestamp
```

## Post-Deployment

### Immediate Checks (0-15 min)
- [ ] Application is accessible
- [ ] Health check endpoints return 200
- [ ] No error spikes in logs
- [ ] Key workflows work (smoke tests)
- [ ] Metrics are normal

### Short-term Monitoring (1-4 hours)
- [ ] Error rates stable
- [ ] Performance metrics normal
- [ ] No customer complaints
- [ ] Database performance stable

### Long-term Validation (24-48 hours)
- [ ] All metrics healthy
- [ ] No unexpected behavior
- [ ] Feature flags validated
- [ ] Update deployment log

## Deployment Communication

### Pre-Deployment Announcement
```
Deployment scheduled: [Date/Time]
Expected duration: [Duration]
Expected impact: [None/Brief downtime/etc.]
Changes included: [Summary or link to changelog]
Contact: [On-call engineer]
```

### Post-Deployment Update
```
Deployment completed: [Time]
Status: [Success/Issues/Rolled back]
Next steps: [Monitoring/Follow-up/etc.]
```

## Common Deployment Issues

### Issue: Database Migration Timeout
**Solution**: Run migrations separately, use batching for large updates

### Issue: Cache Invalidation
**Solution**: Clear caches post-deployment, use versioned cache keys

### Issue: Session Loss
**Solution**: Use rolling deployment, session replication, or warn users

### Issue: Configuration Mismatch
**Solution**: Validate config in CI, use config management tools

## Deployment Checklist Template

```markdown
## Pre-Deployment
- [ ] Tests pass
- [ ] Code reviewed and approved
- [ ] Staging deployment successful
- [ ] Backup created
- [ ] Rollback plan documented
- [ ] Team notified

## Deployment
- [ ] Start deployment
- [ ] Run migrations
- [ ] Deploy application
- [ ] Run smoke tests
- [ ] Check logs for errors

## Post-Deployment
- [ ] Health checks pass
- [ ] Metrics are normal
- [ ] Key features tested
- [ ] Team notified of completion
- [ ] Documentation updated
```

## Related

- [[testing-qa]] - For testing before deployment
- [[git-workflow]] - For version control and releases
- [[project-management]] - For tracking deployment tasks
