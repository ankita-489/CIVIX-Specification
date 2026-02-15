# System Design Document: CIVIX Platform

## Document Information
- **Version**: 1.0
- **Last Updated**: February 15, 2026
- **Status**: Final Design
- **Author**: CIVIX Development Team

---

## Table of Contents
1. [High-Level Architecture](#1-high-level-architecture)
2. [System Architecture Diagram Explanation](#2-system-architecture-diagram-explanation)
3. [Component Design](#3-component-design)
4. [Database Schema](#4-database-schema)
5. [API Endpoints](#5-api-endpoints)
6. [Authentication & Authorization Flow](#6-authentication--authorization-flow)
7. [Issue Reporting Flow](#7-issue-reporting-flow)
8. [Admin Workflow](#8-admin-workflow)
9. [Deployment Strategy](#9-deployment-strategy)
10. [Security Considerations](#10-security-considerations)
11. [Scalability Plan](#11-scalability-plan)
12. [Risk Mitigation](#12-risk-mitigation)

---

## 1. High-Level Architecture

### 1.1 Architecture Overview

CIVIX follows a modern three-tier architecture pattern with microservices principles:

**Presentation Layer (Frontend)**
- React.js single-page application (SPA)
- Responsive design for mobile and desktop
- Real-time updates via Socket.io client
- Mapbox integration for geospatial visualization

**Application Layer (Backend)**
- Node.js + Express.js REST API server
- JWT-based authentication middleware
- Socket.io server for real-time notifications
- Business logic and validation layer

**Data Layer (Database & Storage)**
- MongoDB for primary data storage
- Cloudinary for image storage and CDN
- Optional blockchain integration for badge verification

**External Services**
- Mapbox API for mapping and geocoding
- Cloudinary for image processing
- Blockchain network for badge tokens (optional)
- Email service (SendGrid/AWS SES) for notifications

### 1.2 Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | React.js 18+ | UI framework |
| State Management | Redux Toolkit | Global state management |
| Routing | React Router v6 | Client-side routing |
| UI Components | Material-UI / Tailwind CSS | Component library |
| Maps | Mapbox GL JS | Interactive mapping |
| Real-time | Socket.io Client | WebSocket communication |
| HTTP Client | Axios | API requests |
| Backend | Node.js 18+ LTS | Runtime environment |
| Framework | Express.js 4.x | Web framework |
| Authentication | JWT + bcrypt | Token-based auth |
| Real-time | Socket.io Server | WebSocket server |
| Validation | Joi / Express-validator | Input validation |
| Database | MongoDB 6.x | NoSQL database |
| ODM | Mongoose | MongoDB object modeling |
| Image Storage | Cloudinary | Cloud image storage |
| Blockchain | Ethereum/Polygon | Badge smart contracts |
| Email | SendGrid / AWS SES | Email notifications |

### 1.3 Design Principles

1. **Separation of Concerns**: Clear boundaries between frontend, backend, and data layers
2. **Stateless API**: RESTful API design with JWT for authentication
3. **Real-time Updates**: WebSocket integration for instant notifications
4. **Scalability**: Horizontal scaling capability for all components
5. **Security First**: Defense in depth with multiple security layers
6. **Mobile First**: Responsive design prioritizing mobile experience
7. **Performance**: Optimized queries, caching, and CDN usage
8. **Maintainability**: Modular code structure with clear documentation

---

## 2. System Architecture Diagram Explanation

### 2.1 Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │           React.js Web Application (SPA)                  │  │
│  │                                                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │  │
│  │  │  Issue   │  │   Map    │  │  Admin   │  │  Profile │ │  │
│  │  │  Report  │  │   View   │  │Dashboard │  │   Page   │ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │  │
│  │                                                            │  │
│  │  Redux Store │ Socket.io Client │ Axios HTTP Client      │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                   │
└───────────────────────────┬───────────────────────────────────┘
                            │ HTTPS / WSS
                            │
┌───────────────────────────┴───────────────────────────────────┐
│                      APPLICATION LAYER                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │         Node.js + Express.js API Server                   │  │
│  │                                                            │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Middleware Layer                                   │  │  │
│  │  │  • CORS │ Helmet │ Rate Limiter │ Body Parser     │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                            │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Authentication Middleware (JWT Verification)       │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │                                                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │  │
│  │  │  Auth    │  │  Issue   │  │  User    │  │  Badge   │ │  │
│  │  │  Routes  │  │  Routes  │  │  Routes  │  │  Routes  │ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │  │
│  │                                                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │  │
│  │  │  Auth    │  │  Issue   │  │  User    │  │  Badge   │ │  │
│  │  │Controller│  │Controller│  │Controller│  │Controller│ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │  │
│  │                                                            │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │  │
│  │  │  Auth    │  │  Issue   │  │  User    │  │  Badge   │ │  │
│  │  │ Service  │  │ Service  │  │ Service  │  │ Service  │ │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │  │
│  │                                                            │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │         Socket.io Server (Real-time Events)               │  │
│  │  • Issue Status Updates │ New Comments │ Notifications    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                   │
└───────────────────────────┬───────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
┌───────┴────────┐  ┌───────┴────────┐  ┌──────┴──────┐
│   DATA LAYER   │  │ EXTERNAL APIs  │  │  BLOCKCHAIN │
├────────────────┤  ├────────────────┤  ├─────────────┤
│                │  │                │  │             │
│  ┌──────────┐ │  │  ┌──────────┐ │  │ ┌─────────┐ │
│  │ MongoDB  │ │  │  │  Mapbox  │ │  │ │ Smart   │ │
│  │          │ │  │  │   API    │ │  │ │Contract │ │
│  │ • Users  │ │  │  │          │ │  │ │ (Badge) │ │
│  │ • Issues │ │  │  │ • Maps   │ │  │ └─────────┘ │
│  │ • Comments│ │  │  │ • Geocode│ │  │             │
│  │ • Votes  │ │  │  └──────────┘ │  │ Ethereum/   │
│  │ • Badges │ │  │                │  │ Polygon     │
│  └──────────┘ │  │  ┌──────────┐ │  │ Network     │
│                │  │  │Cloudinary│ │  │             │
│  Mongoose ODM  │  │  │          │ │  └─────────────┘
│                │  │  │ • Upload │ │
└────────────────┘  │  │ • Store  │ │
                    │  │ • CDN    │ │
                    │  └──────────┘ │
                    │                │
                    │  ┌──────────┐ │
                    │  │ SendGrid │ │
                    │  │   /SES   │ │
                    │  │          │ │
                    │  │ • Email  │ │
                    │  └──────────┘ │
                    └────────────────┘
```

### 2.2 Component Interactions

**User Issue Reporting Flow:**
1. User submits issue via React frontend
2. Frontend sends HTTP POST to Express API
3. API validates data and authenticates user (JWT)
4. Issue service processes data and stores in MongoDB
5. Image uploaded to Cloudinary
6. Location geocoded via Mapbox API
7. Socket.io broadcasts new issue to connected clients
8. Frontend updates map and issue list in real-time

**Real-time Notification Flow:**
1. Admin updates issue status in dashboard
2. Backend updates MongoDB document
3. Socket.io server emits status change event
4. Connected clients receive event
5. Frontend updates UI and shows notification
6. Email service sends notification (async)

**Badge Award Flow:**
1. User action triggers badge criteria check
2. Badge service validates achievement
3. Badge record created in MongoDB
4. Smart contract transaction initiated (optional)
5. Blockchain confirms transaction
6. User profile updated with new badge
7. Notification sent to user

---

## 3. Component Design

### 3.1 Frontend Components

#### 3.1.1 Core Components

**App Component**
- Root component managing routing
- Global state provider (Redux)
- Socket.io connection initialization
- Authentication state management

**Authentication Components**
- `Login.jsx`: User login form
- `Register.jsx`: User registration form
- `ForgotPassword.jsx`: Password reset request
- `ResetPassword.jsx`: Password reset form
- `PrivateRoute.jsx`: Protected route wrapper

**Issue Components**
- `IssueForm.jsx`: Issue creation form with image upload
- `IssueList.jsx`: Paginated list of issues
- `IssueCard.jsx`: Individual issue preview card
- `IssueDetail.jsx`: Full issue details view
- `IssueMap.jsx`: Interactive map with issue markers
- `IssueFilter.jsx`: Filter and search controls

**Community Components**
- `VoteButton.jsx`: Upvote functionality
- `CommentSection.jsx`: Comment list and form
- `CommentItem.jsx`: Individual comment display

**Admin Components**
- `AdminDashboard.jsx`: Overview with metrics
- `IssueManagement.jsx`: Issue list with admin actions
- `IssueStatusUpdate.jsx`: Status change form
- `Analytics.jsx`: Charts and reports
- `DepartmentAssignment.jsx`: Issue assignment interface

**User Components**
- `UserProfile.jsx`: User profile display
- `UserSettings.jsx`: Account settings
- `BadgeDisplay.jsx`: Badge collection showcase
- `NotificationCenter.jsx`: Notification list

#### 3.1.2 Shared Components

- `Header.jsx`: Navigation bar
- `Footer.jsx`: Footer with links
- `Sidebar.jsx`: Side navigation (admin)
- `LoadingSpinner.jsx`: Loading indicator
- `ErrorBoundary.jsx`: Error handling wrapper
- `Toast.jsx`: Notification toast messages
- `Modal.jsx`: Reusable modal dialog
- `ImageUploader.jsx`: Image upload with preview
- `LocationPicker.jsx`: Map-based location selector

#### 3.1.3 State Management (Redux)

**Store Structure:**
```javascript
{
  auth: {
    user: {},
    token: '',
    isAuthenticated: false,
    loading: false
  },
  issues: {
    items: [],
    currentIssue: {},
    filters: {},
    pagination: {},
    loading: false
  },
  notifications: {
    items: [],
    unreadCount: 0
  },
  badges: {
    userBadges: [],
    progress: {}
  },
  ui: {
    sidebarOpen: false,
    modalOpen: false
  }
}
```

**Redux Slices:**
- `authSlice`: Authentication state and actions
- `issueSlice`: Issue data and CRUD operations
- `notificationSlice`: Notification management
- `badgeSlice`: Badge data and progress
- `uiSlice`: UI state management

### 3.2 Backend Components

#### 3.2.1 Server Structure

```
src/
├── config/
│   ├── database.js          # MongoDB connection
│   ├── cloudinary.js        # Cloudinary config
│   ├── blockchain.js        # Blockchain config
│   └── email.js             # Email service config
├── middleware/
│   ├── auth.js              # JWT authentication
│   ├── authorize.js         # Role-based authorization
│   ├── validate.js          # Input validation
│   ├── errorHandler.js      # Global error handler
│   ├── rateLimiter.js       # Rate limiting
│   └── upload.js            # File upload handling
├── models/
│   ├── User.js              # User schema
│   ├── Issue.js             # Issue schema
│   ├── Comment.js           # Comment schema
│   ├── Vote.js              # Vote schema
│   ├── Badge.js             # Badge schema
│   └── Notification.js      # Notification schema
├── routes/
│   ├── auth.js              # Authentication routes
│   ├── issues.js            # Issue CRUD routes
│   ├── users.js             # User profile routes
│   ├── comments.js          # Comment routes
│   ├── votes.js             # Vote routes
│   ├── badges.js            # Badge routes
│   └── admin.js             # Admin routes
├── controllers/
│   ├── authController.js    # Auth logic
│   ├── issueController.js   # Issue logic
│   ├── userController.js    # User logic
│   ├── commentController.js # Comment logic
│   ├── voteController.js    # Vote logic
│   ├── badgeController.js   # Badge logic
│   └── adminController.js   # Admin logic
├── services/
│   ├── authService.js       # Auth business logic
│   ├── issueService.js      # Issue business logic
│   ├── imageService.js      # Image processing
│   ├── geocodeService.js    # Geocoding
│   ├── emailService.js      # Email sending
│   ├── badgeService.js      # Badge management
│   └── notificationService.js # Notification logic
├── utils/
│   ├── validators.js        # Custom validators
│   ├── helpers.js           # Helper functions
│   └── constants.js         # App constants
├── sockets/
│   └── socketHandler.js     # Socket.io events
├── app.js                   # Express app setup
└── server.js                # Server entry point
```

#### 3.2.2 Middleware Pipeline

```javascript
// Request flow through middleware
app.use(helmet());              // Security headers
app.use(cors());                // CORS policy
app.use(express.json());        // Body parser
app.use(rateLimiter);           // Rate limiting
app.use('/api/auth', authRoutes);
app.use('/api/issues', authenticate, issueRoutes);
app.use('/api/admin', authenticate, authorize('admin'), adminRoutes);
app.use(errorHandler);          // Error handling
```

### 3.3 Database Layer

#### 3.3.1 Mongoose Models

**User Model**
- Schema definition with validation
- Password hashing pre-save hook
- JWT token generation method
- Password comparison method
- Virtual fields for computed properties

**Issue Model**
- Geospatial indexing for location queries
- Status enum validation
- Timestamps for audit trail
- Virtual populate for comments and votes
- Pre-save hooks for data normalization

**Comment Model**
- Reference to user and issue
- Edit history tracking
- Soft delete capability

**Vote Model**
- Compound unique index (user + issue)
- Prevents duplicate votes

**Badge Model**
- Badge type enum
- Blockchain transaction reference
- Achievement criteria metadata

### 3.4 External Service Integration

#### 3.4.1 Cloudinary Service
```javascript
// Image upload and transformation
- Upload images with auto-optimization
- Generate thumbnails
- Apply watermarks (optional)
- Return CDN URLs
- Delete images when issues removed
```

#### 3.4.2 Mapbox Service
```javascript
// Geocoding and mapping
- Forward geocoding (address → coordinates)
- Reverse geocoding (coordinates → address)
- Map tile serving
- Route optimization (future)
```

#### 3.4.3 Blockchain Service
```javascript
// Badge smart contract interaction
- Mint badge tokens
- Verify badge ownership
- Query badge metadata
- Handle transaction failures
```

#### 3.4.4 Email Service
```javascript
// Transactional emails
- Welcome emails
- Email verification
- Password reset
- Issue status updates
- Weekly digest (optional)
```

---

## 4. Database Schema

### 4.1 MongoDB Collections

#### 4.1.1 Users Collection

```javascript
{
  _id: ObjectId,
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true
  },
  password: {
    type: String,
    required: true,
    minlength: 8
  },
  name: {
    type: String,
    trim: true
  },
  phone: String,
  address: String,
  role: {
    type: String,
    enum: ['citizen', 'authority', 'admin'],
    default: 'citizen'
  },
  isVerified: {
    type: Boolean,
    default: false
  },
  verificationToken: String,
  resetPasswordToken: String,
  resetPasswordExpire: Date,
  profileImage: String,
  preferences: {
    emailNotifications: { type: Boolean, default: true },
    anonymousReporting: { type: Boolean, default: false }
  },
  stats: {
    issuesReported: { type: Number, default: 0 },
    issuesVoted: { type: Number, default: 0 },
    commentsPosted: { type: Number, default: 0 }
  },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}

// Indexes
db.users.createIndex({ email: 1 }, { unique: true })
db.users.createIndex({ role: 1 })
```

#### 4.1.2 Issues Collection

```javascript
{
  _id: ObjectId,
  trackingId: {
    type: String,
    unique: true,
    required: true
  },
  title: {
    type: String,
    required: true,
    trim: true,
    maxlength: 200
  },
  description: {
    type: String,
    required: true,
    maxlength: 2000
  },
  category: {
    type: String,
    required: true,
    enum: ['pothole', 'streetlight', 'water_leak', 'garbage', 'damaged_property', 'other']
  },
  severity: {
    type: String,
    enum: ['low', 'medium', 'high'],
    default: 'medium'
  },
  status: {
    type: String,
    enum: ['pending', 'in_progress', 'resolved', 'rejected'],
    default: 'pending'
  },
  location: {
    type: {
      type: String,
      enum: ['Point'],
      required: true
    },
    coordinates: {
      type: [Number],  // [longitude, latitude]
      required: true
    }
  },
  address: {
    street: String,
    city: String,
    state: String,
    zipCode: String,
    country: String,
    formatted: String
  },
  images: [{
    url: String,
    publicId: String,
    thumbnail: String,
    uploadedAt: Date
  }],
  reporter: {
    type: ObjectId,
    ref: 'User',
    required: true
  },
  isAnonymous: {
    type: Boolean,
    default: false
  },
  voteCount: {
    type: Number,
    default: 0
  },
  commentCount: {
    type: Number,
    default: 0
  },
  assignedTo: {
    department: String,
    assignee: { type: ObjectId, ref: 'User' }
  },
  statusHistory: [{
    status: String,
    updatedBy: { type: ObjectId, ref: 'User' },
    notes: String,
    timestamp: { type: Date, default: Date.now }
  }],
  resolutionNotes: String,
  resolutionImages: [{
    url: String,
    publicId: String
  }],
  resolvedAt: Date,
  rejectionReason: String,
  internalNotes: String,  // Only visible to authorities
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}

// Indexes
db.issues.createIndex({ location: '2dsphere' })  // Geospatial queries
db.issues.createIndex({ trackingId: 1 }, { unique: true })
db.issues.createIndex({ status: 1, createdAt: -1 })
db.issues.createIndex({ category: 1, status: 1 })
db.issues.createIndex({ reporter: 1, createdAt: -1 })
db.issues.createIndex({ voteCount: -1 })
```

#### 4.1.3 Comments Collection

```javascript
{
  _id: ObjectId,
  issue: {
    type: ObjectId,
    ref: 'Issue',
    required: true
  },
  user: {
    type: ObjectId,
    ref: 'User',
    required: true
  },
  text: {
    type: String,
    required: true,
    maxlength: 500
  },
  isEdited: {
    type: Boolean,
    default: false
  },
  editHistory: [{
    text: String,
    editedAt: Date
  }],
  isDeleted: {
    type: Boolean,
    default: false
  },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}

// Indexes
db.comments.createIndex({ issue: 1, createdAt: 1 })
db.comments.createIndex({ user: 1 })
```

#### 4.1.4 Votes Collection

```javascript
{
  _id: ObjectId,
  issue: {
    type: ObjectId,
    ref: 'Issue',
    required: true
  },
  user: {
    type: ObjectId,
    ref: 'User',
    required: true
  },
  createdAt: { type: Date, default: Date.now }
}

// Indexes
db.votes.createIndex({ issue: 1, user: 1 }, { unique: true })  // Prevent duplicate votes
db.votes.createIndex({ user: 1 })
```

#### 4.1.5 Badges Collection

```javascript
{
  _id: ObjectId,
  user: {
    type: ObjectId,
    ref: 'User',
    required: true
  },
  badgeType: {
    type: String,
    required: true,
    enum: [
      'first_report',
      'active_reporter',
      'community_champion',
      'engaged_citizen',
      'conversationalist',
      'problem_solver'
    ]
  },
  name: String,
  description: String,
  icon: String,
  criteria: {
    type: String,
    description: String
  },
  blockchainTxHash: String,
  blockchainNetwork: String,
  tokenId: String,
  metadata: {
    issuesReported: Number,
    issuesVoted: Number,
    commentsPosted: Number,
    issuesResolved: Number
  },
  earnedAt: { type: Date, default: Date.now }
}

// Indexes
db.badges.createIndex({ user: 1, badgeType: 1 }, { unique: true })
db.badges.createIndex({ user: 1, earnedAt: -1 })
```

#### 4.1.6 Notifications Collection

```javascript
{
  _id: ObjectId,
  recipient: {
    type: ObjectId,
    ref: 'User',
    required: true
  },
  type: {
    type: String,
    required: true,
    enum: [
      'issue_status_change',
      'new_comment',
      'issue_voted',
      'badge_earned',
      'issue_assigned',
      'authority_update'
    ]
  },
  title: String,
  message: String,
  relatedIssue: {
    type: ObjectId,
    ref: 'Issue'
  },
  relatedComment: {
    type: ObjectId,
    ref: 'Comment'
  },
  relatedBadge: {
    type: ObjectId,
    ref: 'Badge'
  },
  isRead: {
    type: Boolean,
    default: false
  },
  readAt: Date,
  createdAt: { type: Date, default: Date.now }
}

// Indexes
db.notifications.createIndex({ recipient: 1, isRead: 1, createdAt: -1 })
db.notifications.createIndex({ createdAt: 1 }, { expireAfterSeconds: 2592000 })  // 30 days TTL
```

### 4.2 Relationships

```
User (1) ──── (N) Issue
User (1) ──── (N) Comment
User (1) ──── (N) Vote
User (1) ──── (N) Badge
User (1) ──── (N) Notification

Issue (1) ──── (N) Comment
Issue (1) ──── (N) Vote
Issue (1) ──── (N) Notification
```

### 4.3 Data Validation Rules

1. **Email Validation**: RFC 5322 compliant email format
2. **Password**: Minimum 8 characters, hashed with bcrypt (10 rounds)
3. **Coordinates**: Valid longitude (-180 to 180) and latitude (-90 to 90)
4. **Image Size**: Maximum 5MB per image
5. **Text Length**: Title (200 chars), Description (2000 chars), Comment (500 chars)
6. **Tracking ID**: Auto-generated unique alphanumeric (e.g., CIV-2026-001234)
7. **Status Transitions**: Enforced state machine (pending → in_progress → resolved/rejected)

---

## 5. API Endpoints

### 5.1 Authentication Endpoints

#### POST /api/auth/register
Register a new user account

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "name": "John Doe",
  "phone": "+1234567890"
}
```

**Response (201):**
```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": {
    "userId": "507f1f77bcf86cd799439011",
    "email": "user@example.com"
  }
}
```

#### POST /api/auth/login
Authenticate user and receive JWT token

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}
```

**Response (200):**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "citizen"
  }
}
```

#### GET /api/auth/verify/:token
Verify email address

**Response (200):**
```json
{
  "success": true,
  "message": "Email verified successfully"
}
```

#### POST /api/auth/forgot-password
Request password reset

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Password reset email sent"
}
```

#### POST /api/auth/reset-password/:token
Reset password with token

**Request Body:**
```json
{
  "password": "NewSecurePass123!"
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Password reset successful"
}
```

### 5.2 Issue Endpoints

#### POST /api/issues
Create a new issue (Authenticated)

**Request Body (multipart/form-data):**
```json
{
  "title": "Large pothole on Main Street",
  "description": "Deep pothole causing vehicle damage",
  "category": "pothole",
  "severity": "high",
  "latitude": 40.7128,
  "longitude": -74.0060,
  "isAnonymous": false,
  "images": [File, File]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "507f1f77bcf86cd799439011",
    "trackingId": "CIV-2026-001234",
    "title": "Large pothole on Main Street",
    "status": "pending",
    "location": {
      "type": "Point",
      "coordinates": [-74.0060, 40.7128]
    },
    "images": [
      {
        "url": "https://res.cloudinary.com/...",
        "thumbnail": "https://res.cloudinary.com/..."
      }
    ],
    "createdAt": "2026-02-15T10:30:00Z"
  }
}
```

#### GET /api/issues
Get all issues with filtering and pagination

**Query Parameters:**
- `page` (default: 1)
- `limit` (default: 20)
- `category` (optional)
- `status` (optional)
- `severity` (optional)
- `sortBy` (date, votes, proximity)
- `lat` (for proximity sort)
- `lng` (for proximity sort)
- `search` (keyword search)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "issues": [...],
    "pagination": {
      "currentPage": 1,
      "totalPages": 10,
      "totalIssues": 200,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
```

#### GET /api/issues/:id
Get single issue details

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "507f1f77bcf86cd799439011",
    "trackingId": "CIV-2026-001234",
    "title": "Large pothole on Main Street",
    "description": "Deep pothole causing vehicle damage",
    "category": "pothole",
    "severity": "high",
    "status": "in_progress",
    "location": {
      "type": "Point",
      "coordinates": [-74.0060, 40.7128]
    },
    "address": {
      "formatted": "123 Main St, New York, NY 10001"
    },
    "images": [...],
    "reporter": {
      "id": "507f1f77bcf86cd799439012",
      "name": "John Doe"
    },
    "voteCount": 45,
    "commentCount": 12,
    "statusHistory": [...],
    "createdAt": "2026-02-15T10:30:00Z",
    "updatedAt": "2026-02-15T14:20:00Z"
  }
}
```

#### GET /api/issues/nearby
Get issues near a location

**Query Parameters:**
- `lat` (required)
- `lng` (required)
- `radius` (in meters, default: 5000)
- `limit` (default: 50)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "issues": [...],
    "count": 23
  }
}
```

#### PATCH /api/issues/:id
Update issue (Reporter or Admin only)

**Request Body:**
```json
{
  "title": "Updated title",
  "description": "Updated description"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* updated issue */ }
}
```

#### DELETE /api/issues/:id
Delete issue (Reporter or Admin only)

**Response (200):**
```json
{
  "success": true,
  "message": "Issue deleted successfully"
}
```

### 5.3 Vote Endpoints

#### POST /api/issues/:id/vote
Upvote an issue (Authenticated)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "voteCount": 46,
    "userVoted": true
  }
}
```

#### DELETE /api/issues/:id/vote
Remove vote from issue (Authenticated)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "voteCount": 45,
    "userVoted": false
  }
}
```

### 5.4 Comment Endpoints

#### POST /api/issues/:id/comments
Add comment to issue (Authenticated)

**Request Body:**
```json
{
  "text": "I've noticed this issue too. It's getting worse."
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "507f1f77bcf86cd799439013",
    "text": "I've noticed this issue too. It's getting worse.",
    "user": {
      "id": "507f1f77bcf86cd799439012",
      "name": "John Doe"
    },
    "createdAt": "2026-02-15T15:30:00Z"
  }
}
```

#### GET /api/issues/:id/comments
Get all comments for an issue

**Query Parameters:**
- `page` (default: 1)
- `limit` (default: 20)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "comments": [...],
    "pagination": {...}
  }
}
```

#### PATCH /api/comments/:id
Update own comment (within 15 minutes)

**Request Body:**
```json
{
  "text": "Updated comment text"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* updated comment */ }
}
```

#### DELETE /api/comments/:id
Delete own comment

**Response (200):**
```json
{
  "success": true,
  "message": "Comment deleted successfully"
}
```


### 5.5 User Endpoints

#### GET /api/users/profile
Get current user profile (Authenticated)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "507f1f77bcf86cd799439012",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "citizen",
    "stats": {
      "issuesReported": 15,
      "issuesVoted": 42,
      "commentsPosted": 28
    },
    "badges": [...],
    "createdAt": "2026-01-10T08:00:00Z"
  }
}
```

#### PATCH /api/users/profile
Update user profile (Authenticated)

**Request Body:**
```json
{
  "name": "John Smith",
  "phone": "+1234567890",
  "preferences": {
    "emailNotifications": true,
    "anonymousReporting": false
  }
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* updated profile */ }
}
```

#### PATCH /api/users/change-password
Change password (Authenticated)

**Request Body:**
```json
{
  "currentPassword": "OldPass123!",
  "newPassword": "NewPass123!"
}
```

**Response (200):**
```json
{
  "success": true,
  "message": "Password changed successfully"
}
```

#### DELETE /api/users/account
Delete user account (Authenticated)

**Response (200):**
```json
{
  "success": true,
  "message": "Account deleted successfully"
}
```

#### GET /api/users/:id/issues
Get issues reported by a user

**Response (200):**
```json
{
  "success": true,
  "data": {
    "issues": [...],
    "pagination": {...}
  }
}
```

### 5.6 Badge Endpoints

#### GET /api/badges
Get all available badges

**Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "badgeType": "first_report",
      "name": "First Report",
      "description": "Submit your first issue",
      "icon": "https://...",
      "criteria": "Submit 1 issue"
    },
    ...
  ]
}
```

#### GET /api/badges/user/:userId
Get badges earned by a user

**Response (200):**
```json
{
  "success": true,
  "data": {
    "badges": [...],
    "progress": {
      "active_reporter": {
        "current": 7,
        "required": 10,
        "percentage": 70
      }
    }
  }
}
```

#### GET /api/badges/:id/verify
Verify badge on blockchain

**Response (200):**
```json
{
  "success": true,
  "data": {
    "isValid": true,
    "blockchainTxHash": "0x...",
    "network": "polygon",
    "explorerUrl": "https://polygonscan.com/tx/0x..."
  }
}
```

### 5.7 Notification Endpoints

#### GET /api/notifications
Get user notifications (Authenticated)

**Query Parameters:**
- `page` (default: 1)
- `limit` (default: 20)
- `unreadOnly` (boolean)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "notifications": [...],
    "unreadCount": 5,
    "pagination": {...}
  }
}
```

