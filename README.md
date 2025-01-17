# Password Manager API Documentation

## Description

The Password Manager API is a comprehensive solution crafted for environments where Mule applications are operational within a private network, ensuring secure password management. This API is tailored for businesses that still utilize email for password sharing, offering safer and more efficient alternatives.

## Solution Design

The API can be deployed across various models and utilizes Object Store V2 in CloudHub for persistent storage to prevent data loss upon instance restart. Due to Object Store's access limitations, the API employs a daily or on-demand synchronization to a local CSV file, facilitating effortless access, searchability, and backup.

## API Endpoints

### Retrieve Passwords
- `GET /passwords?query=`: Retrieve passwords based on a query that matches against the title and description.
- `GET /passwords/{title}?token=`: Fetch a specific password. If the password requires approval (`approval=true`), a token is required. If `approval=false`, the token parameter should be omitted.

### Start Authorization Flow
- `GET /passwords/{title}/authorize?retriever=`: Initiate the authorization flow for password retrieval by providing a retriever's email. Returns an authorization token and URL.

### Approve Password Request
- `GET /passwords/{title}/approve?approvalRequest={}&owner={}`: Used by the password owner to approve a pending request. Requires owner's token for authentication.

### Synchronize Data Store
- `GET /sync`: Synchronize the object store with the backup CSV file.

### Add Password
- `POST /passwords`: Add a password entry to the store.
   ```json
   {
       "title": "example-title",
       "description": "Example Description",
       "password": "encrypted-password",
       "approval": true,
       "approver": "example@email.com"
   }
   
### Delete Password

  - `DELETE /passwords/{title}`: Remove a password entry. For entries requiring approval (approval=true), a token must be provided in the request header.

### Update Password

  - `PATCH /passwords/{title}`: Update the description or content of an existing password. Changing the title will create a new entry instead of updating.

## Data Model
| Field | Description | Notes |
| :-- | :-- | :-- |
| title | Unique identifier without spaces/special chars | Can only contain / and _ |
| description | Field description allowing any characters | Used for search |
| password | Encrypted password string |  |
| approval | Boolean indicating if approval flow is needed | Defaults to false |
| approver | Email of the approver |  |
| approvedTokens | Array of approved tokens for access |  |
| pendingTokens | Array of tokens awaiting approval |  |


## Security
All sensitive data within the API, including passwords and tokens, are managed with high encryption standards. The approval flow mechanism adds an extra layer of security to safeguard password access and handling.
