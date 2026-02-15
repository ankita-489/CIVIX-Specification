
# Software Requirements Specification: CIVIX

## 1. Introduction

### 1.1 Purpose
This document specifies the software requirements for CIVIX, a smart civic issue reporting platform designed to bridge the communication gap between citizens and local authorities regarding public infrastructure and civic issues.

### 1.2 Scope
CIVIX is a web-based platform that enables citizens to report, track, and engage with civic issues in their communities. The system provides real-time issue tracking, geolocation services, community engagement features, and an administrative dashboard for authorities to manage and resolve reported issues.

### 1.3 Definitions and Acronyms
- **SRS**: Software Requirements Specification
- **Geotag**: Geographic metadata attached to media files
- **Admin**: Administrative user representing local authorities
- **Citizen**: End-user who reports and engages with civic issues
- **Issue**: A reported civic problem requiring attention
- **Badge**: Blockchain-based digital credential for user contributions

### 1.4 References
- Web Content Accessibility Guidelines (WCAG) 2.1
- General Data Protection Regulation (GDPR)
- Blockchain standards for digital credentials

### 1.5 Overview
This document outlines the problem definition, objectives, user roles, functional and non-functional requirements, system constraints, assumptions, and acceptance criteria for the CIVIX platform.

## 2. Problem Definition

### 2.1 Current Challenges
Citizens face numerous public infrastructure issues daily, including:
- Potholes on roads
- Broken or non-functional streetlights
- Water leaks and pipe bursts
- Accumulated garbage and waste
- Damaged public property
- Unsafe pedestrian crossings

### 2.2 Existing Gaps
- **Lack of Structured Reporting**: No centralized system for citizens to report issues
- **Zero Transparency**: Citizens cannot track the status of their complaints
- **Low Accountability**: Authorities often ignore or delay individual complaints
- **No Community Engagement**: Citizens cannot see what issues others are facing
- **Poor Communication**: No feedback loop between authorities and citizens
- **Duplicate Efforts**: Multiple people report the same issue without coordination

### 2.3 Impact
These gaps result in:
- Prolonged resolution times for civic issues
- Citizen frustration and disengagement
- Inefficient resource allocation by authorities
- Deteriorating public infrastructure
- Reduced quality of life in communities

## 3. Objectives

### 3.1 Primary Objectives
1. **Simplify Issue Reporting**: Enable citizens to report civic issues with minimal effort
2. **Increase Transparency**: Provide real-time visibility into issue status and resolution progress
3. **Enhance Accountability**: Create a public record of issues and authority responses
4. **Foster Community Engagement**: Allow citizens to collaborate on identifying and prioritizing issues
5. **Streamline Authority Workflow**: Provide tools for efficient issue management and resolution

### 3.2 Secondary Objectives
1. Incentivize civic participation through blockchain-based badges
2. Reduce duplicate reporting through public issue visibility
3. Enable data-driven decision making for urban planning
4. Build trust between citizens and local authorities
5. Create a historical record of civic infrastructure maintenance

## 4. User Roles

### 4.1 Citizen (Reporter)
**Description**: General public users who report and engage with civic issues

**Capabilities**:
- Register and authenticate on the platform
- Report new civic issues
- Upload photo evidence
- View issues on a public map
- Vote on existing issues
- Comment on issues
- Track personal submissions
- Receive notifications on issue updates
- Earn and view contribution badges

**Access Level**: Standard user with read and limited write permissions

### 4.2 Authority (Admin)
**Description**: Government officials and municipal workers responsible for resolving issues

**Capabilities**:
- Access administrative dashboard
- View all reported issues
- Filter and search issues by type, location, status, priority
- Update issue status (Pending, In Progress, Resolved, Rejected)
- Assign issues to departments or personnel
- Add resolution notes and updates
- Upload completion photos
- Generate reports and analytics
- Communicate with citizens through the platform

**Access Level**: Administrative user with full read and write permissions

### 4.3 System Administrator
**Description**: Technical personnel managing the platform

**Capabilities**:
- Manage user accounts
- Configure system settings
- Monitor platform performance
- Manage issue categories
- Moderate content
- Access system logs
- Manage blockchain integration

