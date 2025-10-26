# Coolify API Endpoints Reference

Base URL: `https://your-coolify-instance.com/api/v1`

All endpoints require bearer token authentication:
```bash
-H "Authorization: Bearer YOUR_API_TOKEN"
```

## Services

### Get Service Details
```
GET /services/{uuid}
```
Returns detailed service information including docker-compose configuration, applications, databases, and server details.

### List All Services
```
GET /services
```
Returns a list of all services.

### Service Control
```
POST /services/{uuid}/start
POST /services/{uuid}/stop
POST /services/{uuid}/restart
```

## Applications

### Get Application Details
```
GET /applications/{uuid}
```
Returns application configuration, status, and metadata.

### Get Application Logs
```
GET /applications/{uuid}/logs?lines=200
```
Query Parameters:
- `lines` (optional): Number of log lines to retrieve (default: 100)

### Application Control
```
POST /applications/{uuid}/start
POST /applications/{uuid}/stop
POST /applications/{uuid}/restart
```

## Deployments

### Deploy a Resource
```
POST /deploy
```
Request body:
```json
{
  "uuid": "resource-uuid"
}
```

### Get Deployment Status
```
GET /deployments/{uuid}
```

## Servers

### List Servers
```
GET /servers
```

### Get Server Details
```
GET /servers/{uuid}
```

### Validate Server Connection
```
POST /servers/{uuid}/validate
```

## Projects

### List Projects
```
GET /projects
```

### Get Project Details
```
GET /projects/{uuid}
```

## Teams

### List Teams
```
GET /teams
```

### Get Team Members
```
GET /teams/{id}/members
```

## Resources

### List All Resources
```
GET /resources
```
Returns all resources (applications, services, databases) with UUIDs, names, types, and status.

## Environment Variables

### Get Application Environment Variables
```
GET /applications/{uuid}/envs
```

### Update Environment Variables
```
PATCH /applications/{uuid}/envs
```

## Databases

### List Databases
```
GET /databases
```

### Get Database Details
```
GET /databases/{uuid}
```

### Database Control
```
POST /databases/{uuid}/start
POST /databases/{uuid}/stop
POST /databases/{uuid}/restart
```

## Common Response Codes

- `200` - Success
- `401` - Unauthorized (check your API token)
- `404` - Resource not found
- `422` - Validation error
- `500` - Server error

## Rate Limiting

Check response headers for rate limit information:
- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

## Example Usage

### Using curl
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://your-instance.com/api/v1/resources
```

### Filtering JSON with jq
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://your-instance.com/api/v1/services/SERVICE_UUID \
  | jq '.applications[0].status'
```