#### PATCH /api/notifications/:id/read
Mark notification as read

**Response (200):**
```json
{
  "success": true,
  "message": "Notification marked as read"
}
```

#### PATCH /api/notifications/read-all
Mark all notifications as read

**Response (200):**
```json
{
  "success": true,
  "message": "All notifications marked as read"
}
```

### 5.8 Admin Endpoints

#### GET /api/admin/dashboard
Get admin dashboard metrics (Admin only)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "totalIssues": 1250,
    "pendingIssues": 45,
    "inProgressIssues": 23,
    "resolvedIssues": 1182,
    "avgResolutionTime": 4.5,  // days
    "issuesByCategory": {
      "pothole": 450,
      "streetlight": 320,
      "water_leak": 180,
      "garbage": 200,
      "other": 100
    },
    "recentIssues": [...],
    "topVotedIssues": [...]
  }
}
```

#### PATCH /api/admin/issues/:id/status
Update issue status (Admin only)

**Request Body:**
```json
{
  "status": "in_progress",
  "notes": "Work crew assigned. Expected completion in 3 days.",
  "assignedTo": {
    "department": "Public Works"
  }
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* updated issue */ }
}
```

#### POST /api/admin/issues/:id/resolve
Mark issue as resolved (Admin only)

**Request Body (multipart/form-data):**
```json
{
  "resolutionNotes": "Pothole filled and road resurfaced",
  "resolutionImages": [File, File]
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* resolved issue */ }
}
```

#### POST /api/admin/issues/:id/reject
Reject an issue (Admin only)

**Request Body:**
```json
{
  "rejectionReason": "Issue is outside our jurisdiction"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": { /* rejected issue */ }
}
```

#### GET /api/admin/reports
Generate analytics reports (Admin only)

**Query Parameters:**
- `type` (category, resolution, geographic)
- `startDate`
- `endDate`
- `format` (json, csv, pdf)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "report": {...},
    "downloadUrl": "https://..."  // for CSV/PDF
  }
}
```