**Access Level**: Full system access

## 5. Functional Requirements

### 5.1 User Authentication and Authorization

#### 5.1.1 User Registration
- **FR-1.1**: System shall allow citizens to register using email and password
- **FR-1.2**: System shall validate email addresses through verification links
- **FR-1.3**: System shall enforce password strength requirements (minimum 8 characters, mix of letters, numbers, symbols)
- **FR-1.4**: System shall support optional profile information (name, phone, address)

#### 5.1.2 User Login
- **FR-1.5**: System shall authenticate users with email and password
- **FR-1.6**: System shall provide "Remember Me" functionality
- **FR-1.7**: System shall implement password reset via email
- **FR-1.8**: System shall support session management with automatic timeout after 30 minutes of inactivity

#### 5.1.3 Authorization
- **FR-1.9**: System shall enforce role-based access control (Citizen, Authority, Admin)
- **FR-1.10**: System shall restrict administrative functions to authorized users only

### 5.2 Issue Reporting

#### 5.2.1 Create Issue Report
- **FR-2.1**: System shall allow citizens to create new issue reports with a single tap/click
- **FR-2.2**: System shall require the following mandatory fields:
  - Issue title
  - Issue category (dropdown: Pothole, Streetlight, Water Leak, Garbage, Other)
  - Description
  - Location (auto-geotagged or manually selected)
- **FR-2.3**: System shall support optional fields:
  - Photo upload (up to 5 images, max 5MB each)
  - Severity level (Low, Medium, High)
  - Additional notes

#### 5.2.2 Geolocation
- **FR-2.4**: System shall automatically capture GPS coordinates when reporting an issue
- **FR-2.5**: System shall allow users to manually adjust location on a map interface
- **FR-2.6**: System shall validate that coordinates fall within supported service areas
- **FR-2.7**: System shall extract address information from coordinates (reverse geocoding)

#### 5.2.3 Photo Upload
- **FR-2.8**: System shall support image formats: JPEG, PNG, HEIC
- **FR-2.9**: System shall automatically compress images to optimize storage
- **FR-2.10**: System shall extract and store EXIF metadata (timestamp, location if available)
- **FR-2.11**: System shall display image preview before submission

#### 5.2.4 Submission
- **FR-2.12**: System shall assign a unique tracking ID to each issue
- **FR-2.13**: System shall timestamp all submissions
- **FR-2.14**: System shall send confirmation notification to the reporter
- **FR-2.15**: System shall set initial status as "Pending"

### 5.3 Issue Viewing and Discovery

#### 5.3.1 Public Map View
- **FR-3.1**: System shall display all public issues on an interactive map
- **FR-3.2**: System shall use distinct markers for different issue categories
- **FR-3.3**: System shall color-code markers by status (Pending, In Progress, Resolved)
- **FR-3.4**: System shall support map zoom and pan functionality
- **FR-3.5**: System shall show issue summary on marker click
- **FR-3.6**: System shall cluster nearby markers at lower zoom levels

#### 5.3.2 Issue List View
- **FR-3.7**: System shall provide a list view of all issues
- **FR-3.8**: System shall support filtering by:
  - Category
  - Status
  - Date range
  - Location/area
  - Severity
- **FR-3.9**: System shall support sorting by:
  - Date (newest/oldest)
  - Vote count
  - Proximity to user
- **FR-3.10**: System shall implement pagination (20 issues per page)

#### 5.3.3 Issue Detail View
- **FR-3.11**: System shall display complete issue information:
  - Title, description, category
  - Photos
  - Location (map and address)
  - Status and timeline
  - Reporter information (anonymized option)
  - Vote count
  - Comments
  - Resolution notes (if resolved)
- **FR-3.12**: System shall show status history with timestamps

### 5.4 Community Engagement

#### 5.4.1 Voting
- **FR-4.1**: System shall allow authenticated users to upvote issues
- **FR-4.2**: System shall limit each user to one vote per issue
- **FR-4.3**: System shall allow users to remove their vote
- **FR-4.4**: System shall display total vote count for each issue
- **FR-4.5**: System shall use vote count as a priority indicator

