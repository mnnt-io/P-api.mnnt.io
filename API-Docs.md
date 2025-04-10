# Andrometa Backend API Documentation

This document outlines the API endpoints and interactions provided by the Andrometa Backend Server for both web and mobile clients.

## Base URL

All API requests should be made to:

```
https://api.mnnt.io
```

## Authentication

Most API endpoints require authentication using JWT:

1. Obtain a JWT token via the login endpoint
2. Include the token in the Authorization header of subsequent requests:
    ```
    Authorization: <token>
    ```

## Endpoints

### Authentication Endpoints

#### Register User

```
POST /auth/register
```

Creates a new user account.

**Request Body:**

```json
{
	"username": "username",
	"email": "user@example.com",
	"password": "securePassword1!",
	"referrer": "referrer_username", // optional
	"country": "US", // optional
	"immutable_address": "0x..." // optional
}
```

**Response:**

```json
{
	"message": "User created successfully."
}
```

#### Login

```
POST /auth/login
```

Authenticates a user and returns a JWT token.

**Request Body:**

```json
{
	"login": "username_or_email",
	"password": "securePassword1!"
}
```

**Response:**

```json
{
	"message": "Successfully logged in as username.",
	"token": "jwt_token_here",
	"user": {
		"id": "user_id",
		"username": "username",
		"email": "user@example.com",
		"address": "0x...",
		"quest_points": 100
	}
}
```

#### Social Logins

```
POST /auth/google/login
POST /auth/google/register
POST /auth/apple/login
POST /auth/apple/register
POST /auth/passport/registerAndLogin
```

Various social login methods with similar request/response patterns to standard login.

#### Forgot Password

```
POST /auth/forgotpassword
```

Initiates password reset process for a user.

**Request Body:**

```json
{
	"email": "user@example.com"
}
```

**Response:**

```json
{
	"message": "Successfully sent forgot password email."
}
```

#### Reset Password

```
POST /auth/resetpassword
```

Completes the password reset process.

**Request Body:**

```json
{
	"token": "reset_token_from_email",
	"password": "newSecurePassword1!"
}
```

**Response:**

```json
{
	"message": "Successfully reset password."
}
```

#### Fetch User

```
GET /auth/user
```

Retrieves the authenticated user's profile.

**Response:**

```json
{
	"id": "user_id",
	"username": "username",
	"email": "user@example.com",
	"address": "0x...",
	"quest_points": 100,
	"profile_photo": "https://url/to/photo.jpg"
}
```

### Inventory Endpoints

#### Fetch Inventory

```
GET /inventory/v2
```

Retrieves the authenticated user's inventory.

**Response:**

```json
{
	"loadout": [
		{
			"data": "item_id",
			"quantity": 1,
			"enchantments": [1, 2, 3]
		}
	],
	"inventory": [
		{
			"data": "item_id",
			"quantity": 5,
			"enchantments": []
		}
	]
}
```

#### Fetch User Inventory

```
GET /inventory/:username
```

Retrieves a specific user's inventory.

**Response:**

```json
{
  "loadout": [...],
  "inventory": [...]
}
```

#### Get Currencies

```
GET /inventory/currency/:username
```

Retrieves a user's currency balances.

**Response:**

```json
{
	"message": "1000,500",
	"hasCapsulePass": true
}
```

#### Update Inventory

```
POST /inventory/update
```

Updates a user's inventory with changes.

**Request Body:**

```json
{
	"data": "encrypted_data",
	"sec": "encrypted_security_data"
}
```

**Response:**

```json
{
	"message": "Inventory processed successfully for username."
}
```

### Leaderboard Endpoints

#### Fetch Leaderboard

```
GET /leaderboard
```

Retrieves the global leaderboard.

**Query Parameters:**

-   `address`: Filter by username
-   `score`: Filter by minimum score
-   `take`: Number of entries to return
-   `skip`: Number of entries to skip

**Response:**

```json
[
	{
		"username": "top_player",
		"score": 10000,
		"enemies_killed": 500,
		"damage_dealt": 50000,
		"avg_time_per_wave": 45,
		"computed_score": 12500,
		"platform": "Windows"
	}
]
```

#### Fetch User Entry

```
GET /leaderboard/user?address=username
```

Retrieves a specific user's leaderboard entry with ranking.

**Response:**

```json
[
	{
		"username": "username",
		"score": 8000,
		"enemies_killed": 400,
		"damage_dealt": 40000,
		"avg_time_per_wave": 50,
		"computed_score": 9500,
		"position": 5
	}
]
```

#### Add Leaderboard Entry

