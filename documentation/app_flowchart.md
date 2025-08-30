flowchart TD
  A[Landing Page] --> B{New or Existing User}
  B -->|New| C[Sign Up Page]
  B -->|Existing| D[Sign In Page]
  C --> E[Submit Credentials]
  D --> E
  E --> F[Auth API]
  F --> G{Auth Successful}
  G -->|Yes| H[Dashboard Page]
  G -->|No| I[Error Message]
  H --> J[Logout]
  J --> D