#### 5.4.2 Commenting
- **FR-4.6**: System shall allow authenticated users to comment on issues
- **FR-4.7**: System shall support text comments (max 500 characters)
- **FR-4.8**: System shall timestamp all comments
- **FR-4.9**: System shall allow users to edit their own comments within 15 minutes
- **FR-4.10**: System shall allow users to delete their own comments
- **FR-4.11**: System shall display comments in chronological order
- **FR-4.12**: System shall notify issue reporter of new comments

### 5.5 Notifications

#### 5.5.1 Real-Time Updates
- **FR-5.1**: System shall notify reporters when their issue status changes
- **FR-5.2**: System shall notify users when issues they voted on are updated
- **FR-5.3**: System shall notify users when someone comments on their issue
- **FR-5.4**: System shall support notification delivery via:
  - In-app notifications
  - Email notifications (optional, user preference)

#### 5.5.2 Notification Management
- **FR-5.5**: System shall allow users to view notification history
- **FR-5.6**: System shall allow users to mark notifications as read
- **FR-5.7**: System shall allow users to configure notification preferences
- **FR-5.8**: System shall clear notifications older than 30 days

### 5.6 Blockchain-Based Badges

#### 5.6.1 Badge Earning
- **FR-6.1**: System shall award badges for user contributions:
  - First Report: Submit first issue
  - Active Reporter: Submit 10 issues
  - Community Champion: Submit 50 issues
  - Engaged Citizen: Vote on 20 issues
  - Conversationalist: Comment on 30 issues
  - Problem Solver: Report 5 issues that get resolved
- **FR-6.2**: System shall record badge awards on blockchain
- **FR-6.3**: System shall generate unique badge tokens with metadata

#### 5.6.2 Badge Display
- **FR-6.4**: System shall display earned badges on user profiles
- **FR-6.5**: System shall show badge progress indicators
- **FR-6.6**: System shall provide badge verification via blockchain explorer link
- **FR-6.7**: System shall allow users to share badges on social media

### 5.7 Authority Dashboard

#### 5.7.1 Issue Management
- **FR-7.1**: System shall provide a dashboard view of all issues
- **FR-7.2**: System shall display key metrics:
  - Total issues
  - Pending issues
  - In-progress issues
  - Resolved issues (by time period)
  - Average resolution time
  - Issues by category
- **FR-7.3**: System shall allow authorities to update issue status
- **FR-7.4**: System shall require resolution notes when marking issues as resolved
- **FR-7.5**: System shall allow authorities to upload completion photos
- **FR-7.6**: System shall allow authorities to reject issues with justification

#### 5.7.2 Assignment and Workflow
- **FR-7.7**: System shall allow authorities to assign issues to departments
- **FR-7.8**: System shall allow authorities to set priority levels
- **FR-7.9**: System shall allow authorities to add internal notes (not visible to public)
- **FR-7.10**: System shall track time spent in each status

#### 5.7.3 Communication
- **FR-7.11**: System shall allow authorities to post public updates on issues
- **FR-7.12**: System shall allow authorities to request additional information from reporters
- **FR-7.13**: System shall notify reporters when authorities add updates

#### 5.7.4 Reporting and Analytics
- **FR-7.14**: System shall generate reports on:
  - Issues by category over time
  - Resolution rates by department
  - Average resolution time by category
  - Geographic distribution of issues
  - Most voted issues
- **FR-7.15**: System shall export reports in CSV and PDF formats
- **FR-7.16**: System shall provide data visualization (charts, graphs)

### 5.8 Search Functionality
- **FR-8.1**: System shall provide search functionality for issues
- **FR-8.2**: System shall support search by:
  - Keywords in title/description
  - Location/address
  - Issue ID
  - Category
- **FR-8.3**: System shall display search results with relevance ranking
- **FR-8.4**: System shall highlight search terms in results

### 5.9 User Profile Management
- **FR-9.1**: System shall allow users to view their profile
- **FR-9.2**: System shall display user statistics:
  - Issues reported
  - Issues voted on
  - Comments made
  - Badges earned
