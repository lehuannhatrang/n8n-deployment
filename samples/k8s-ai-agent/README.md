# K8s AI Agent Setup Guide

This guide will help you set up the Kubernetes AI Agent using n8n and MCP (Model Context Protocol) servers.

## Prerequisites

- Access to Kubernetes clusters
- kubectl configured for your clusters
- n8n instance running
- Base64 encoding utility

## Setup Instructions

### 1. Configure MCP Server YAML Files

Edit the `mcp-{clustername}.yaml` files based on your cluster names:

1.1. **Update cluster names in YAML files:**
   - For each cluster, edit the corresponding `mcp-{clustername}.yaml` file
   - Update the `CLUSTER_NAME` environment variable value
   - Update the deployment and service names to match your cluster name

Example for cluster named "production":
```yaml
env:
  - name: CLUSTER_NAME
    value: "production"  # Update this value
```

1.2. **Configure kubeconfig secrets:**
   - Get the kubeconfig file for each cluster
   - Encode each kubeconfig to base64:
     ```bash
     cat /path/to/your/cluster1.kubeconfig | base64 -w 0
     ```
   - Edit `mcp-kubeconfigs-secret.yaml` and add the base64 encoded kubeconfig:
     ```yaml
     data:
       cluster1.kubeconfig: <your-base64-encoded-kubeconfig-here>
       cluster2.kubeconfig: <your-base64-encoded-kubeconfig-here>
     ```

### 2. Deploy MCP Servers to Kubernetes

Deploy the MCP servers in the following order:

2.1. **Create namespace:**
```bash
kubectl apply -f mcp-servers/mcp-k8s-ns.yaml
```

2.2. **Deploy kubeconfig secrets:**
```bash
kubectl apply -f mcp-servers/mcp-kubeconfigs-secret.yaml
```

2.3. **Deploy all MCP server instances:**
```bash
kubectl apply -f mcp-servers/mcp-cluster1.yaml
kubectl apply -f mcp-servers/mcp-cluster2.yaml
# Apply additional mcp-{clustername}.yaml files as needed
```

### 3. Configure n8n Workflow

3.1. **Import workflow template:**
   - Open n8n front end
   - Create new workflow from scratch
   - Import from file
   - Choose `K8S_Multi_Cluster_Template.json`

### 4. Configure Workflow Components

4.1. **Update MCP Client URL:**
   - Double-click on the MCP Client node in the workflow
   - Update the URL according to your K8S MCP Server Service
   - Sample URL format: `http://mcp-server-{clustername}:3001` (for internal cluster access)
   - Or use NodePort: `http://{node-ip}:31331` (for external access)

4.2. **Configure AI Model:**
   - Double-click on the AI Model node
   - Set up the credentials (API key, etc.)
   - Configure the model name (e.g., "gpt-4", "claude-3-sonnet", etc.)

### 5. Test and Enable Workflow

5.1. **Test the workflow:**
   - Run a test execution to ensure all components are working
   - Verify MCP server connectivity
   - Check AI model responses

5.2. **Save and enable:**
   - Save the workflow
   - Enable the workflow for production use

### 6. Get Webhook URL

6.1. **Configure webhook for external access:**
   - Double-click on the Webhook node
   - Switch to the "Production" tab
   - Copy the webhook URL
   - This URL will be used for external integrations and API calls

## Troubleshooting

- **MCP Server Connection Issues:** Check if the MCP server pods are running and accessible
- **Kubeconfig Issues:** Verify base64 encoding is correct and kubeconfig has proper permissions
- **AI Model Issues:** Ensure API credentials are valid and model name is correct
- **Webhook Issues:** Check n8n webhook settings and firewall configurations

## Service Endpoints

After deployment, your MCP servers will be available at:
- Internal: `http://mcp-server-{clustername}.mcp-k8s.svc.cluster.local:3001`
- NodePort: `http://{node-ip}:313XX` (where XX corresponds to your cluster number)

## Notes

- Each cluster requires its own MCP server deployment
- Kubeconfig files should have appropriate RBAC permissions for the operations you want to perform
- The webhook URL can be used to trigger the workflow from external systems