```
POST /leaderboard/add
```

Adds or updates a leaderboard entry for a user.

**Request Body:**

```json
{
	"data": "encrypted_data",
	"sec": "encrypted_security_data"
}
```

**Decrypted Data Structure:**

```json
{
	"id": "username",
	"score": 8500,
	"enemies_killed": 425,
	"damage_dealt": 42500,
	"avg_time_per_wave": 48,
	"attempts": 12,
	"tag": "Windows"
}
```

**Response:**

```json
{
	"message": "Leaderboard entry for user username successfully updated.",
	"score": 10200
}
```

### Challenge Endpoints

#### Fetch Challenges

```
GET /challenger
```

Retrieves challenges data.

**Response:**

```json
[
	{
		"username": "player1",
		"cumulative_andros": 5000
	}
]
```

#### Fetch User Challenges

```
GET /challenger/user?user=username
```

Retrieves a user's active challenges.

**Response:**

```json
[
	{
		"id": "challenge_id",
		"amount": 100,
		"isCancellable": true
	}
]
```

#### Add Challenge

```
POST /challenger/add
```

Creates or accepts a challenge.

**Request Body:**

```json
{
	"data": "encrypted_data",
	"sec": "encrypted_security_data"
}
```

**Decrypted Data Structure:**

```json
{
	"id": "username",
	"score": 5000,
	"enemies_killed": 250,
	"damage_dealt": 25000,
	"avg_time_per_wave": 40,
	"overtime": 0,
	"andros": 100,
	"tag": "Windows"
}
```

**Response:**

```json
{
	"success": true,
	"type": 1,
	"message": "Challenge accepted successfully",
	"score": 6000
}
```

### Collection Endpoints

#### Fetch Collections

```
GET /collections
```

Retrieves all available collections.

**Response:**

```json
[
	{
		"name": "Outkasts",
		"ticker": "OK",
		"slug": "outkasts",
		"description": "Collection description",
		"banner": "https://url/to/banner.jpg",
		"logo": "https://url/to/logo.jpg",
		"address": "0x...",
		"creator": {
			"name": "Creator Name"
		}
	}
]
```

#### Fetch Tokens by Owner

```
GET /collections/owned/:owner_address
```

Retrieves tokens owned by a specific address.

**Response:**

```json
[
	{
		"token_id": 1,
		"name": "Token #1",
		"collection_address": "0x...",
		"owner_address": "0x...",
		"metadata": {
			"trait1": "value1",
			"trait2": "value2"
		}
	}
]
```

#### Fetch Collection Tokens

```
GET /collections/:collection_slug/tokens
```

Retrieves tokens in a specific collection.

**Response:**

```json
[
  {
    "token_id": 1,
    "name": "Token #1",
    "owner_address": "0x...",
    "metadata": {...}
  }
]
```

### Missions Endpoints

#### Fetch Collection Missions

```
GET /missions/:collection_slug
```

Retrieves missions for a specific collection.

**Response:**

```json
[
  {
    "id": "mission_id",
    "name": "Mission Name",
    "description": "Mission description",
    "start": 1650000000,
    "end": 1660000000,
    "reward_amount": 1000,
    "xp_rewards": 500,
    "boosted_parts": {...}
  }
]
```

#### Fetch Mission Details

```
GET /missions/:collection_slug/:mission_id?eth_address=0x...
```

Retrieves details for a specific mission.

**Response:**

```json
{
  "id": "mission_id",
  "name": "Mission Name",
  "description": "Mission description",
  "start": 1650000000,
  "end": 1660000000,
  "reward_amount": 1000,
  "xp_rewards": 500,
  "boosted_parts": {...},
  "summaries": [
    {
      "user_address": "0x...",
      "deployed_power": 150,
      "paid_tokens": 500
    }
  ]
}
```

#### Dispatch to Mission

```
POST /missions/:collection_slug/:mission_id/dispatch
```

Dispatches tokens to a mission.

**Request Body:**

```json
{
	"deployed_tokens": [1, 2, 3],
	"eth_address": "0x...",
	"signature": "signed_message"
}
```

**Response:**

```json
{
	"message": "Successfully dispatched Outkasts #1,2,3 with a total power of 250"
}
```

#### Claim Mission Rewards

```
POST /missions/:collection_slug/:mission_id/claim
```

Claims rewards from a completed mission.

**Request Body:**

```json
{
	"eth_address": "0x...",
	"signature": "signed_message"
}
```

**Response:**

```json
{
	"message": "Your outkasts have successfully returned from their expedition and found 1000 shards."
}
```

