# Azure Indirect Node Counting Test Playbooks

Sample playbooks for testing AAP Indirect Node Counting with Azure resources.

## Prerequisites

### 1. Install Azure Collection

```bash
ansible-galaxy collection install -r requirements.yml
```

### 2. Configure Azure Credentials

**Option A: Environment Variables (Recommended for testing)**
```bash
export AZURE_CLIENT_ID="your-client-id"
export AZURE_SECRET="your-client-secret"
export AZURE_SUBSCRIPTION_ID="your-subscription-id"
export AZURE_TENANT="your-tenant-id"
```

**Option B: Azure CLI**
```bash
az login
```

**Option C: Credentials File**
```bash
cat ~/.azure/credentials
[default]
subscription_id=your-subscription-id
client_id=your-client-id
secret=your-client-secret
tenant=your-tenant-id
```

## Playbooks

### Simple Test (`azure_indirect_node_test.yml`)

Creates a resource group and gathers information - completely free.

```bash
# Run test
ansible-playbook azure_indirect_node_test.yml

# Run with cleanup
ansible-playbook azure_indirect_node_test.yml -e cleanup=true
```

### Comprehensive Test (`azure_comprehensive_test.yml`)

Creates multiple resources to test aggregation:
- Resource Group
- Storage Account (free tier)
- Blob Container

```bash
# Run test
ansible-playbook azure_comprehensive_test.yml

# Run with cleanup
ansible-playbook azure_comprehensive_test.yml -e cleanup=true
```

## Cost

**$0** - All resources used are in Azure free tier:
- Resource Groups: Always free
- Storage Account: First 5GB free
- All resources are deleted with `-e cleanup=true`

## Verifying Indirect Node Counting

After running in AAP, check the audit table:

```sql
SELECT name, canonical_facts, events, count 
FROM main_indirectmanagednodeaudit 
WHERE job_id = YOUR_JOB_ID;
```

Expected: Resources aggregated with multiple module events tracked.

## Manual Cleanup

If you forget to cleanup:

```bash
az group delete -n aap-indirect-node-test-rg --yes
az group delete -n aap-indirect-test-rg --yes
```