#### GET /api/admin/users
Get all users (Admin only)

**Query Parameters:**
- `page` (default: 1)
- `limit` (default: 50)
- `role` (optional filter)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "users": [...],
    "pagination": {...}
  }
}
```

### 5.9 Search Endpoint

#### GET /api/search
Global search across issues

**Query Parameters:**
- `q` (search query)
- `type` (issues, users)
- `filters` (category, status, etc.)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "results": [...],
    "count": 15
  }
}
```

### 5.10 API Response Standards

**Success Response Format:**
```json
{
  "success": true,
  "data": { /* response data */ },
  "message": "Optional success message"
}
```

**Error Response Format:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": { /* optional additional details */ }
  }
}
```

**Common HTTP Status Codes:**
- 200: Success
- 201: Created
- 400: Bad Request (validation error)
- 401: Unauthorized (not authenticated)
- 403: Forbidden (not authorized)
- 404: Not Found
- 409: Conflict (duplicate resource)
- 429: Too Many Requests (rate limit)
- 500: Internal Server Error

---

## 6. Authentication & Authorization Flow

### 6.1 JWT Authentication Flow

```
┌─────────┐                                    ┌─────────┐
│ Client  │                                    │ Server  │
└────┬────┘                                    └────┬────┘
     │                                              │
     │  1. POST /api/auth/login                    │
     │     { email, password }                     │
     ├────────────────────────────────────────────>│
     │                                              │
     │                                         2. Validate
     │                                         credentials
     │                                         (bcrypt compare)
     │                                              │
     │                                         3. Generate JWT
     │                                         (user id, role)
     │                                              │
     │  4. Response: { token, user }               │
     │<────────────────────────────────────────────┤
     │                                              │
     │  5. Store token in localStorage/cookie      │
     │                                              │
     │  6. Subsequent requests with token          │
     │     Authorization: Bearer <token>           │
     ├────────────────────────────────────────────>│
     │                                              │
     │                                         7. Verify JWT
     │                                         (middleware)
     │                                              │
     │                                         8. Decode payload
     │                                         (user id, role)
     │                                              │
     │                                         9. Attach user
     │                                         to request
     │                                              │
     │  10. Protected resource response            │
     │<────────────────────────────────────────────┤
     │                                              │
```

### 6.2 JWT Token Structure

**Payload:**
```json
{
  "userId": "507f1f77bcf86cd799439011",
  "email": "user@example.com",
  "role": "citizen",
  "iat": 1708000000,
  "exp": 1708086400
}
```

**Token Configuration:**
- Algorithm: HS256
- Secret: Environment variable (JWT_SECRET)
- Expiration: 24 hours
- Refresh: Not implemented in v1 (future enhancement)

### 6.3 Authentication Middleware

```javascript
// middleware/auth.js
const authenticate = async (req, res, next) => {
  try {
    // 1. Extract token from header
    const token = req.header('Authorization')?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({
        success: false,
        error: { message: 'No authentication token provided' }
      });
    }
    
    // 2. Verify token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // 3. Find user
    const user = await User.findById(decoded.userId).select('-password');
    
    if (!user) {
      return res.status(401).json({
        success: false,
        error: { message: 'User not found' }
      });
    }
    
    // 4. Attach user to request
    req.user = user;
    req.userId = user._id;
    req.userRole = user.role;
    
    next();
  } catch (error) {
    res.status(401).json({
      success: false,
      error: { message: 'Invalid or expired token' }
    });
  }
};
```

### 6.4 Authorization Middleware

```javascript
// middleware/authorize.js
const authorize = (...roles) => {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({
        success: false,
        error: { message: 'Not authenticated' }
      });
    }
    
    if (!roles.includes(req.userRole)) {
      return res.status(403).json({
        success: false,
        error: { message: 'Not authorized to access this resource' }
      });
    }
    
    next();
  };
};