- **FR-9.3**: System shall allow users to edit profile information
- **FR-9.4**: System shall allow users to change password
- **FR-9.5**: System shall allow users to delete their account
- **FR-9.6**: System shall provide privacy settings (public/anonymous reporting)

## 6. Non-Functional Requirements

### 6.1 Performance
- **NFR-1.1**: System shall load the home page within 2 seconds on standard broadband connection
- **NFR-1.2**: System shall display map with 100 markers within 3 seconds
- **NFR-1.3**: System shall process issue submission within 5 seconds
- **NFR-1.4**: System shall support at least 1,000 concurrent users
- **NFR-1.5**: System shall handle image upload and processing within 10 seconds

### 6.2 Scalability
- **NFR-2.1**: System architecture shall support horizontal scaling
- **NFR-2.2**: System shall handle up to 10,000 issues without performance degradation
- **NFR-2.3**: System shall support addition of new municipalities without code changes
- **NFR-2.4**: Database shall be optimized for read-heavy operations

### 6.3 Availability
- **NFR-3.1**: System shall maintain 99.5% uptime
- **NFR-3.2**: System shall implement automated health checks
- **NFR-3.3**: System shall provide graceful degradation if external services fail
- **NFR-3.4**: Planned maintenance shall be scheduled during low-usage hours

### 6.4 Security
- **NFR-4.1**: System shall encrypt all data in transit using TLS 1.3
- **NFR-4.2**: System shall encrypt sensitive data at rest
- **NFR-4.3**: System shall implement protection against common vulnerabilities (SQL injection, XSS, CSRF)
- **NFR-4.4**: System shall enforce rate limiting on API endpoints
- **NFR-4.5**: System shall log all authentication attempts
- **NFR-4.6**: System shall implement secure session management
- **NFR-4.7**: System shall comply with GDPR data protection requirements
- **NFR-4.8**: System shall sanitize all user inputs

### 6.5 Usability
- **NFR-5.1**: System shall be accessible on desktop and mobile browsers
- **NFR-5.2**: System shall follow responsive design principles
- **NFR-5.3**: System shall comply with WCAG 2.1 Level AA accessibility standards
- **NFR-5.4**: System shall provide intuitive navigation with maximum 3 clicks to any feature
- **NFR-5.5**: System shall display helpful error messages with recovery suggestions
- **NFR-5.6**: System shall support multiple languages (initially English, expandable)

### 6.6 Reliability
- **NFR-6.1**: System shall implement automated backups daily
- **NFR-6.2**: System shall maintain backup retention for 30 days
- **NFR-6.3**: System shall implement error logging and monitoring
- **NFR-6.4**: System shall recover from failures without data loss
- **NFR-6.5**: System shall validate all data inputs

### 6.7 Maintainability
- **NFR-7.1**: Code shall follow established coding standards and style guides
- **NFR-7.2**: System shall include comprehensive API documentation
- **NFR-7.3**: System shall implement modular architecture for easy updates
- **NFR-7.4**: System shall include automated testing with minimum 80% code coverage
- **NFR-7.5**: System shall use version control for all code

### 6.8 Compatibility
- **NFR-8.1**: System shall support modern browsers (Chrome, Firefox, Safari, Edge - latest 2 versions)
- **NFR-8.2**: System shall be compatible with iOS 14+ and Android 10+
- **NFR-8.3**: System shall degrade gracefully on older browsers
- **NFR-8.4**: System shall work with screen readers and assistive technologies

### 6.9 Data Integrity
- **NFR-9.1**: System shall validate all data before storage
- **NFR-9.2**: System shall maintain referential integrity in database
- **NFR-9.3**: System shall implement transaction management for critical operations
- **NFR-9.4**: System shall prevent duplicate issue submissions (same location, category within 24 hours)

## 7. System Constraints

### 7.1 Technical Constraints
- **C-1.1**: System must be web-based (no native mobile app in initial release)
- **C-1.2**: System must integrate with existing blockchain infrastructure for badges
- **C-1.3**: System must use standard mapping APIs (Google Maps, OpenStreetMap, or similar)
- **C-1.4**: System must support standard image formats only (JPEG, PNG, HEIC)
- **C-1.5**: System must operate within cloud infrastructure budget constraints