### Marketplace Endpoints

#### Fetch Listings

```
GET /marketplace/listings
```

Retrieves all active marketplace listings.

**Response:**

```json
[
	{
		"id": "listing_id",
		"collection_address": "0x...",
		"token_id": 1,
		"cost": 1000000000000000000,
		"currency_address": "0x...",
		"from_address": "0x...",
		"creation": "2023-04-10T12:00:00Z",
		"expiration": "2023-05-10T12:00:00Z",
		"status": "Pending"
	}
]
```

#### Create Listing

```
POST /marketplace/listings/create
```

Creates a new marketplace listing.

**Request Body:**

```json
{
	"collection_address": "0x...",
	"token_id": 1,
	"currency_address": "0x...",
	"cost": 1000,
	"expiration": 1680000000,
	"eth_address": "0x...",
	"signature": "signed_message"
}
```

**Response:**

```json
{
	"message": "Listing created successfully."
}
```

#### Accept Listing

```
POST /marketplace/listings/:listing_id
```

Purchases a listed item.

**Request Body:**

```json
{
	"eth_address": "0x...",
	"signature": "signed_message"
}
```

**Response:**

```json
{
	"message": "Successfully accepted listing."
}
```

### Store Endpoints

#### Fetch Store Items

```
GET /store?username=username
```

Retrieves available store items for a user.

**Response:**

```json
[
	{
		"results": [
			"p_gem_pack_100",
			"p_shards_pack_500-Purchasable",
			"daily_reward-Claimable"
		]
	}
]
```

#### Verify Transaction

```
POST /store/transaction
```

Verifies and processes a store purchase.

**Request Body:**

```json
{
	"data": "encrypted_data",
	"sec": "encrypted_security_data"
}
```

**Decrypted Data Structure:**

```json
{
	"username": "username",
	"productId": "p_gem_pack_100",
	"transactionId": "store_transaction_id",
	"tag": "Android"
}
```

**Response:**

```json
{
	"message": "511:0-4112,100;510:0-0,50;5000"
}
```

### Quest Endpoints

#### Fetch Quests

```
GET /quests
```

Retrieves available quests for the authenticated user.

**Response:**

```json
[
	{
		"id": "quest_id",
		"name": "Quest Name",
		"reward": 50,
		"start": "2023-04-01T00:00:00Z",
		"end": "2023-05-01T00:00:00Z",
		"mission": {
			"platform": "Discord",
			"type": "Follow"
		},
		"completed": []
	}
]
```

#### Verify Quest Completion

```
POST /quests/:quest_id/verify
```

Verifies and completes a quest.

**Response:**

```json
{
	"message": "Successfully verified Quest"
}
```

### Notification Endpoints

#### Fetch Notifications

```
GET /notify
```

Retrieves pending notifications for the authenticated user.

**Response:**

```json
[
	{
		"message": "New quest available!",
		"type": "TASK"
	}
]
```

### Support Endpoints

#### Fetch Loyalty

```
GET /support
```

Retrieves user's loyalty choice.

**Response:**

```json
{
	"message": "Andrometa,TeamA,TeamB"
}
```

#### Update Loyalty

```
POST /support/update
```

Updates user's loyalty choice.

**Request Body:**

```json
{
	"choice": "TeamA"
}
```

**Response:**

```json
{
	"message": "Successfully updated loyalty choice."
}
```

### Bug Reporting Endpoints

#### Submit Bug Report

```
POST /bugs
```

Submits a bug report.

**Request Body:**
Form data with:

-   `title`: Bug title
-   `description`: Bug description
-   Files: Screenshots/attachments

**Response:**

```json
{
	"message": "Successfully submitted the bug."
}
```

## Encryption

Several endpoints require encrypted data for security. The encryption process:

1. Generate AES-256 key and IV
2. Encrypt data using AES-256-CBC
3. Encrypt the AES key and IV using the server's RSA public key
4. Send the encrypted data, encrypted AES key, and encrypted IV

## Error Handling

API errors are returned with appropriate HTTP status codes:

-   `400` - Bad Request: Invalid input
-   `401` - Unauthorized: Authentication required
-   `402` - Unauthenticated: Invalid credentials
-   `403` - Forbidden: Insufficient permissions
-   `404` - Not Found: Resource not found
-   `500` - Internal Server Error: Unexpected server issue

Error response format:

```json
{
	"name": "ErrorType",
	"message": "Detailed error message"
}
```

Common error types:

-   `NotFoundMNNTError`
-   `UnauthorizedMNNTError`
-   `UnauthenticatedMNNTError`