// Usage:
// app.use('/api/admin', authenticate, authorize('admin', 'authority'), adminRoutes);
```

### 6.5 Role-Based Access Control (RBAC)

| Resource | Citizen | Authority | Admin |
|----------|---------|-----------|-------|
| Register/Login | ✓ | ✓ | ✓ |
| Create Issue | ✓ | ✓ | ✓ |
| View Issues | ✓ | ✓ | ✓ |
| Edit Own Issue | ✓ | ✗ | ✓ |
| Delete Own Issue | ✓ | ✗ | ✓ |
| Vote on Issue | ✓ | ✓ | ✓ |
| Comment on Issue | ✓ | ✓ | ✓ |
| Update Issue Status | ✗ | ✓ | ✓ |
| Assign Issue | ✗ | ✓ | ✓ |
| View Dashboard | ✗ | ✓ | ✓ |
| Generate Reports | ✗ | ✓ | ✓ |
| Manage Users | ✗ | ✗ | ✓ |
| System Settings | ✗ | ✗ | ✓ |

### 6.6 Password Security

**Hashing:**
```javascript
// Pre-save hook in User model
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  
  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

// Password comparison method
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};
```

**Password Requirements:**
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

**Validation Regex:**
```javascript
const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/;
```

### 6.7 Session Management

**Token Storage (Frontend):**
- localStorage: Persistent across browser sessions
- sessionStorage: Cleared when tab closes
- httpOnly cookie: Most secure (prevents XSS)

**Recommended Approach:**
```javascript
// Store token in httpOnly cookie (backend sets)
res.cookie('token', token, {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  maxAge: 24 * 60 * 60 * 1000  // 24 hours
});
```

**Session Timeout:**
- Automatic logout after 30 minutes of inactivity
- Implemented via frontend activity tracking
- Token expiration enforced by backend

### 6.8 Email Verification Flow

```
1. User registers → Account created (isVerified: false)
2. Generate verification token (crypto.randomBytes)
3. Send email with verification link
4. User clicks link → GET /api/auth/verify/:token
5. Verify token → Update user (isVerified: true)
6. Redirect to login page
```

### 6.9 Password Reset Flow

```
1. User requests reset → POST /api/auth/forgot-password
2. Generate reset token (crypto.randomBytes)
3. Store token hash and expiry in database
4. Send email with reset link (valid 1 hour)
5. User clicks link → Redirect to reset form
6. User submits new password → POST /api/auth/reset-password/:token
7. Verify token and expiry
8. Hash new password and update user
9. Invalidate reset token
10. Send confirmation email
```

---

## 7. Issue Reporting Flow

### 7.1 Complete Issue Reporting Sequence

```
┌─────────┐                                    ┌─────────┐
│ Client  │                                    │ Backend │
└────┬────┘                                    └────┬────┘
     │                                              │
     │  1. User fills issue form                   │
     │     - Title, description, category          │
     │     - Auto-capture GPS location             │
     │     - Upload photos (optional)              │
     │                                              │
     │  2. POST /api/issues                        │
     │     (multipart/form-data)                   │
     ├────────────────────────────────────────────>│
     │                                              │
     │                                         3. Authenticate
     │                                         (JWT middleware)
     │                                              │
     │                                         4. Validate input
     │                                         (Joi schema)
     │                                              │
     │                                         5. Upload images
     │                                         to Cloudinary
     │                                              │
     │                                         6. Reverse geocode
     │                                         (Mapbox API)
     │                                              │
     │                                         7. Generate tracking ID
     │                                         (CIV-2026-XXXXXX)
     │                                              │
     │                                         8. Create issue in DB
     │                                         (MongoDB)
     │                                              │
     │                                         9. Update user stats
     │                                         (issuesReported++)
     │                                              │
     │                                         10. Check badge criteria
     │                                         (first_report?)
     │                                              │
     │                                         11. Emit Socket event
     │                                         (new_issue)
     │                                              │
     │  12. Response: Issue created                │
     │<────────────────────────────────────────────┤
     │                                              │
     │  13. Show success notification              │
     │                                              │
     │  14. Redirect to issue detail page          │
     │                                              │
     │  15. Socket.io receives new_issue event     │
     │                                              │
     │  16. Update map with new marker             │
     │                                              │
     │  17. Update issue list                      │
     │                                              │
```

### 7.2 Image Upload Process

```javascript
// Frontend: Image selection and preview
const handleImageSelect = (files) => {
  // 1. Validate file types
  const validTypes = ['image/jpeg', 'image/png', 'image/heic'];
  const validFiles = files.filter(f => validTypes.includes(f.type));
  
  // 2. Validate file sizes (max 5MB each)
  const validSizes = validFiles.filter(f => f.size <= 5 * 1024 * 1024);
  
  // 3. Limit to 5 images
  const selectedImages = validSizes.slice(0, 5);
  
  // 4. Generate previews
  selectedImages.forEach(file => {
    const reader = new FileReader();
    reader.onload = (e) => {
      setPreviews(prev => [...prev, e.target.result]);
    };
    reader.readAsDataURL(file);
  });
  
  setImages(selectedImages);
};

// Backend: Image processing
const uploadImages = async (files) => {
  const uploadPromises = files.map(async (file) => {
    // 1. Upload to Cloudinary
    const result = await cloudinary.uploader.upload(file.path, {
      folder: 'civix/issues',
      transformation: [
        { width: 1200, height: 1200, crop: 'limit' },
        { quality: 'auto:good' }
      ]
    });
    
    // 2. Generate thumbnail
    const thumbnail = cloudinary.url(result.public_id, {
      width: 300,
      height: 300,
      crop: 'fill'
    });
    
    return {
      url: result.secure_url,
      publicId: result.public_id,
      thumbnail: thumbnail,
      uploadedAt: new Date()
    };
  });
  
  return await Promise.all(uploadPromises);
};
```

### 7.3 Geolocation Capture

```javascript
// Frontend: Get user location
const captureLocation = () => {
  return new Promise((resolve, reject) => {
    if (!navigator.geolocation) {
      reject(new Error('Geolocation not supported'));
    }
    
    navigator.geolocation.getCurrentPosition(
      (position) => {
        resolve({
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
          accuracy: position.coords.accuracy
        });
      },
      (error) => {
        reject(error);
      },
      {
        enableHighAccuracy: true,
        timeout: 10000,
        maximumAge: 0
      }
    );
  });
};

// Backend: Reverse geocoding
const reverseGeocode = async (lat, lng) => {
  const response = await axios.get(
    `https://api.mapbox.com/geocoding/v5/mapbox.places/${lng},${lat}.json`,
    {
      params: {
        access_token: process.env.MAPBOX_ACCESS_TOKEN,
        types: 'address'
      }
    }
  );
  
  const place = response.data.features[0];
  
  return {
    street: place.address + ' ' + place.text,
    city: place.context.find(c => c.id.includes('place'))?.text,
    state: place.context.find(c => c.id.includes('region'))?.text,
    zipCode: place.context.find(c => c.id.includes('postcode'))?.text,
    country: place.context.find(c => c.id.includes('country'))?.text,
    formatted: place.place_name
  };
};
```

### 7.4 Duplicate Detection

```javascript
// Check for duplicate issues
const checkDuplicate = async (category, coordinates) => {
  const existingIssue = await Issue.findOne({
    category: category,
    status: { $in: ['pending', 'in_progress'] },
    location: {
      $near: {
        $geometry: {
          type: 'Point',
          coordinates: coordinates
        },
        $maxDistance: 100  // 100 meters
      }
    },
    createdAt: {
      $gte: new Date(Date.now() - 24 * 60 * 60 * 1000)  // Last 24 hours
    }
  });
  
  return existingIssue;
};
```

### 7.5 Tracking ID Generation

```javascript
const generateTrackingId = async () => {
  const year = new Date().getFullYear();
  
  // Get count of issues this year
  const count = await Issue.countDocuments({
    createdAt: {
      $gte: new Date(`${year}-01-01`),
      $lt: new Date(`${year + 1}-01-01`)
    }
  });
  
  // Format: CIV-YYYY-NNNNNN
  const trackingId = `CIV-${year}-${String(count + 1).padStart(6, '0')}`;
  
  return trackingId;
};
```

---

## 8. Admin Workflow

### 8.1 Admin Dashboard Overview

```
┌────────────────────────────────────────────────────────────┐
│                    CIVIX Admin Dashboard                    │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │   Total      │  │   Pending    │  │  In Progress │    │
│  │   Issues     │  │   Issues     │  │   Issues     │    │
│  │    1,250     │  │      45      │  │      23      │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │   Resolved   │  │   Average    │  │  Resolution  │    │
│  │   Issues     │  │  Resolution  │  │     Rate     │    │
│  │    1,182     │  │   4.5 days   │  │     94.6%    │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         Issues by Category (Chart)                   │  │
│  │  Pothole: ████████████ 450                          │  │
│  │  Streetlight: ████████ 320                          │  │
│  │  Water Leak: █████ 180                              │  │
│  │  Garbage: ██████ 200                                │  │
│  │  Other: ███ 100                                     │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         Recent Issues (Table)                        │  │
│  │  ID          | Title        | Status    | Priority  │  │
│  │  CIV-001234  | Pothole...   | Pending   | High      │  │
│  │  CIV-001233  | Streetlight  | Progress  | Medium    │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

### 8.2 Issue Management Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                  Issue Lifecycle States                      │
└─────────────────────────────────────────────────────────────┘

    ┌──────────┐
    │  PENDING │  ← Initial state when citizen reports
    └─────┬────┘
          │
          ├─────────────────────────────────────┐
          │                                     │
          ▼                                     ▼
    ┌─────────────┐                      ┌──────────┐
    │ IN PROGRESS │                      │ REJECTED │
    └──────┬──────┘                      └──────────┘
           │                                   ↑
           │                                   │
           ▼                              (Invalid/
    ┌──────────┐                         Out of scope)
    │ RESOLVED │
    └──────────┘

State Transitions:
1. PENDING → IN_PROGRESS: Admin assigns and starts work
2. PENDING → REJECTED: Issue is invalid or out of scope
3. IN_PROGRESS → RESOLVED: Work completed
4. IN_PROGRESS → PENDING: Work paused/reassigned
```

### 8.3 Admin Actions Sequence

**Scenario: Admin processes a new issue**

```
1. Admin logs into dashboard
   ↓
2. Views pending issues list
   ↓
3. Clicks on issue to view details
   ↓
4. Reviews:
   - Issue description
   - Photos
   - Location on map
   - Vote count
   - Comments
   ↓
5. Validates issue legitimacy
   ↓
