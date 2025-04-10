# Andrometa Backend

![Andrometa Logo](https://placeholder-for-andrometa-logo.png)

Andrometa Backend is a comprehensive Node.js server that powers the Andrometa gaming platform, handling everything from user authentication to game mechanics, inventory management, and social features. The backend provides a unified API for both web and mobile clients.

## 🚀 Features

-   **Authentication**: Multiple auth methods including email, Google, Apple, and Passport integration
-   **Game Mechanics**: Leaderboards, challenges, and missions for players to complete
-   **Inventory System**: Robust item management with crafting capabilities
-   **NFT Integration**: Collection management, token trading, and fusion mechanics
-   **Social Features**: In-app chat, servers, and real-time notifications
-   **Marketplace**: Secure token/NFT listing and bidding functionality
-   **Store**: In-app purchases with platform-specific verification
-   **Quests & Achievements**: Progression tracking across multiple platforms
-   **Streaming Support**: Integration for content creators

## 🔧 Tech Stack

-   **Framework**: Express.js, TypeScript
-   **Database**: Prisma ORM with PostgreSQL
-   **Authentication**: JWT, OAuth2
-   **Real-time**: WebSockets
-   **Storage**: AWS S3
-   **Payments**: Stripe integration

## 🛠️ Setup & Installation

### Prerequisites

-   Node.js 16+
-   PostgreSQL
-   AWS account for S3 storage
-   Stripe account for payments

### Installation

1. Clone the repository

```bash
git clone https://github.com/yourusername/andrometa-backend.git
cd andrometa-backend
```

2. Install dependencies

```bash
npm install
```

3. Create a `.env` file in the root directory with the following variables:

```
# Server
PORT=3000

# Database
DATABASE_URL="postgresql://username:password@localhost:5432/andrometa"

# Authentication
JWT_SECRET="your-jwt-secret"
ADMIN_SECRET_PASS="your-admin-secret"

# AWS
S3_ACCESS_KEY="your-aws-access-key"
S3_SECRET_KEY="your-aws-secret-key"
OUTKAST_S3_BUCKET="your-s3-bucket-name"
MNNT_BUCKET="your-mnnt-bucket-name"

# Stripe
STRIPE_SECRET_KEY="your-stripe-secret-key"
STRIPE_ENDPOINT_SECRET="your-stripe-endpoint-secret"

# Email
AWS_SES_ACCESS_KEY_ID="your-ses-access-key"
AWS_SES_SECRET_ACCESS_KEY="your-ses-secret-key"
EMAIL="no-reply@yourdomain.com"

# Social Auth
DISCORD_TOKEN="your-discord-token"
GOOGLE_API_KEY="your-google-api-key"

# Third-party Integrations
OPENAI_API_KEY="your-openai-api-key"
```

4. Initialize the database

```bash
npx prisma migrate dev
```

5. Start the development server

```bash
npm run dev
```

6. The server will be available at [http://localhost:3000](http://localhost:3000)

## 🔍 Key Components

### Server Architecture

The backend is organized into several modules:

-   **Auth**: User registration, login, and session management
-   **Collections**: NFT collection and token management
-   **Inventory**: Player item storage and manipulation
-   **Missions**: Time-based activities for players
-   **Marketplace**: Trading platform for digital assets
-   **Leaderboard**: Competitive player rankings
-   **Challenges**: Player vs player competitions
-   **Quests**: Achievement tracking across platforms
-   **Store**: In-app purchase verification and fulfillment
-   **Support**: User assistance and loyalty features
-   **Notification**: Real-time alerts and updates

### WebSockets

Real-time communication is implemented for:

-   Chat functionality
-   Live stream interaction
-   Notification delivery
-   Game state updates

### Security Features

-   JWT authentication
-   Server-side validation
-   Signature verification for sensitive operations
-   Rate limiting
-   Encryption for sensitive data

## 📝 API Documentation

For detailed API documentation, see [API-Docs.md](API-Docs.md).

## 🚢 Deployment

### Deploying to AWS with Copilot

[AWS Copilot](https://aws.github.io/copilot-cli/) is a tool for developers to build, release, and operate containerized applications on AWS. It simplifies the process of deploying and managing applications on AWS ECS and AWS Fargate.

#### Prerequisites

-   [AWS Copilot CLI](https://aws.github.io/copilot-cli/docs/getting-started/install/) installed
-   AWS credentials configured
-   Docker installed
-   Your application containerized (Dockerfile in root directory)

#### Setup

1. Initialize your application with Copilot

```bash
copilot app init andrometa-backend
```

2. Add a service (e.g., API service)

```bash
copilot svc init --name api --svc-type "Load Balanced Web Service" --dockerfile ./Dockerfile
```

3. Set up your environments (e.g., test, staging, production)

```bash
copilot env init --name test --profile default --app andrometa-backend
copilot env init --name production --profile default --app andrometa-backend
```

#### Configuration

Create or modify the `copilot/api/manifest.yml` file to configure your service:

```yaml
name: api
type: Load Balanced Web Service

image:
    build: Dockerfile
    port: 3000

http:
    path: "/"
    healthcheck:
        path: "/health"
        healthy_threshold: 3
        unhealthy_threshold: 5
        interval: 10s
        timeout: 5s

cpu: 256
memory: 512
count: 2
exec: true

variables:
    NODE_ENV: production

secrets:
    JWT_SECRET: /andrometa/secrets/jwt-secret
    DATABASE_URL: /andrometa/secrets/database-url
    STRIPE_SECRET_KEY: /andrometa/secrets/stripe-secret-key
    # Add all other secrets from your .env file

scaling:
    min: 2
    max: 10
    cpu_percentage: 70
```

#### CI/CD Pipeline Setup

1. Create a pipeline for your application

```bash
copilot pipeline init
```

2. This will create a `copilot/pipeline.yml` file. Modify it to fit your workflow:

```yaml
name: andrometa-pipeline
version: 1

source:
    provider: GitHub
    properties:
        branch: main
        repository: https://github.com/yourusername/andrometa-backend
        connection_name: my-github-connection

stages:
    - name: test
      test_commands:
          - npm run test
    - name: production
      requires_approval: true

automated:
    triggers:
        - branch: main
    test_commands:
        - make test
```

3. Deploy your pipeline

```bash
copilot pipeline update
```

#### Deployment

Deploy to your test environment:

```bash
copilot svc deploy --name api --env test
```

Once verified, deploy to production:

```bash
copilot svc deploy --name api --env production
```

### Additional AWS Resources

For the complete Andrometa backend deployment, you'll also need to set up:

1. **Amazon RDS for PostgreSQL** - For your database
2. **Amazon S3** - For storing NFT images and other assets
3. **Amazon CloudFront** - For content delivery
4. **AWS Lambda** - For serverless functions like NFT generation
5. **Amazon ElastiCache** - For caching frequently accessed data
6. **Amazon SES** - For sending emails

These resources can be managed through the AWS Console
