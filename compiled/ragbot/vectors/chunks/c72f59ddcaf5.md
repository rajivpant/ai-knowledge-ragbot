s**: Control over concurrent sessions
- [ ] ⚫ **Session Revocation**: Can revoke all sessions
- [ ] ⚫ **Session Visibility**: Users can see active sessions

---

## 11. SCALABILITY & PERFORMANCE (Tier 2+)

### 11.1 Scalability 🔵

- [ ] 🔵 **Stateless Design**: Application state externalized
- [ ] 🔵 **Database Not Bottleneck**: Database can handle expected load
- [ ] 🟣 **Horizontal Scaling**: Can scale horizontally
- [ ] 🟣 **Auto-Scaling**: Auto-scaling configured
- [ ] ⚫ **No Single Points of Failure**: Redundancy in place

### 11.2 Performance 🔵

- [ ] 🔵 **Acceptable Response Time**: API responses <500ms typical
- [ ] 🔵 **Caching Used**: Caching where beneficial
- [ ] 🟣 **Background Jobs**: Expensive operations offloaded
- [ ] 🟣 **CDN for Static Assets**: Static assets served via CDN
- [ ] ⚫ **Performance Baselines**: SLOs defined and monitored

---

## 12. RELIABILITY (Tier 2+)

### 12.1 Fault Tolerance 🔵

- [ ] 🔵 **Errors Handled Gracefully**: App doesn't crash on errors
- [ ] 🔵 **External Calls Have Timeouts**: All network calls timeout
- [ ] 🟣 **Retries with Backoff**: Retries use exponential backoff
- [ ] 🟣 **Circuit Breakers**: Circuit breakers for external deps
- [ ] ⚫ **Graceful Degradation**: Fallbacks when dependencies fail

### 12.2 Data Durability 🔵

- [ ] 🔵 **Backups Exist**: Database is backed up
- [ ] 🟣 **Backups Tested**: Backups verified for recoverability
- [ ] 🟣 **Transactions Used**: Database transactions used correctly
- [ ] ⚫ **Point-in-Time Recovery**: PITR available
- [ ] ⚫ **DR Plan**: Disaster recovery plan documented and tested

---

## 13. OBSERVABILITY (Tier 2+)

### 13.1 Logging 🔵

- [ ] 🔵 **Logs Exist**: Application produces logs
- [ ] 🔵 **Log Levels Used**: Appropriate use of DEBUG, INFO, WARN, ERROR
- [ ] 🟣 **Structured Logging**: Logs are structured (JSON)
- [ ] 🟣 **Correlation IDs**: Request tracing across components
- [ ] 🟣 **Centralized Logs**: Logs aggregated centrally
- [ ] ⚫ **Sensitive Data Excluded**: No secrets/PII in logs

### 13.2 Monitoring 🟣

- [ ] 🟣 **Health Checks**: Health check endpoints exist
- [ ] 🟣 **Metrics Collected**: Key metrics instrumented
- [ ] 🟣 **Dashboards Exist**: Operational dashboards available
- [ ] ⚫ **SLI/SLO Defined**: Service levels defined and tracked
- [ ] ⚫ **Distributed Tracing**: Traces across service boundaries

### 13.3 Alerting 🟣

- [ ] 🟣 **Alerts Configured**: Alerts for critical failures
- [ ] 🟣 **Alerts Actionable**: Alerts are not noisy
- [ ] ⚫ **Runbooks Linked**: Alerts link to runbooks
- [ ] ⚫ **On-Call Rotation**: Proper on-call process

---

## 14. DEPLOYMENT & OPERATIONS

### 14.1 Build & Deploy 🟢

- [ ] 🟢 **Build Documented**: How to build is documented
- [ ] 🟢 **Deploy Documented**: How to deploy is documented
- [ ] 🔵 **Automated Build**: CI builds on every commit
- [ ] 🔵 **Automated Deploy**: Deployment is automated
- [ ] 🟣 **Infrastructure as Code**: IaC for infrastructure
- [ ] 🟣 **Environment Parity**: Environments are similar

### 14.2 Deployment Strategy 🟣

- [ ] 🟣 **Zero-Downtime**: Deployments don't cause downtime
- [ ] 🟣 **Rollback Capability**: Can rollback quickly
- [ ] ⚫ **Canary/Blue-Green**: Gradual rollout supported
- [ ] ⚫ **Feature Flags**: Feature flags for releases

### 14.3 Configuration 🔵

- [ ] 🔵 **Config Externalized**: Config not hardcoded
- [ ] 🔵 **Env-Specific Config**: Different config per environment
- [ ] 🟣 **Config Validated**: Config validated at startup
- [ ] 🟣 **Config Documented**: All config options documented

---

## 15. LICENSING & LEGAL

### 15.1 Dependencies 🔵

- [ ] 🔵 **Licenses Known**: Dependencies' licenses are known
- [ ] 🔵 **No Problematic Licenses**: No GPL/AGPL if incompatible with use
- [ ] 🟣 **License Inventory**: Complete license inventory exists
- [ ] 🟣 **Attribution Met**: Attribution requirements satisfied

### 15.2 Intellectual Property 🟣

- [ ] 🟣 **Copyright Notices**: Appropriate copyright notices
- [ ] 🟣 **Code Provenance Clear**: Origin of all code is clear
- [ ] ⚫ **CLA if Need