6. Decision Point:
   
   Valid Issue?
   ├─ YES → Continue to step 7
   └─ NO → Reject issue with reason
   
7. Assigns issue:
   - Select department (Public Works, Utilities, etc.)
   - Set priority (Low/Medium/High)
   - Add internal notes
   ↓
8. Updates status to "In Progress"
   ↓
9. Adds public update:
   "Work crew assigned. Expected completion in 3 days."
   ↓
10. System actions:
    - Saves status change to database
    - Records in status history
    - Sends notification to reporter
    - Broadcasts Socket.io event
    ↓
11. Work is completed
    ↓
12. Admin marks as resolved:
    - Adds resolution notes
    - Uploads completion photos
    - Sets resolved timestamp
    ↓
13. System actions:
    - Updates issue status
    - Sends notification to reporter
    - Sends notification to voters
    - Updates analytics
    - Checks badge criteria for reporter
```

### 8.4 Issue Assignment System

```javascript
// Issue assignment structure
{
  assignedTo: {
    department: 'Public Works',
    assignee: ObjectId('...'),  // Specific authority user
    assignedAt: Date,
    assignedBy: ObjectId('...')  // Admin who assigned
  },
  priority: 'high',
  estimatedCompletion: Date,
  internalNotes: 'Requires heavy equipment. Schedule for next week.'
}

// Department categories
const departments = [
  'Public Works',
  'Water & Sewage',
  'Electrical',
  'Sanitation',
  'Parks & Recreation',
  'Transportation',
  'Building & Safety'
];
```

### 8.5 Bulk Operations

**Bulk Status Update:**
```javascript
// Admin can update multiple issues at once
POST /api/admin/issues/bulk-update
{
  "issueIds": ["id1", "id2", "id3"],
  "updates": {
    "status": "in_progress",
    "assignedTo": {
      "department": "Public Works"
    }
  }
}
```

**Bulk Export:**
```javascript
// Export filtered issues to CSV
GET /api/admin/issues/export?status=pending&category=pothole&format=csv
```

### 8.6 Analytics and Reporting

**Available Reports:**

1. **Issue Summary Report**
   - Total issues by status
   - Issues by category
   - Issues by location
   - Time period comparison

2. **Resolution Performance Report**
   - Average resolution time by category
   - Resolution rate by department
   - SLA compliance metrics
   - Trend analysis

3. **User Engagement Report**
   - Active users count
   - Issues per user
   - Voting patterns
   - Comment activity

4. **Geographic Heat Map**
   - Issue density by area
   - Category distribution by location
   - Identify problem zones

**Report Generation:**
```javascript
// Generate report
POST /api/admin/reports/generate
{
  "reportType": "resolution_performance",
  "dateRange": {
    "start": "2026-01-01",
    "end": "2026-02-15"
  },
  "filters": {
    "category": "pothole",
    "department": "Public Works"
  },
  "format": "pdf"
}

// Response
{
  "success": true,
  "data": {
    "reportId": "rep_123456",
    "downloadUrl": "https://...",
    "expiresAt": "2026-02-16T10:00:00Z"
  }
}
```

### 8.7 Admin Notification System

**Admin receives notifications for:**
- New high-priority issues
- Issues with high vote counts (>50 votes)
- Issues pending for >7 days
- User reports/flags on issues
- System alerts

**Notification Preferences:**
```javascript
{
  adminNotifications: {
    newHighPriority: true,
    highVoteCount: true,
    pendingThreshold: 7,  // days
    emailDigest: 'daily',  // daily, weekly, never
    pushNotifications: true
  }
}
```

### 8.8 Admin Activity Logging

**All admin actions are logged:**
```javascript
{
  _id: ObjectId,
  admin: ObjectId('...'),
  action: 'status_update',
  resource: 'issue',
  resourceId: ObjectId('...'),
  changes: {
    before: { status: 'pending' },
    after: { status: 'in_progress' }
  },
  ipAddress: '192.168.1.1',
  userAgent: 'Mozilla/5.0...',
  timestamp: Date
}
```

**Audit Trail Benefits:**
- Accountability
- Compliance
- Debugging
- Performance tracking
- Security monitoring

---

## 9. Deployment Strategy

### 9.1 Infrastructure Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Production Environment                  │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │              Load Balancer (NGINX)                  │    │
│  │         SSL Termination | Rate Limiting             │    │
│  └──────────────────┬─────────────────────────────────┘    │
│                     │                                        │
│         ┌───────────┴───────────┐                           │
│         │                       │                           │
│  ┌──────▼──────┐        ┌──────▼──────┐                   │
│  │   Node.js   │        │   Node.js   │                   │
│  │   Server 1  │        │   Server 2  │                   │
│  │  (Primary)  │        │  (Replica)  │                   │
│  └──────┬──────┘        └──────┬──────┘                   │
│         │                       │                           │
│         └───────────┬───────────┘                           │
│                     │                                        │
│              ┌──────▼──────┐                                │
│              │   MongoDB   │                                │
│              │   Cluster   │                                │
│              │  (Replica   │                                │
│              │    Set)     │                                │
│              └─────────────┘                                │
│                                                              │
│  External Services:                                         │
│  • Cloudinary (Image CDN)                                   │
│  • Mapbox (Maps & Geocoding)                                │
│  • SendGrid (Email)                                         │
│  • Blockchain Network (Badges)                              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 9.2 Deployment Platforms

**Recommended Platform: AWS**

**Services Used:**
- **EC2**: Node.js application servers
- **MongoDB Atlas**: Managed MongoDB cluster
- **S3**: Static asset storage (backup)
- **CloudFront**: CDN for frontend assets
- **Route 53**: DNS management
- **Certificate Manager**: SSL certificates
- **CloudWatch**: Monitoring and logging
- **Elastic Load Balancer**: Traffic distribution

**Alternative Platforms:**
- **Heroku**: Quick deployment, managed infrastructure
- **DigitalOcean**: Cost-effective, simple setup
- **Google Cloud Platform**: Similar to AWS
- **Vercel**: Frontend hosting (React app)
- **Railway**: Full-stack deployment

### 9.3 Environment Configuration

**Three Environments:**

1. **Development**
   - Local machines
   - Docker containers
   - MongoDB local instance
   - Mock external services

2. **Staging**
   - Mirrors production
   - Testing environment
   - Separate database
   - Real external services (test accounts)

3. **Production**
   - Live environment
   - High availability
   - Monitoring enabled
   - Backup systems active

**Environment Variables:**
```bash
# .env.production
NODE_ENV=production
PORT=5000

# Database
MONGODB_URI=mongodb+srv://...

# JWT
JWT_SECRET=<strong-secret-key>
JWT_EXPIRE=24h

# Cloudinary
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...

# Mapbox
MAPBOX_ACCESS_TOKEN=...

# Email
SENDGRID_API_KEY=...
EMAIL_FROM=noreply@civix.com

# Blockchain
BLOCKCHAIN_NETWORK=polygon
BLOCKCHAIN_RPC_URL=...
BLOCKCHAIN_PRIVATE_KEY=...
CONTRACT_ADDRESS=...

# Frontend URL
FRONTEND_URL=https://civix.com

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX_REQUESTS=100
```

### 9.4 CI/CD Pipeline

**Using GitHub Actions:**

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
      - name: Run linter
        run: npm run lint

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build frontend
        run: |
          cd frontend
          npm install
          npm run build
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: frontend-build
          path: frontend/build

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to AWS EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            cd /var/www/civix
            git pull origin main
            npm install
            pm2 restart civix
```

### 9.5 Docker Configuration

**Backend Dockerfile:**
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 5000

CMD ["node", "server.js"]
```

**Frontend Dockerfile:**
```dockerfile
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**Docker Compose:**
```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=production
      - MONGODB_URI=${MONGODB_URI}
    depends_on:
      - mongodb

  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend

  mongodb:
    image: mongo:6
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

### 9.6 Database Migration Strategy

**Initial Setup:**
```javascript
// scripts/setup-database.js
const setupDatabase = async () => {
  // 1. Create indexes
  await db.collection('users').createIndex({ email: 1 }, { unique: true });
  await db.collection('issues').createIndex({ location: '2dsphere' });
  
  // 2. Create default admin user
  await User.create({
    email: 'admin@civix.com',
    password: 'ChangeMe123!',
    role: 'admin',
    isVerified: true
  });
  
  // 3. Seed issue categories
  // 4. Initialize system settings
};
```

**Migration Process:**
```bash
# Run migrations
npm run migrate

# Rollback if needed
npm run migrate:rollback
```

### 9.7 Monitoring and Logging

**Application Monitoring:**
- **PM2**: Process management and monitoring
- **CloudWatch**: AWS metrics and logs
- **Sentry**: Error tracking
- **New Relic**: Performance monitoring (optional)

**Log Management:**
```javascript
// Winston logger configuration
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});
```

**Health Check Endpoint:**
```javascript
// GET /api/health
app.get('/api/health', async (req, res) => {
  const health = {
    uptime: process.uptime(),
    timestamp: Date.now(),
    status: 'OK',
    services: {
      database: await checkDatabaseConnection(),
      cloudinary: await checkCloudinaryConnection(),
      mapbox: await checkMapboxConnection()
    }
  };
  
  res.json(health);
});
```

### 9.8 Backup Strategy

**Database Backups:**
- **Frequency**: Daily automated backups
- **Retention**: 30 days
- **Storage**: AWS S3 or MongoDB Atlas backups
- **Testing**: Monthly restore tests

**Backup Script:**
```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_NAME="civix"

# Create backup
mongodump --uri="$MONGODB_URI" --out="$BACKUP_DIR/$DATE"

# Compress
tar -czf "$BACKUP_DIR/civix_$DATE.tar.gz" "$BACKUP_DIR/$DATE"

# Upload to S3
aws s3 cp "$BACKUP_DIR/civix_$DATE.tar.gz" s3://civix-backups/

# Clean up old backups (keep 30 days)
find $BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete
```

### 9.9 Rollback Plan

**Rollback Procedure:**
1. Identify issue in production
2. Stop incoming traffic (maintenance mode)
3. Revert to previous stable version
4. Restore database if needed
5. Run smoke tests
6. Resume traffic
7. Post-mortem analysis

**Zero-Downtime Deployment:**
- Blue-green deployment
- Rolling updates
- Feature flags for gradual rollout

---

## 10. Security Considerations

### 10.1 Security Layers

```
┌─────────────────────────────────────────────────────────────┐
│                    Security Architecture                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Layer 1: Network Security                                  │
│  • HTTPS/TLS 1.3 encryption                                 │
│  • Firewall rules                                           │
│  • DDoS protection                                          │
│  • VPC isolation (AWS)                                      │
│                                                              │
│  Layer 2: Application Security                              │
│  • Input validation                                         │
│  • Output encoding                                          │
│  • CORS policy                                              │
│  • Security headers (Helmet.js)                             │
│  • Rate limiting                                            │
│                                                              │
│  Layer 3: Authentication & Authorization                    │
│  • JWT tokens                                               │
│  • Password hashing (bcrypt)                                │
│  • Role-based access control                                │
│  • Session management                                       │
│                                                              │
│  Layer 4: Data Security                                     │
│  • Database encryption at rest                              │
│  • Sensitive data masking                                   │
│  • Secure backups                                           │
│  • PII protection                                           │
│                                                              │
│  Layer 5: Monitoring & Response                             │
│  • Security logging                                         │
│  • Intrusion detection                                      │
│  • Incident response plan                                   │
│  • Regular security audits                                  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 10.2 OWASP Top 10 Mitigation