### 7.2 Regulatory Constraints
- **C-2.1**: System must comply with data protection regulations (GDPR, local privacy laws)
- **C-2.2**: System must implement data retention policies per legal requirements
- **C-2.3**: System must provide data export functionality for user data requests
- **C-2.4**: System must obtain user consent for location tracking
- **C-2.5**: System must comply with accessibility regulations

### 7.3 Business Constraints
- **C-3.1**: Initial deployment limited to specific municipalities or regions
- **C-3.2**: System must be operational within 6 months of project start
- **C-3.3**: System must work with existing municipal IT infrastructure
- **C-3.4**: Training materials must be provided for authority users

### 7.4 Resource Constraints
- **C-4.1**: Development team size limited to available resources
- **C-4.2**: Infrastructure costs must remain within allocated budget
- **C-4.3**: Third-party service costs (mapping, blockchain) must be optimized

## 8. Assumptions

### 8.1 User Assumptions
- **A-1.1**: Citizens have access to internet-connected devices (smartphones, computers)
- **A-1.2**: Citizens are willing to register and create accounts
- **A-1.3**: Citizens will provide accurate information when reporting issues
- **A-1.4**: Authority users have basic computer literacy
- **A-1.5**: Users have modern web browsers with JavaScript enabled

### 8.2 Technical Assumptions
- **A-2.1**: Reliable internet connectivity is available in service areas
- **A-2.2**: GPS/location services are available on user devices
- **A-2.3**: Third-party services (mapping, blockchain) will remain available and stable
- **A-2.4**: Cloud infrastructure will provide required performance and reliability
- **A-2.5**: Blockchain network will process badge transactions within reasonable time

### 8.3 Operational Assumptions
- **A-3.1**: Authorities will actively monitor and respond to reported issues
- **A-3.2**: Authorities will update issue status in a timely manner
- **A-3.3**: Municipal departments will cooperate with the platform
- **A-3.4**: System administrators will be available for maintenance and support
- **A-3.5**: User support resources will be available to handle inquiries

### 8.4 Data Assumptions
- **A-4.1**: Geographic boundaries of service areas are clearly defined
- **A-4.2**: Issue categories adequately cover common civic problems
- **A-4.3**: Historical issue data (if any) can be migrated to the system
- **A-4.4**: User-generated content will be generally appropriate (with moderation)

## 9. Future Enhancements

### 9.1 Phase 2 Features
- **FE-1.1**: Native mobile applications (iOS and Android)
- **FE-1.2**: Offline mode with sync when connection restored
- **FE-1.3**: Push notifications for mobile apps
- **FE-1.4**: Voice-based issue reporting
- **FE-1.5**: AI-powered issue categorization and duplicate detection
- **FE-1.6**: Predictive analytics for infrastructure maintenance
- **FE-1.7**: Integration with municipal work order systems
- **FE-1.8**: Chatbot for common queries

### 9.2 Phase 3 Features
- **FE-2.1**: Augmented reality for issue visualization
- **FE-2.2**: Crowdsourced issue verification
- **FE-2.3**: Gamification with leaderboards and challenges
- **FE-2.4**: Social media integration for issue sharing
- **FE-2.5**: Public API for third-party integrations
- **FE-2.6**: Advanced analytics dashboard with ML insights
- **FE-2.7**: Multi-tenancy support for multiple cities
- **FE-2.8**: Citizen satisfaction surveys post-resolution

### 9.3 Long-Term Vision
- **FE-3.1**: Integration with smart city IoT sensors
- **FE-3.2**: Automated issue detection from city cameras
- **FE-3.3**: Blockchain-based voting for budget allocation
- **FE-3.4**: Decentralized governance features
- **FE-3.5**: Cross-municipality issue tracking and collaboration

## 10. Acceptance Criteria

### 10.1 User Registration and Authentication
- **AC-1.1**: A new user can successfully register with valid email and password
- **AC-1.2**: User receives verification email and can activate account
- **AC-1.3**: Registered user can log in with correct credentials
- **AC-1.4**: User cannot log in with incorrect credentials
- **AC-1.5**: User can reset password via email link
- **AC-1.6**: Session expires after 30 minutes of inactivity

