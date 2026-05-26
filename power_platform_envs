# Architecture Questions - February 13, 2026

## Table of Contents
1. [Do We Need Multiple Tenants?](#do-we-need-multiple-tenants)
2. [Dataverse Environments Naming Conventions](#dataverse-environments-naming-conventions)

---

## Do We Need Multiple Tenants?

### Short Answer
**No, for most CI/CD scenarios a single Microsoft 365 tenant with multiple Dataverse environments is sufficient and recommended.**

### Explanation

#### Tenants vs Environments
- **Tenant**: A dedicated instance of Microsoft 365 and Azure AD containing all users, licenses, and environments
- **Environment**: An isolated Dataverse instance within a tenant (e.g., Dev, UAT, Production)

#### When Single Tenant is Sufficient (Recommended)
Use **one tenant with multiple environments** when:
- All environments serve the same organization
- Users can share a common Azure AD
- Cost optimization is a priority (no license duplication)
- Simplified user management is desired
- CI/CD pipelines can use shared service principals

**Benefits:**
- ✅ Simplified user management (single Azure AD)
- ✅ Shared security policies and compliance
- ✅ Lower licensing costs (single tenant licenses)
- ✅ Easier service principal management
- ✅ Simplified CI/CD pipeline authentication
- ✅ Cross-environment solution migrations are straightforward

#### When Multiple Tenants Are Needed
Consider **separate tenants** only for:
- **Client Isolation**: Serving multiple external clients who must not share any infrastructure
- **Geographical Requirements**: Data sovereignty laws requiring separate tenants per region
- **Acquisition/Subsidiaries**: Legally separate entities with independent IT governance
- **Compliance Mandates**: Regulatory requirements for complete isolation
- **Testing Microsoft Updates**: Separate tenant for preview/early release programs

**Drawbacks:**
- ❌ Duplicate licensing costs across tenants
- ❌ Complex user management (multiple Azure ADs)
- ❌ Separate service principals per tenant
- ❌ More complex CI/CD pipeline configuration
- ❌ Difficult to share resources or insights across tenants

### Recommendation for Your Architecture

Based on your documented environment hierarchy:
```
Individual Dev (Dev1-N) → Main Dev → UAT → Production
```

**Use a single tenant structure:**

| Tenant | Environments | Purpose |
|--------|--------------|---------|
| **Primary Tenant** | Dev1, Dev2, ..., DevN | Individual developer sandboxes |
| | MainDev | Integration environment |
| | UAT | User acceptance testing |
| | Production | Live production environment |

**Service Principal Strategy:**
- Create one service principal per environment for pipeline authentication
- All service principals reside in the same Azure AD tenant
- Use environment-specific Azure DevOps service connections

---

## Dataverse Environments Naming Conventions

### Naming Pattern

Use a structured naming convention that identifies environment purpose, tier, and optionally region/instance:

```
{Organization}-{Purpose}-{Tier}[-{Client}][-{Instance}]
```

### Examples

```
Nextwit-Power-Dev01          # Developer 1's environment
Nextwit-Power-Dev02          # Developer 2's environment
Nextwit-Power-MainDev        # Main development/integration
Nextwit-Power-UAT            # User acceptance testing
Nextwit-Power-Prod           # Production (default/single client)
Nextwit-Power-Prod-ClientA   # Production for Client A
Nextwit-Power-Prod-ClientB   # Production for Client B
Nextwit-Power-Sandbox        # Experimentation/training
```

### Naming Components

| Component | Description | Examples | Required |
|-----------|-------------|----------|----------|
| **Organization** | Company/project identifier | Nextwit, NW, Contoso | Yes |
| **Purpose** | Platform identifier | Power, Dataverse, CRM | Yes |
| **Tier** | Environment type | Dev01-99, MainDev, UAT, Prod, Sandbox | Yes |
| **Client** | Client/customer identifier | ClientA, ClientB, Acme, ContosoLtd | Optional |
| **Instance** | Multiple instances of same tier | 01, 02, Blue, Green | Optional |

### Tier Naming Standards

```yaml
Individual Developer Environments:
  Pattern: Dev{01-99}
  Examples: Dev01, Dev02, Dev15
  Notes: Zero-padded two-digit numbers for sorting

Integration/Main Development:
  Pattern: MainDev
  Examples: MainDev
  Notes: Single integration environment

User Acceptance Testing:
  Pattern: UAT
  Examples: UAT, UAT-ClientA
  Notes: Can have client-specific UAT environments if needed

Production:
  Pattern: Prod
  Examples: Prod, Prod-ClientA, Prod-ClientB
  Notes: Use client suffixes for multi-client deployments or Blue/Green for zero-downtime (Prod-Blue, Prod-Green)

Sandbox/Training:
  Pattern: Sandbox, Training
  Examples: Sandbox, Training01
  Notes: For experimentation, not connected to CI/CD pipeline
```

### Related Naming Conventions

#### Service Principals
Match service principal names to target environments:
```
SP-Nextwit-Power-Dev01
SP-Nextwit-Power-MainDev
SP-Nextwit-Power-UAT
SP-Nextwit-Power-Prod
```

#### Azure DevOps Service Connections
Use consistent naming for pipeline connections:
```
Dataverse-Nextwit-Dev01
Dataverse-Nextwit-MainDev
Dataverse-Nextwit-UAT
Dataverse-Nextwit-Prod
```

#### Environment Variables in Pipelines
```yaml
variables:
  - name: DEV_ENV_URL
    value: 'https://nextwit-power-dev01.crm.dynamics.com'
  - name: MAINDEV_ENV_URL
    value: 'https://nextwit-power-maindev.crm.dynamics.com'
  - name: UAT_ENV_URL
    value: 'https://nextwit-power-uat.crm.dynamics.com'
  - name: PROD_ENV_URL
    value: 'https://nextwit-power-prod.crm.dynamics.com'
  - name: PROD_CLIENTA_ENV_URL
    value: 'https://nextwit-power-prod-clienta.crm.dynamics.com'
  - name: PROD_CLIENTB_ENV_URL
    value: 'https://nextwit-power-prod-clientb.crm.dynamics.com'
```

### Environment URL Patterns

Dataverse automatically generates URLs based on environment name:
```
https://{environment-name}.crm.dynamics.com          # North America
https://{environment-name}.crm4.dynamics.com         # Europe
https://{environment-name}.crm5.dynamics.com         # APAC
https://{environment-name}.crm11.dynamics.com        # UK
```

**Keep environment names:**
- ✅ Lowercase or mixed case (URL-safe)
- ✅ Under 40 characters
- ✅ No spaces (use hyphens)
- ✅ Descriptive and scannable
- ❌ Avoid special characters beyond hyphens

### Environment Metadata Tags

Use Dataverse environment properties/tags for additional metadata:

| Tag Key | Example Value | Purpose |
|---------|---------------|---------|
| CostCenter | "IT-DevOps" | Billing attribution |
| Owner | "zsolt.zombik@aidevme.com" | Primary contact |
| Tier | "Production" | Environment classification |
| BackupSchedule | "Daily" | Backup frequency |
| DataClassification | "Internal" | Data sensitivity level |
| MaintenanceWindow | "Sat 02:00-04:00 UTC" | Allowed downtime window |

### PowerShell Environment Creation Example

```powershell
# Create environment with standardized naming
New-AdminPowerAppEnvironment `
    -DisplayName "Nextwit-Power-Dev01" `
    -Location "unitedstates" `
    -EnvironmentSku "Sandbox" `
    -ProvisionDatabase `
    -CurrencyName "USD" `
    -LanguageName "English"

# Add metadata tags
Set-AdminPowerAppEnvironmentMetadata `
    -EnvironmentName "nextwit-power-dev01" `
    -Metadata @{
        CostCenter = "IT-DevOps"
        Owner = "zsolt.zombik@aidevme.com"
        Tier = "Development"
    }
```

### Best Practices

1. **Consistency**: Use the same pattern across all environments
2. **Sorting**: Zero-pad numbers (Dev01, not Dev1) for alphabetical sorting
3. **Documentation**: Maintain environment inventory in README or separate doc
4. **Avoid Ambiguity**: Don't use generic names like "Test" or "Dev" without qualifiers
5. **Plan for Scale**: Design naming scheme to accommodate future growth (Dev01-99 vs Dev1-9)
6. **Client Suffixes Last**: Place client identifiers at the end (Prod-ClientA, not ClientA-Prod)
7. **Client Naming**: Use clear, consistent client identifiers (avoid sensitive client names in environment names if needed)

### Environment Inventory Template

Maintain a table of all environments:

| Environment Name | URL | Tier | Client | Owner | Purpose | Service Principal |
|------------------|-----|------|--------|-------|---------|-------------------|
| Nextwit-Power-Dev01 | org.crm.dynamics.com | Dev | - | Developer 1 | Feature development | SP-Nextwit-Power-Dev01 |
| Nextwit-Power-MainDev | org.crm.dynamics.com | Integration | - | DevOps Team | Integration testing | SP-Nextwit-Power-MainDev |
| Nextwit-Power-UAT | org.crm.dynamics.com | UAT | - | QA Team | User acceptance | SP-Nextwit-Power-UAT |
| Nextwit-Power-Prod | org.crm.dynamics.com | Production | Default | Operations | Default production | SP-Nextwit-Power-Prod |
| Nextwit-Power-Prod-ClientA | org.crm.dynamics.com | Production | Client A | Operations | Client A production | SP-Nextwit-Power-Prod-ClientA |
| Nextwit-Power-Prod-ClientB | org.crm.dynamics.com | Production | Client B | Operations | Client B production | SP-Nextwit-Power-Prod-ClientB |

---

## Implementation Checklist

- [ ] Decide on single vs multi-tenant architecture
- [ ] Define organization-specific naming pattern
- [ ] Document naming convention in repository
- [ ] Create service principals with matching names
- [ ] Update Azure DevOps service connections
- [ ] Update pipeline YAML with environment variables
- [ ] Create environment inventory documentation
- [ ] Train team on naming standards
- [ ] Add naming validation to PR templates

## References

- [Microsoft Power Platform Environments Overview](https://learn.microsoft.com/power-platform/admin/environments-overview)
- [Environment Naming Best Practices](https://learn.microsoft.com/power-platform/guidance/adoption/environment-strategy)
- Main architecture document: [dataverse-ci-cd-design.md](documentation/dataverse-ci-cd-design.md)