#### 10.2.1 Injection Attacks

**SQL/NoSQL Injection Prevention:**
```javascript
// ✓ SAFE: Using Mongoose with parameterized queries
const user = await User.findOne({ email: req.body.email });

// ✗ UNSAFE: String concatenation
const user = await User.findOne({ $where: `this.email == '${req.body.email}'` });

// Input validation with Joi
const issueSchema = Joi.object({
  title: Joi.string().min(5).max(200).required(),
  description: Joi.string().min(10).max(2000).required(),
  category: Joi.string().valid('pothole', 'streetlight', 'water_leak', 'garbage', 'other').required()
});
```

#### 10.2.2 Broken Authentication

**Secure Authentication Implementation:**
```javascript
// Password hashing
const hashedPassword = await bcrypt.hash(password, 10);

// JWT with expiration
const token = jwt.sign(
  { userId: user._id, role: user.role },
  process.env.JWT_SECRET,
  { expiresIn: '24h' }
);

// Account lockout after failed attempts
const MAX_LOGIN_ATTEMPTS = 5;
const LOCK_TIME = 15 * 60 * 1000; // 15 minutes

if (user.loginAttempts >= MAX_LOGIN_ATTEMPTS) {
  if (Date.now() - user.lockUntil < LOCK_TIME) {
    throw new Error('Account temporarily locked');
  }
}
```

#### 10.2.3 Sensitive Data Exposure

**Data Protection:**
```javascript
// Remove sensitive fields from responses
userSchema.methods.toJSON = function() {
  const user = this.toObject();
  delete user.password;
  delete user.resetPasswordToken;
  delete user.verificationToken;
  return user;
};

// Encrypt sensitive data at rest
const crypto = require('crypto');
const algorithm = 'aes-256-gcm';

const encrypt = (text) => {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv(algorithm, Buffer.from(key), iv);
  let encrypted = cipher.update(text);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  return iv.toString('hex') + ':' + encrypted.toString('hex');
};
```

#### 10.2.4 XML External Entities (XXE)

**Prevention:**
- Not applicable (no XML parsing in this application)
- If added later, use secure XML parsers with external entity processing disabled

#### 10.2.5 Broken Access Control

**Authorization Checks:**
```javascript
// Check resource ownership
const checkIssueOwnership = async (req, res, next) => {
  const issue = await Issue.findById(req.params.id);
  
  if (!issue) {
    return res.status(404).json({ error: 'Issue not found' });
  }
  
  // Only owner or admin can modify
  if (issue.reporter.toString() !== req.userId && req.userRole !== 'admin') {
    return res.status(403).json({ error: 'Not authorized' });
  }
  
  req.issue = issue;
  next();
};

// Usage
router.patch('/issues/:id', authenticate, checkIssueOwnership, updateIssue);
```

#### 10.2.6 Security Misconfiguration

**Secure Configuration:**
```javascript
// Helmet.js for security headers
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https://res.cloudinary.com"],
      connectSrc: ["'self'", "https://api.mapbox.com"]
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
}));

// CORS configuration
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true,
  optionsSuccessStatus: 200
}));

// Hide sensitive headers
app.disable('x-powered-by');
```

#### 10.2.7 Cross-Site Scripting (XSS)

**XSS Prevention:**
```javascript
// Input sanitization
const sanitizeHtml = require('sanitize-html');

const sanitizeInput = (input) => {
  return sanitizeHtml(input, {
    allowedTags: [],
    allowedAttributes: {}
  });
};

// Usage in controller
const createIssue = async (req, res) => {
  const { title, description } = req.body;
  
  const issue = await Issue.create({
    title: sanitizeInput(title),
    description: sanitizeInput(description),
    // ...
  });
};

// Frontend: Use React's built-in XSS protection
// React automatically escapes values in JSX
<div>{issue.title}</div>  // Safe

// For dangerouslySetInnerHTML, sanitize first
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />
```

#### 10.2.8 Insecure Deserialization

**Prevention:**
```javascript
// Use JSON.parse safely
try {
  const data = JSON.parse(req.body.data);
} catch (error) {
  return res.status(400).json({ error: 'Invalid JSON' });
}

// Validate deserialized data
const schema = Joi.object({
  // Define expected structure
});

const { error, value } = schema.validate(data);
```

#### 10.2.9 Using Components with Known Vulnerabilities

**Dependency Management:**
```bash
# Regular dependency audits
npm audit

# Fix vulnerabilities
npm audit fix

# Update dependencies
npm update

# Use Snyk or Dependabot for automated monitoring
```

**package.json security:**
```json
{
  "scripts": {
    "audit": "npm audit",
    "audit:fix": "npm audit fix"
  }
}
```

#### 10.2.10 Insufficient Logging & Monitoring

**Comprehensive Logging:**
```javascript
// Security event logging
const logSecurityEvent = (event, details) => {
  logger.warn('SECURITY_EVENT', {
    event,
    details,
    timestamp: new Date(),
    ip: details.ip,
    userId: details.userId
  });
};

// Log authentication attempts
app.post('/api/auth/login', async (req, res) => {
  const { email, password } = req.body;
  
  try {
    const user = await User.findOne({ email });
    
    if (!user || !(await user.comparePassword(password))) {
      logSecurityEvent('FAILED_LOGIN', {
        email,
        ip: req.ip,
        userAgent: req.get('user-agent')
      });
      
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    logSecurityEvent('SUCCESSFUL_LOGIN', {
      userId: user._id,
      ip: req.ip
    });
    
    // Generate token...
  } catch (error) {
    logger.error('Login error', error);
  }
});
```

### 10.3 Rate Limiting

**Implementation:**
```javascript
const rateLimit = require('express-rate-limit');

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later',
  standardHeaders: true,
  legacyHeaders: false
});

// Strict rate limit for authentication
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // 5 attempts per window
  skipSuccessfulRequests: true
});

// Apply limiters
app.use('/api/', apiLimiter);
app.use('/api/auth/login', authLimiter);
app.use('/api/auth/register', authLimiter);
```

### 10.4 CSRF Protection

**Implementation:**
```javascript
const csrf = require('csurf');

// CSRF protection for state-changing operations
const csrfProtection = csrf({ cookie: true });

app.use(csrfProtection);

// Send CSRF token to frontend
app.get('/api/csrf-token', (req, res) => {
  res.json({ csrfToken: req.csrfToken() });
});

// Frontend includes token in requests
axios.post('/api/issues', data, {
  headers: {
    'CSRF-Token': csrfToken
  }
});
```

### 10.5 File Upload Security

**Secure Upload Handling:**
```javascript
const multer = require('multer');
const path = require('path');

// File filter
const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/heic'];
  
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('Invalid file type. Only JPEG, PNG, and HEIC allowed.'), false);
  }
};

// Multer configuration
const upload = multer({
  storage: multer.memoryStorage(),
  limits: {
    fileSize: 5 * 1024 * 1024, // 5MB
    files: 5
  },
  fileFilter: fileFilter
});

// Validate file content (not just extension)
const validateImageContent = async (buffer) => {
  const fileType = await import('file-type');
  const type = await fileType.fromBuffer(buffer);
  
  if (!type || !['image/jpeg', 'image/png'].includes(type.mime)) {
    throw new Error('Invalid image file');
  }
};
```

### 10.6 API Security Best Practices

**Security Checklist:**