### 10.2 Issue Reporting
- **AC-2.1**: Authenticated user can create a new issue report with all required fields
- **AC-2.2**: System automatically captures GPS coordinates when reporting
- **AC-2.3**: User can upload up to 5 photos with an issue
- **AC-2.4**: System assigns unique tracking ID to each issue
- **AC-2.5**: User receives confirmation notification after submission
- **AC-2.6**: Newly created issue appears on public map within 10 seconds
- **AC-2.7**: System prevents submission with missing required fields

### 10.3 Map and Issue Discovery
- **AC-3.1**: Public map displays all issues with appropriate markers
- **AC-3.2**: Clicking a marker shows issue summary popup
- **AC-3.3**: Map markers are color-coded by status
- **AC-3.4**: User can filter issues by category and status
- **AC-3.5**: User can search for issues by keyword or location
- **AC-3.6**: Issue list view displays paginated results

### 10.4 Community Engagement
- **AC-4.1**: Authenticated user can upvote an issue
- **AC-4.2**: User can remove their vote from an issue
- **AC-4.3**: Vote count updates immediately and displays correctly
- **AC-4.4**: User can post a comment on an issue
- **AC-4.5**: Comments appear in chronological order
- **AC-4.6**: Issue reporter receives notification when someone comments

### 10.5 Notifications
- **AC-5.1**: User receives notification when their issue status changes
- **AC-5.2**: User receives notification for comments on their issues
- **AC-5.3**: Notifications appear in notification center
- **AC-5.4**: User can mark notifications as read
- **AC-5.5**: Email notifications are sent based on user preferences

### 10.6 Blockchain Badges
- **AC-6.1**: User earns "First Report" badge after submitting first issue
- **AC-6.2**: Badge appears on user profile immediately after earning
- **AC-6.3**: Badge is recorded on blockchain with verifiable transaction
- **AC-6.4**: User can view badge verification link
- **AC-6.5**: Badge progress indicators show current status toward next badge

### 10.7 Authority Dashboard
- **AC-7.1**: Authority user can access admin dashboard
- **AC-7.2**: Dashboard displays key metrics accurately
- **AC-7.3**: Authority can update issue status with resolution notes
- **AC-7.4**: Authority can assign issues to departments
- **AC-7.5**: Authority can upload completion photos
- **AC-7.6**: Status updates trigger notifications to relevant users
- **AC-7.7**: Authority can generate and export reports

### 10.8 Performance
- **AC-8.1**: Home page loads within 2 seconds on standard connection
- **AC-8.2**: Map with 100 markers loads within 3 seconds
- **AC-8.3**: Issue submission completes within 5 seconds
- **AC-8.4**: System supports 1,000 concurrent users without degradation
- **AC-8.5**: Image upload processes within 10 seconds

### 10.9 Security
- **AC-9.1**: All data transmission uses HTTPS encryption
- **AC-9.2**: Passwords are hashed and never stored in plain text
- **AC-9.3**: System prevents SQL injection attacks
- **AC-9.4**: System prevents XSS attacks
- **AC-9.5**: Rate limiting prevents API abuse
- **AC-9.6**: User sessions are secure and properly managed

### 10.10 Usability
- **AC-10.1**: Platform is fully functional on desktop browsers
- **AC-10.2**: Platform is fully functional on mobile browsers
- **AC-10.3**: All features are accessible within 3 clicks
- **AC-10.4**: Error messages are clear and actionable
- **AC-10.5**: Platform meets WCAG 2.1 Level AA standards
- **AC-10.6**: Platform works with screen readers

### 10.11 Data Integrity
- **AC-11.1**: System validates all input data before storage
- **AC-11.2**: System prevents duplicate issue submissions for same location/category within 24 hours
- **AC-11.3**: All database operations maintain referential integrity
- **AC-11.4**: System performs daily automated backups
- **AC-11.5**: User data can be exported on request

---

**Document Version**: 1.0  
**Last Updated**: February 15, 2026  
**Status**: Draft for Review