1. **Always use HTTPS** in production
2. **Validate all inputs** on both client and server
3. **Sanitize outputs** to prevent XSS
4. **Use parameterized queries** to prevent injection
5. **Implement rate limiting** on all endpoints
6. **Log security events** for audit trail
7. **Keep dependencies updated** regularly
8. **Use environment variables** for secrets
9. **Implement proper error handling** (don't leak info)
10. **Regular security audits** and penetration testing

**Error Handling:**
```javascript
// Don't expose internal errors
app.use((err, req, res, next) => {
  logger.error(err.stack);
  
  // Generic error message for production
  const message = process.env.NODE_ENV === 'production'
    ? 'An error occurred'
    : err.message;
  
  res.status(err.status || 500).json({
    success: false,
    error: { message }
  });
});
```

### 10.7 GDPR Compliance

**Data Protection Measures:**

1. **User Consent**
   - Clear privacy policy
   - Explicit consent for data collection
   - Cookie consent banner

2. **Right to Access**
   - Users can download their data
   - API endpoint: GET /api/users/data-export

3. **Right to Deletion**
   - Users can delete their account
   - Cascade delete or anonymize data
   - API endpoint: DELETE /api/users/account

4. **Data Minimization**
   - Collect only necessary data
   - Optional fields for non-essential info

5. **Data Retention**
   - Clear retention policies
   - Automatic deletion of old data
   - Notification cleanup (30 days)

**Data Export Implementation:**
```javascript
const exportUserData = async (userId) => {
  const user = await User.findById(userId).select('-password');
  const issues = await Issue.find({ reporter: userId });
  const comments = await Comment.find({ user: userId });
  const votes = await Vote.find({ user: userId });
  const badges = await Badge.find({ user: userId });
  
  return {
    user,
    issues,
    comments,
    votes,
    badges,
    exportDate: new Date()
  };
};
```

### 10.8 Security Testing

**Testing Strategy:**

1. **Automated Security Scanning**
   - npm audit
   - Snyk
   - OWASP ZAP

2. **Manual Testing**
   - Penetration testing
   - Code review
   - Security audit

3. **Continuous Monitoring**
   - Real-time alerts
   - Log analysis
   - Anomaly detection

---

## 11. Scalability Plan

### 11.1 Scalability Dimensions

```
┌─────────────────────────────────────────────────────────────┐
│                    Scalability Strategy                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Vertical Scaling (Scale Up)                                │
│  • Increase server resources (CPU, RAM)                     │
│  • Upgrade database instance                                │
│  • Suitable for: 0-10K users                                │
│                                                              │
│  Horizontal Scaling (Scale Out)                             │
│  • Add more application servers                             │
│  • Database replication                                     │
│  • Load balancing                                           │
│  • Suitable for: 10K+ users                                 │
│                                                              │
│  Database Scaling                                           │
│  • Read replicas                                            │
│  • Sharding                                                 │
│  • Indexing optimization                                    │
│  • Query optimization                                       │
│                                                              │
│  Caching Strategy                                           │
│  • Redis for session storage                                │
│  • CDN for static assets                                    │
│  • Application-level caching                                │
│  • Database query caching                                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 11.2 Load Balancing

**NGINX Configuration:**
```nginx
upstream backend {
    least_conn;  # Load balancing method
    server backend1.civix.com:5000 weight=3;
    server backend2.civix.com:5000 weight=2;
    server backend3.civix.com:5000 weight=1 backup;
}

server {
    listen 443 ssl http2;
    server_name api.civix.com;
    
    ssl_certificate /etc/ssl/certs/civix.crt;
    ssl_certificate_key /etc/ssl/private/civix.key;
    
    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### 11.3 Caching Strategy

**Redis Implementation:**
```javascript
const redis = require('redis');
const client = redis.createClient({
  host: process.env.REDIS_HOST,
  port: process.env.REDIS_PORT
});

// Cache middleware
const cacheMiddleware = (duration) => {
  return async (req, res, next) => {
    const key = `cache:${req.originalUrl}`;
    
    try {
      const cached = await client.get(key);
      
      if (cached) {
        return res.json(JSON.parse(cached));
      }
      
      // Store original send function
      const originalSend = res.json;
      
      // Override send function
      res.json = function(data) {
        client.setex(key, duration, JSON.stringify(data));
        originalSend.call(this, data);
      };
      
      next();
    } catch (error) {
      next();
    }
  };
};

// Usage
app.get('/api/issues', cacheMiddleware(300), getIssues);  // Cache for 5 minutes
```

**Cache Invalidation:**
```javascript
// Invalidate cache when data changes
const invalidateIssueCache = async (issueId) => {
  const keys = await client.keys('cache:/api/issues*');
  if (keys.length > 0) {
    await client.del(...keys);
  }
};

// Call after creating/updating issue
await invalidateIssueCache();
```

### 11.4 Database Optimization

**Indexing Strategy:**
```javascript
// Compound indexes for common queries
db.issues.createIndex({ status: 1, createdAt: -1 });
db.issues.createIndex({ category: 1, status: 1, createdAt: -1 });
db.issues.createIndex({ reporter: 1, createdAt: -1 });

// Geospatial index for location queries
db.issues.createIndex({ location: '2dsphere' });

// Text index for search
db.issues.createIndex({ title: 'text', description: 'text' });
```

**Query Optimization:**
```javascript
// ✓ OPTIMIZED: Select only needed fields
const issues = await Issue.find({ status: 'pending' })
  .select('title category location status createdAt')
  .limit(20)
  .lean();  // Return plain objects (faster)

// ✗ UNOPTIMIZED: Fetch all fields
const issues = await Issue.find({ status: 'pending' });

// ✓ OPTIMIZED: Use pagination
const page = parseInt(req.query.page) || 1;
const limit = 20;
const skip = (page - 1) * limit;

const issues = await Issue.find()
  .skip(skip)
  .limit(limit);

// ✓ OPTIMIZED: Aggregate for statistics
const stats = await Issue.aggregate([
  { $match: { status: 'resolved' } },
  { $group: {
      _id: '$category',
      count: { $sum: 1 },
      avgResolutionTime: { $avg: '$resolutionTime' }
    }
  }
]);
```

**Read Replicas:**
```javascript
// MongoDB connection with read preference
const mongoose = require('mongoose');

mongoose.connect(process.env.MONGODB_URI, {
  readPreference: 'secondaryPreferred',  // Read from replicas when possible
  replicaSet: 'civix-replica-set'
});

// Force read from primary for critical operations
const user = await User.findById(userId).read('primary');
```

### 11.5 CDN Configuration

**Cloudflare/CloudFront Setup:**
```javascript
// Static assets served via CDN
const CDN_URL = process.env.CDN_URL;

// Frontend configuration
const config = {
  publicPath: process.env.NODE_ENV === 'production' ? CDN_URL : '/'
};

// Image URLs with CDN
const imageUrl = `${CDN_URL}/images/${filename}`;
```

### 11.6 Microservices Architecture (Future)

**Service Decomposition:**
```
Current Monolith → Future Microservices

┌─────────────────┐     ┌─────────────────┐
│   Monolithic    │     │  Auth Service   │
│   Application   │ →   ├─────────────────┤
│                 │     │  Issue Service  │
│  • Auth         │     ├─────────────────┤
│  • Issues       │     │  User Service   │
│  • Users        │     ├─────────────────┤
│  • Badges       │     │  Badge Service  │
│  • Notifications│     ├─────────────────┤
└─────────────────┘     │ Notification    │
                        │    Service      │
                        └─────────────────┘
```

### 11.7 Performance Targets

**Scalability Milestones:**

| Users | Strategy | Infrastructure |
|-------|----------|----------------|
| 0-1K | Single server | 1 EC2 instance, MongoDB Atlas M10 |
| 1K-10K | Vertical scaling | Larger EC2, MongoDB M30 |
| 10K-50K | Horizontal scaling | 2-3 EC2 instances, Load balancer, Redis |
| 50K-100K | Advanced caching | 5+ EC2 instances, Read replicas, CDN |
| 100K+ | Microservices | Service mesh, Kubernetes, Sharding |

**Performance Benchmarks:**
- API response time: <200ms (p95)
- Page load time: <2s
- Concurrent users: 10,000+
- Database queries: <50ms (p95)
- Image upload: <5s

---

## 12. Risk Mitigation

### 12.1 Risk Assessment Matrix

| Risk | Probability | Impact | Severity | Mitigation Strategy |
|------|------------|--------|----------|---------------------|
| Data breach | Medium | High | Critical | Encryption, security audits, monitoring |
| Service downtime | Low | High | High | Load balancing, redundancy, monitoring |
| Database failure | Low | Critical | Critical | Backups, replication, disaster recovery |
| API abuse | High | Medium | Medium | Rate limiting, authentication, monitoring |
| Image storage costs | Medium | Medium | Medium | Compression, CDN, usage limits |
| Blockchain network issues | Medium | Low | Low | Fallback mechanisms, queue system |
| Third-party API failures | Medium | Medium | Medium | Graceful degradation, fallbacks |
| Scalability bottlenecks | Medium | High | High | Performance monitoring, load testing |
| User data privacy violations | Low | Critical | Critical | GDPR compliance, data protection |
| Malicious content upload | High | Medium | Medium | Content moderation, validation |

### 12.2 Technical Risks

#### 12.2.1 Database Failure

**Risk:** MongoDB cluster failure leading to data loss or service unavailability

**Mitigation:**
```javascript
// 1. Replica Set Configuration
const mongoConfig = {
  replicaSet: 'civix-rs',
  readPreference: 'secondaryPreferred',
  retryWrites: true,
  w: 'majority'  // Write concern for durability
};

// 2. Automated Backups
// Daily backups with 30-day retention
// Point-in-time recovery enabled

// 3. Health Monitoring
const checkDatabaseHealth = async () => {
  try {
    await mongoose.connection.db.admin().ping();
    return { status: 'healthy' };
  } catch (error) {
    logger.error('Database health check failed', error);
    // Trigger alert
    return { status: 'unhealthy', error };
  }
};

// 4. Connection Pooling
mongoose.connect(uri, {
  maxPoolSize: 10,
  minPoolSize: 2,
  socketTimeoutMS: 45000
});
```

**Recovery Plan:**
1. Detect failure via monitoring
2. Failover to replica (automatic)
3. Restore from backup if needed
4. Investigate root cause
5. Implement preventive measures

#### 12.2.2 Third-Party Service Failures

**Risk:** Cloudinary, Mapbox, or blockchain services become unavailable

**Mitigation:**
```javascript
// Graceful degradation for Cloudinary
const uploadImage = async (file) => {
  try {
    const result = await cloudinary.uploader.upload(file.path);
    return result.secure_url;
  } catch (error) {
    logger.error('Cloudinary upload failed', error);
    
    // Fallback: Store locally temporarily
    const localPath = await saveLocally(file);
    
    // Queue for retry
    await queueImageUpload(file, localPath);
    
    return localPath;
  }
};

// Fallback for Mapbox geocoding
const geocodeAddress = async (lat, lng) => {
  try {
    return await mapboxGeocode(lat, lng);
  } catch (error) {
    logger.error('Mapbox geocoding failed', error);
    
    // Fallback: Use basic coordinates
    return {
      formatted: `${lat}, ${lng}`,
      coordinates: [lng, lat]
    };
  }
};

// Blockchain fallback
const awardBadge = async (userId, badgeType) => {
  // Award badge in database immediately
  const badge = await Badge.create({
    user: userId,
    badgeType,
    earnedAt: new Date()
  });
  
  // Queue blockchain transaction for later
  try {
    const txHash = await mintBadgeToken(badge);
    badge.blockchainTxHash = txHash;
    await badge.save();
  } catch (error) {
    logger.error('Blockchain minting failed', error);
    // Badge still awarded, blockchain can be updated later
    await queueBlockchainMint(badge._id);
  }
  
  return badge;
};
```

#### 12.2.3 DDoS Attacks

**Risk:** Distributed denial of service attacks overwhelming the system

**Mitigation:**
```javascript
// 1. Rate Limiting (multiple layers)
const strictLimiter = rateLimit({
  windowMs: 1 * 60 * 1000,  // 1 minute
  max: 10,  // 10 requests per minute
  message: 'Too many requests'
});

// 2. IP-based blocking
const blockedIPs = new Set();

const ipBlocker = (req, res, next) => {
  if (blockedIPs.has(req.ip)) {
    return res.status(403).json({ error: 'Access denied' });
  }
  next();
};

// 3. Request validation
const validateRequest = (req, res, next) => {
  // Check for suspicious patterns
  const suspiciousPatterns = [
    /(\.\.|\/\/)/,  // Path traversal
    /<script>/i,    // XSS attempts
    /union.*select/i  // SQL injection
  ];
  
  const url = req.originalUrl;
  if (suspiciousPatterns.some(pattern => pattern.test(url))) {
    logger.warn('Suspicious request detected', { ip: req.ip, url });
    blockedIPs.add(req.ip);
    return res.status(403).json({ error: 'Invalid request' });
  }
  
  next();
};

// 4. CloudFlare/AWS Shield
// Use CDN with DDoS protection
// Configure WAF rules
```

**Additional Measures:**
- Use CloudFlare or AWS Shield for DDoS protection
- Implement CAPTCHA for suspicious activity
- Monitor traffic patterns
- Auto-scaling to handle legitimate traffic spikes

#### 12.2.4 Data Breach

**Risk:** Unauthorized access to sensitive user data

**Mitigation:**
```javascript
// 1. Encryption at rest
const encryptSensitiveData = (data) => {
  const cipher = crypto.createCipheriv(
    'aes-256-gcm',
    Buffer.from(process.env.ENCRYPTION_KEY, 'hex'),
    iv
  );
  // Encrypt sensitive fields
};

// 2. Audit logging
const logDataAccess = (userId, resource, action) => {
  AuditLog.create({
    user: userId,
    resource,
    action,
    timestamp: new Date(),
    ip: req.ip
  });
};

// 3. Access monitoring
const detectAnomalousAccess = async (userId) => {
  const recentAccess = await AuditLog.find({
    user: userId,
    timestamp: { $gte: new Date(Date.now() - 3600000) }
  });
  
  // Alert if unusual patterns
  if (recentAccess.length > 100) {
    await sendSecurityAlert(userId, 'Unusual access pattern detected');
  }
};

// 4. Data minimization
// Only collect necessary data
// Anonymize data where possible
// Regular data cleanup
```

**Incident Response Plan:**
1. Detect breach (monitoring alerts)
2. Contain breach (isolate affected systems)
3. Assess impact (identify compromised data)
4. Notify affected users (within 72 hours - GDPR)
5. Remediate vulnerabilities
6. Post-incident review

### 12.3 Operational Risks

#### 12.3.1 Insufficient Authority Engagement

**Risk:** Authorities don't actively use the platform to resolve issues

**Mitigation:**
- Comprehensive training program
- Dedicated support team
- Regular engagement meetings
- Performance metrics and reporting
- Incentive programs
- User-friendly admin interface

**Success Metrics:**
- Average response time < 24 hours
- Resolution rate > 80%
- Authority login frequency
- User satisfaction scores

#### 12.3.2 Low User Adoption

**Risk:** Citizens don't use the platform to report issues

**Mitigation:**
- Marketing and awareness campaigns
- Community outreach programs
- Partnerships with local organizations
- Gamification (badges, leaderboards)
- Success stories and testimonials
- Mobile-first design
- Multi-language support

**Growth Strategy:**
- Launch in pilot municipality
- Gather feedback and iterate
- Expand to neighboring areas
- Build network effects

#### 12.3.3 Content Moderation

**Risk:** Inappropriate or malicious content uploaded by users

**Mitigation:**
```javascript
// 1. Automated content filtering
const moderateContent = async (text) => {
  // Check for profanity
  const hasProfanity = profanityFilter.check(text);
  
  // Check for spam patterns
  const isSpam = spamDetector.analyze(text);
  
  if (hasProfanity || isSpam) {
    return {
      approved: false,
      reason: hasProfanity ? 'profanity' : 'spam'
    };
  }
  
  return { approved: true };
};

// 2. User reporting system
const reportIssue = async (issueId, reason) => {
  await Report.create({
    issue: issueId,
    reporter: req.userId,
    reason,
    status: 'pending'
  });
  
  // Auto-hide if multiple reports
  const reportCount = await Report.countDocuments({ issue: issueId });
  if (reportCount >= 3) {
    await Issue.findByIdAndUpdate(issueId, { hidden: true });
    // Notify moderators
  }
};

// 3. Manual review queue
// Admin dashboard shows flagged content
// Moderators can approve/reject/delete
```

**Moderation Workflow:**
1. Automated filtering on submission
2. User reports for problematic content
3. Manual review by moderators
4. Action taken (approve/remove/ban)
5. User notification

#### 12.3.4 Budget Overruns

**Risk:** Infrastructure and service costs exceed budget

**Mitigation:**
- Cost monitoring and alerts
- Usage quotas and limits
- Optimize resource utilization
- Reserved instances for predictable workload
- Auto-scaling policies
- Regular cost reviews

**Cost Optimization:**
```javascript
// Image optimization to reduce storage costs
const optimizeImage = async (file) => {
  return await cloudinary.uploader.upload(file.path, {
    quality: 'auto:good',  // Automatic quality optimization
    fetch_format: 'auto',  // Automatic format selection
    transformation: [
      { width: 1200, crop: 'limit' },  // Max width
      { quality: 80 }  // Compression
    ]
  });
};

// Database query optimization
// Use indexes, limit results, cache frequently accessed data

// CDN caching to reduce bandwidth
// Cache-Control headers for static assets
```

### 12.4 Business Risks

#### 12.4.1 Regulatory Compliance

**Risk:** Non-compliance with data protection regulations (GDPR, CCPA)

**Mitigation:**
- Legal review of privacy policy
- Data protection impact assessment
- User consent mechanisms
- Data retention policies
- Right to access/deletion implementation
- Regular compliance audits
- Data processing agreements with vendors

**Compliance Checklist:**
- [ ] Privacy policy published
- [ ] Cookie consent implemented
- [ ] Data export functionality
- [ ] Account deletion functionality
- [ ] Data retention policies
- [ ] Vendor agreements in place
- [ ] Security measures documented
- [ ] Breach notification procedures

#### 12.4.2 Liability Issues

**Risk:** Platform held liable for unresolved issues or false reports

**Mitigation:**
- Clear terms of service
- Disclaimer of liability
- Issue verification process
- User accountability measures
- Insurance coverage
- Legal counsel consultation

**Terms of Service Key Points:**
- Platform is a reporting tool, not responsible for resolution
- Users responsible for accuracy of reports
- Authorities responsible for issue resolution
- Platform not liable for damages from unresolved issues

### 12.5 Monitoring and Alerting

**Comprehensive Monitoring:**
```javascript
// 1. Application Performance Monitoring
const apm = require('elastic-apm-node').start({
  serviceName: 'civix-api',
  serverUrl: process.env.APM_SERVER_URL
});

// 2. Error Tracking
const Sentry = require('@sentry/node');
Sentry.init({ dsn: process.env.SENTRY_DSN });

// 3. Custom Metrics
const metrics = {
  issuesCreated: new Counter('issues_created_total'),
  apiLatency: new Histogram('api_request_duration_seconds'),
  activeUsers: new Gauge('active_users')
};

// 4. Health Checks
app.get('/health', async (req, res) => {
  const health = {
    status: 'ok',
    timestamp: Date.now(),
    uptime: process.uptime(),
    checks: {
      database: await checkDatabase(),
      redis: await checkRedis(),
      cloudinary: await checkCloudinary()
    }
  };
  
  const isHealthy = Object.values(health.checks).every(c => c.status === 'ok');
  res.status(isHealthy ? 200 : 503).json(health);
});

// 5. Alerting Rules
const alerts = {
  highErrorRate: {
    condition: 'error_rate > 5%',
    action: 'notify_team',
    channels: ['email', 'slack']
  },
  databaseDown: {
    condition: 'database_health == unhealthy',
    action: 'page_oncall',
    channels: ['pagerduty']
  },
  highLatency: {
    condition: 'p95_latency > 1000ms',
    action: 'notify_team',
    channels: ['slack']
  }
};
```

### 12.6 Disaster Recovery Plan

**Recovery Time Objective (RTO):** 4 hours  
**Recovery Point Objective (RPO):** 1 hour

**Disaster Scenarios:**

1. **Complete Data Center Failure**
   - Failover to backup region
   - Restore from latest backup
   - Update DNS to point to backup
   - Estimated recovery: 2-4 hours

2. **Database Corruption**
   - Stop write operations
   - Restore from point-in-time backup
   - Validate data integrity
   - Resume operations
   - Estimated recovery: 1-2 hours

3. **Security Breach**
   - Isolate affected systems
   - Assess damage
   - Restore from clean backup
   - Patch vulnerabilities
   - Notify users
   - Estimated recovery: 4-8 hours

**Backup Strategy:**
- Automated daily backups
- Continuous replication to secondary region
- Weekly backup testing
- 30-day retention period
- Encrypted backup storage

**Recovery Procedures:**
```bash
# Database restore procedure
1. Stop application servers
2. Restore MongoDB from backup
   mongorestore --uri="$MONGODB_URI" --archive=backup.archive
3. Verify data integrity
4. Start application servers
5. Monitor for issues
6. Notify users of restoration
```

### 12.7 Continuous Improvement

**Risk Management Process:**
1. **Identify** new risks regularly
2. **Assess** probability and impact
3. **Prioritize** based on severity
4. **Mitigate** with appropriate controls
5. **Monitor** effectiveness of controls
6. **Review** and update quarterly

**Key Performance Indicators:**
- System uptime: >99.5%
- Mean time to recovery (MTTR): <4 hours
- Security incidents: 0 per quarter
- Data loss incidents: 0
- Compliance violations: 0
- User satisfaction: >4.0/5.0

---

## 13. Conclusion

### 13.1 Design Summary

The CIVIX platform is designed as a robust, scalable, and secure civic engagement solution that bridges the gap between citizens and local authorities. The architecture follows modern best practices with:

- **Three-tier architecture** for clear separation of concerns
- **RESTful API design** for flexibility and maintainability
- **Real-time updates** via WebSocket for enhanced user experience
- **Comprehensive security** measures at multiple layers
- **Scalability** through horizontal scaling and caching
- **Reliability** through redundancy and monitoring

### 13.2 Technology Justification

**Frontend: React.js**
- Component-based architecture for reusability
- Large ecosystem and community support
- Excellent performance with virtual DOM
- Strong mobile responsiveness capabilities

**Backend: Node.js + Express**
- JavaScript full-stack development
- Non-blocking I/O for real-time features
- Extensive middleware ecosystem
- Easy integration with Socket.io

**Database: MongoDB**
- Flexible schema for evolving requirements
- Excellent geospatial query support
- Horizontal scalability
- JSON-like documents match JavaScript objects

**Image Storage: Cloudinary**
- Automatic optimization and transformation
- Global CDN for fast delivery
- Generous free tier
- Easy integration

**Maps: Mapbox**
- Customizable map styles
- Accurate geocoding
- Reasonable pricing
- Good documentation

### 13.3 Success Criteria

The platform will be considered successful when:

1. **User Adoption**
   - 1,000+ registered users in first 3 months
   - 100+ issues reported per month
   - 70%+ user retention rate

2. **Authority Engagement**
   - 80%+ of issues receive response within 48 hours
   - 70%+ resolution rate
   - Average resolution time < 7 days

3. **Technical Performance**
   - 99.5%+ uptime
   - <2s page load time
   - <200ms API response time
   - Zero data breaches

4. **User Satisfaction**
   - 4.0+ rating on app stores
   - 80%+ positive feedback
   - <5% complaint rate

### 13.4 Next Steps

**Phase 1: Development (Months 1-3)**
- Set up development environment
- Implement core features
- Unit and integration testing
- Security audit

**Phase 2: Testing (Month 4)**
- User acceptance testing
- Performance testing
- Security penetration testing
- Bug fixes and optimization

**Phase 3: Pilot Launch (Month 5)**
- Deploy to staging environment
- Pilot with one municipality
- Gather user feedback
- Iterate based on feedback

**Phase 4: Production Launch (Month 6)**
- Deploy to production
- Marketing and user onboarding
- Monitor performance
- Provide support

**Phase 5: Growth (Months 7-12)**
- Expand to more municipalities
- Add new features based on feedback
- Optimize performance
- Scale infrastructure

### 13.5 Future Enhancements

**Short-term (6-12 months):**
- Native mobile apps (iOS/Android)
- Push notifications
- Advanced analytics dashboard
- AI-powered issue categorization
- Multi-language support

**Long-term (12-24 months):**
- Integration with municipal work order systems
- Predictive maintenance using ML
- Augmented reality issue visualization
- Public API for third-party integrations
- Multi-tenancy for multiple cities

---

## Appendix

### A. Glossary

- **API**: Application Programming Interface
- **CDN**: Content Delivery Network
- **CORS**: Cross-Origin Resource Sharing
- **CSRF**: Cross-Site Request Forgery
- **GDPR**: General Data Protection Regulation
- **JWT**: JSON Web Token
- **ODM**: Object Document Mapper
- **RBAC**: Role-Based Access Control
- **REST**: Representational State Transfer
- **SPA**: Single Page Application
- **SSL/TLS**: Secure Sockets Layer / Transport Layer Security
- **XSS**: Cross-Site Scripting

### B. References

1. MongoDB Documentation: https://docs.mongodb.com/
2. Express.js Guide: https://expressjs.com/
3. React Documentation: https://react.dev/
4. Mapbox API: https://docs.mapbox.com/
5. Cloudinary Documentation: https://cloudinary.com/documentation
6. OWASP Top 10: https://owasp.org/www-project-top-ten/
7. GDPR Guidelines: https://gdpr.eu/
8. JWT Best Practices: https://tools.ietf.org/html/rfc8725

### C. Contact Information

**Development Team:**
- Project Lead: [Name]
- Backend Lead: [Name]
- Frontend Lead: [Name]
- DevOps Lead: [Name]

**Support:**
- Email: support@civix.com
- Documentation: https://docs.civix.com
- GitHub: https://github.com/civix/platform

---

**Document End**

*This design document is a living document and will be updated as the project evolves.*

