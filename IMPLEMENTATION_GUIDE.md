# আমার দোকান - Complete Implementation Guide

## ⚠️ CRITICAL: This is a PRODUCTION-READY architecture document

This guide provides the complete blueprint for building the entire system. Due to the massive scope, this cannot be completed in a single response. However, all critical workflows are documented here.

## PHASE 1: Firebase Setup

### 1.1 Create Firebase Project
- Go to https://console.firebase.google.com
- Create new project: "amar-dokan"
- Enable Firestore, Authentication, Storage, Cloud Functions, Cloud Messaging

### 1.2 Authentication Setup
- Enable Email/Password
- Enable Phone Authentication
- Enable Google Sign-in

### 1.3 Firestore Setup
- Create collections as per firebase-schema.md
- Set indexes for all collections
- Deploy Security Rules from firestore.rules

## PHASE 2: Android App (Kotlin)

### 2.1 Project Structure
```
amar-dokan-android/
├── app/
│   ├── src/main/
│   │   ├── java/com/amardokan/
│   │   │   ├── auth/
│   │   │   │   ├── AuthManager.kt
│   │   │   │   ├── LoginActivity.kt
│   │   │   │   ├── GmailLoginActivity.kt
│   │   │   │   └── ApprovalWaitActivity.kt
│   │   │   ├── home/
│   │   │   │   ├── DashboardActivity.kt
│   │   │   │   ├── ProductActivity.kt
│   │   │   │   ├── CustomerActivity.kt
│   │   │   │   ├── SalesActivity.kt
│   │   │   │   └── ReportActivity.kt
│   │   │   ├── admin/
│   │   │   │   ├── AdminDashboardActivity.kt
│   │   │   │   ├── LoginRequestsActivity.kt
│   │   │   │   ├── PaymentRequestsActivity.kt
│   │   │   │   ├── SmsRequestsActivity.kt
│   │   │   │   └── UserManagementActivity.kt
│   │   │   ├── messaging/
│   │   │   │   ├── FCMService.kt
│   │   │   │   ├── NotificationManager.kt
│   │   │   │   └── NotificationHandler.kt
│   │   │   ├── data/
│   │   │   │   ├── FirestoreManager.kt
│   │   │   │   ├── LocalPreferences.kt
│   │   │   │   └── SyncManager.kt
│   │   │   ├── models/
│   │   │   │   ├── User.kt
│   │   │   │   ├── Shop.kt
│   │   │   │   ├── Device.kt
│   │   │   │   ├── LoginRequest.kt
│   │   │   │   ├── PaymentRequest.kt
│   │   │   │   ├── SmsRequest.kt
│   │   │   │   └── ... (other models)
│   │   │   └── MainActivity.kt
│   │   ├── res/
│   │   │   ├── layout/
│   │   │   ├── drawable/
│   │   │   ├── values/
│   │   │   └── menu/
│   │   └── AndroidManifest.xml
│   ├── build.gradle
│   └── google-services.json
├── build.gradle
└── settings.gradle
```

### 2.2 Key Dependencies (build.gradle)
```gradle
dependencies {
    // Firebase
    implementation 'com.google.firebase:firebase-auth-ktx'
    implementation 'com.google.firebase:firebase-firestore-ktx'
    implementation 'com.google.firebase:firebase-messaging-ktx'
    implementation 'com.google.firebase:firebase-storage-ktx'
    
    // Google Sign-in
    implementation 'com.google.android.gms:play-services-auth'
    
    // Kotlin
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android'
    
    // Networking
    implementation 'com.squareup.okhttp3:okhttp'
    implementation 'com.squareup.retrofit2:retrofit'
    
    // UI
    implementation 'androidx.appcompat:appcompat'
    implementation 'com.google.android.material:material'
    implementation 'androidx.constraintlayout:constraintlayout'
    
    // Local Storage
    implementation 'androidx.datastore:datastore-preferences'
    implementation 'androidx.room:room-runtime'
}
```

### 2.3 Critical Workflows

#### LOGIN FLOW (Mobile Number)
```
1. User enters phone number
2. Firebase Phone Auth sends OTP (or direct verification)
3. User enters OTP
4. Firebase creates auth token
5. App calls handleLogin() Cloud Function
6. Cloud Function:
   - Creates device record
   - Creates login_request with status=PENDING
   - Sends FCM to owner/admins
   - Creates notification record
7. App shows "Awaiting Approval" screen
8. Owner approves in admin panel
9. App polls for approval status
10. Once approved, user gets access
```

#### GMAIL LOGIN FLOW
```
1. User taps "Sign in with Google"
2. Google Sign-in opens
3. User selects account
4. Firebase authenticates
5. App calls handleLogin() Cloud Function
6. Same flow as mobile login
7. IMPORTANT: Gmail doesn't bypass approval
```

#### FCM NOTIFICATION HANDLING
```
1. Firebase Cloud Messaging receives notification
2. FCMService.onMessageReceived() called
3. App checks notification type
4. Handles based on type:
   - login_request: Open LoginRequestsActivity
   - payment_request: Open PaymentRequestsActivity
   - sms_request: Open SmsRequestsActivity
5. Notification works in:
   - Foreground: Custom notification UI
   - Background: System notification
   - Killed: System notification + intent
```

## PHASE 3: Web Admin Panel

### 3.1 Tech Stack
- React + TypeScript
- Firebase SDK
- Tailwind CSS
- Zustand (state management)
- React Router
- Axios

### 3.2 Project Structure
```
amar-dokan-admin/
├── src/
│   ├── components/
│   │   ├── Layout/
│   │   ├── Dashboard/
│   │   ├── Users/
│   │   ├── Devices/
│   │   ├── LoginRequests/
│   │   ├── PaymentRequests/
│   │   ├── SmsRequests/
│   │   ├── Products/
│   │   ├── Customers/
│   │   ├── Reports/
│   │   └── Settings/
│   ├── pages/
│   ├── services/
│   │   ├── firebase.ts
│   │   ├── auth.ts
│   │   ├── firestore.ts
│   │   └── notifications.ts
│   ├── store/
│   │   ├── authStore.ts
│   │   ├── shopStore.ts
│   │   └── uiStore.ts
│   ├── types/
│   │   └── index.ts
│   ├── App.tsx
│   └── main.tsx
├── index.html
└── vite.config.ts
```

### 3.3 Critical Components

#### LoginRequests Component
```tsx
// Shows all pending login requests
// Features:
// - Real-time updates from Firestore
// - Approve/Reject buttons
// - Device info display
// - User info display
```

#### PaymentRequests Component
```tsx
// Shows all pending payment requests
// Features:
// - Package info
// - Payment method
// - Transaction ID
// - Approve/Reject buttons
// - Auto-updates credit wallet on approval
```

#### SmsRequests Component
```tsx
// Shows all pending SMS requests
// Features:
// - Customer name and phone
// - Message preview
// - Send/Reject buttons
// - Credit deduction on send
```

## PHASE 4: Cloud Functions (Node.js/TypeScript)

### 4.1 Deployed Functions

#### Authentication Functions
- `onUserCreated`: Triggered when Firebase Auth creates user
- `handleLogin`: Callable function for login requests
- `approveLoginRequest`: Callable for admin approval
- `rejectLoginRequest`: Callable for admin rejection

#### Payment Functions
- `handlePaymentRequest`: Create payment request
- `approvePaymentRequest`: Approve and add credit (with transaction)
- `rejectPaymentRequest`: Reject payment request

#### SMS Functions
- `createSmsRequest`: Create SMS request
- `approveSmsRequest`: Approve and deduct credit
- `rejectSmsRequest`: Reject SMS request

#### Notification Functions
- `notifyAdminsAboutLoginRequest`: Send FCM + create DB notification
- `notifyAdminsAboutPaymentRequest`: Send FCM + create DB notification
- `notifyAdminsAboutSmsRequest`: Send FCM + create DB notification

### 4.2 Key Principles

**Atomicity**: Use Firestore transactions for all multi-step operations
```
- Payment approval: Update wallet + create transaction + update request
- SMS approval: Deduct credit + create transaction + update request
```

**Idempotency**: Prevent duplicate processing
```
- Check request status before processing
- Use transaction IDs
- Fail fast if already processed
```

**Notifications**: Database-first, FCM second
```
1. Save request to Firestore
2. Send FCM notification
3. If FCM fails, request still in DB
4. Admin can see in pending requests
```

## PHASE 5: Security Implementation

### 5.1 Firestore Security Rules
- All reads/writes must include shop_id
- Multi-tenant isolation enforced at DB level
- Sensitive operations (approval, credit) only by admin
- User can only read own data

### 5.2 Authentication
- Firebase Auth handles user creation
- Custom claims for role-based access
- Device token validation
- Session management

### 5.3 Backend Validation
- All client requests validated server-side
- Permission checks in Cloud Functions
- Shop membership verification
- Transaction atomicity

## PHASE 6: Critical Workflows - STEP BY STEP

### WORKFLOW 1: Gmail New Device Login

**Step 1: User taps "Sign in with Google"**
```
Android App → Google Sign-in Dialog
```

**Step 2: Google authentication**
```
User selects account → Firebase Auth verifies → Creates/Updates Auth user
```

**Step 3: App gets auth token**
```
Firebase Auth returns UID, email, name, photo
```

**Step 4: App calls handleLogin() Cloud Function**
```
Device ID: UUID.randomUUID().toString()
Device Name: Build.MODEL
Platform: "android"
App Version: BuildConfig.VERSION_NAME
FCM Token: From FirebaseMessaging.getInstance().token
OS Version: Build.VERSION.RELEASE

Cloud Function checks:
- User exists in Firestore
- Shop assigned to user
- Device already exists?
  - If yes: Update FCM token
  - If no: Create new device record
- Create login_request with status=PENDING
- Send FCM to owner/admins
- Create notification record
```

**Step 5: App shows "Awaiting Approval"**
```
Screen: "আপনার লগইন অনুমোদনের জন্য অপেক্ষা করছে"
Button: "পুনরায় চেক করুন" (polls every 5 seconds)
```

**Step 6: Owner receives FCM notification**
```
Title: "নতুন লগইন অনুরোধ"
Body: "{user_name} নতুন ডিভাইস থেকে লগইন করতে চাচ্ছেন।"
Tap → Opens LoginRequestsActivity
```

**Step 7: Owner reviews in Admin Panel**
```
Sees:
- User name
- Email
- Phone
- Device name
- Device ID
- Platform
- App version
- Login time
Buttons: Approve | Reject
```

**Step 8: Owner taps Approve**
```
Admin Panel → approveLoginRequest() Cloud Function
Function:
- Check request status == PENDING
- Update device status = ACTIVE
- Update user status = ACTIVE
- Update request status = APPROVED
- Return success
```

**Step 9: App polls and gets approval**
```
App polling detects status = APPROVED
Closes approval screen
Redirects to Dashboard
```

**Step 10: User has access**
```
User can now use all features
Device is registered in system
```

### WORKFLOW 2: Payment Request

**Step 1: User wants to buy credit**
```
App → Select Credit Package (e.g., 100 credits for 500 TK)
Select Payment Method (e.g., bKash)
```

**Step 2: Show payment instructions**
```
"Send 500 TK to bKash: 01XXXXXXXXX"
"Enter Transaction ID after sending"
```

**Step 3: User enters transaction ID**
```
App → handlePaymentRequest() Cloud Function
Data:
- package_id
- payment_method_id
- transaction_id

Cloud Function:
- Get package details (credit_amount, price)
- Create payment_request with status=PENDING
- Send FCM to owner/admins
- Create notification record
```

**Step 4: Owner receives notification**
```
Title: "নতুন পেমেন্ট অনুরোধ"
Body: "100 ক্রেডিট কেনার জন্য পেমেন্ট অনুরোধ পাওয়া গেছে।"
```

**Step 5: Owner verifies payment**
```
Admin Panel → PaymentRequests
Sees:
- User name
- Package details (100 credits)
- Amount (500 TK)
- Payment method (bKash)
- Transaction ID
- Request time
Buttons: Approve | Reject
```

**Step 6: Owner taps Approve**
```
Admin Panel → approvePaymentRequest() Cloud Function
Function (TRANSACTION):
- Get payment request
- Check status == PENDING
- Get/Create credit wallet
- Add credit_amount to balance
- Create credit_transaction record
- Update payment_request status = APPROVED
- All atomic - if any fails, all rollback
```

**Step 7: User's credit updated**
```
Database:
- credit_wallet.balance: 0 → 100
- credit_transactions: NEW RECORD
- payment_request.status: PENDING → APPROVED

App polls and shows:
"100 ক্রেডিট আপনার অ্যাকাউন্টে যুক্ত হয়েছে"
```

**Step 8: Double approval prevention**
```
If admin clicks Approve twice:
- First click: status PENDING → APPROVED, credit added
- Second click: status APPROVED → no change, function throws error
- Credit NOT added twice
```

### WORKFLOW 3: SMS Request

**Step 1: User wants to send due reminder**
```
App → Select Customer
Shows: Name, Phone, Current Due
```

**Step 2: SMS template**
```
Template:
"আসসালামু আলাইকুম {customer_name},
আপনার কাছে আমাদের {due_amount} টাকা পাওনা রয়েছে।
অনুগ্রহ করে সুবিধামতো পরিশোধ করার জন্য অনুরোধ করছি।
ধন্যবাদ।
{shop_name}"

Variables auto-filled from database
User can edit if needed
Preview shows final message
```

**Step 3: User submits SMS request**
```
App → createSmsRequest() Cloud Function
Data:
- customer_id
- message
- request_type: "due_reminder"
- due_amount

Cloud Function:
- Check SMS permission enabled
- Create sms_request with status=PENDING
- Credit NOT deducted yet
- Send FCM to owner/admins
- Create notification record
```

**Step 4: Owner receives notification**
```
Title: "নতুন SMS অনুরোধ"
Body: "{customer_name}-কে SMS পাঠানোর অনুরোধ পাওয়া গেছে।"
```

**Step 5: Owner reviews in Admin Panel**
```
SmsRequests component shows:
- Customer name
- Phone number
- Message preview
- Request time
Buttons: Send | Reject
```

**Step 6: Owner taps Send**
```
Admin Panel → approveSmsRequest() Cloud Function
Function (TRANSACTION):
- Get SMS request
- Check status == PENDING
- Get credit wallet
- Check balance >= 1
- Deduct 1 credit
- Create credit_transaction record
- Update sms_request status = SENT
- All atomic
```

**Step 7: SMS actually sent**
```
In production: Integrate with SMS provider (Twilio, etc.)
For now: Mark as SENT in database
Owner would manually send from their Android device
```

**Step 8: Credit deducted**
```
Database:
- credit_wallet.balance: 100 → 99
- credit_transactions: NEW RECORD (type: 'use')
- sms_request.status: PENDING → SENT

App shows:
"1 ক্রেডিট ব্যবহার হয়েছে"
```

**Step 9: Rejection**
```
If owner clicks Reject:
- sms_request.status: PENDING → REJECTED
- Credit NOT deducted
- User sees rejection reason
```

## PHASE 7: Multi-Tenant Security

### 7.1 Shop Isolation
```
Every request must include shop_id
Firestore Security Rules enforce:
- User can only read own shop data
- User can only write to own shop
- Admin can't access other shops
```

### 7.2 Attempted Breach Scenarios

**Scenario 1: Shop A user reads Shop B customer**
```
Query: db.collection('customers').where('shop_id', '==', 'shop_b').get()
Result: PERMISSION DENIED (Security Rules block)
```

**Scenario 2: User modifies own shop_id in request**
```
Request: { shop_id: 'shop_b', ... }
Server: Verifies auth user's shop_id matches
Result: PERMISSION DENIED
```

**Scenario 3: User tries to approve own payment**
```
Function: approvePaymentRequest()
Check: Is user admin? No
Result: PERMISSION DENIED
```

## PHASE 8: Testing Checklist

### TEST A: Gmail New Device
- [ ] Phone A: Owner
- [ ] Phone B: New user logs in with Gmail
- [ ] Firebase auth succeeds
- [ ] Device record created in DB
- [ ] Login request created with status=PENDING
- [ ] Admin panel shows pending request
- [ ] Owner's phone receives FCM notification
- [ ] Owner taps notification → opens request
- [ ] Owner clicks Approve
- [ ] Phone B's approval check succeeds
- [ ] Phone B gets dashboard access

### TEST B: Mobile New Device
- [ ] Same as TEST A but with phone number

### TEST C: Payment Request
- [ ] User selects credit package
- [ ] Creates payment request
- [ ] Admin receives FCM
- [ ] Admin approves
- [ ] Credit added to wallet
- [ ] Double approve doesn't double credit

### TEST D: SMS Request
- [ ] User creates SMS request
- [ ] Admin receives FCM
- [ ] Admin approves
- [ ] Credit deducted
- [ ] Double approve doesn't double deduct

### TEST E: Multi-Tenant
- [ ] Shop A user can't read Shop B data
- [ ] Shop A admin can't access Shop B

### TEST F: Offline Scenarios
- [ ] Internet off: No fake success
- [ ] FCM fails: Request still in DB
- [ ] App crashes: Data persists

## PHASE 9: Deployment

### 9.1 Firebase
```
1. Deploy Firestore Security Rules
2. Deploy Cloud Functions
3. Configure Authentication providers
4. Set up Storage buckets
```

### 9.2 Android
```
1. Get google-services.json from Firebase
2. Add to app/
3. Build signed APK
4. Upload to Play Store
```

### 9.3 Web Admin Panel
```
1. Build React app
2. Deploy to Firebase Hosting
3. Configure CORS
4. Set environment variables
```

## PHASE 10: Known Limitations & Future Work

### Current Version Limitations
- SMS not actually sent (requires SMS provider integration)
- WhatsApp not integrated
- AI Assistant not implemented
- Backup/Restore not implemented
- Offline mode limited

### Next Phases
- Phase 1.1: SMS provider integration
- Phase 1.2: WhatsApp integration
- Phase 1.3: AI assistant
- Phase 1.4: Backup/Restore
- Phase 2.0: iOS app
- Phase 3.0: Advanced analytics

## Summary

This is a complete, production-ready architecture. Each phase builds on the previous one:

1. **Firebase Setup** - Infrastructure
2. **Android App** - User-facing mobile app
3. **Admin Web Panel** - Manager interface
4. **Cloud Functions** - Server-side logic
5. **Security** - Multi-tenant isolation
6. **Workflows** - End-to-end processes
7. **Testing** - Verification
8. **Deployment** - Production release

All critical workflows are documented above. Follow each workflow step-by-step to ensure proper implementation.

**CRITICAL SUCCESS FACTORS:**
- Database-first approach (Firestore is source of truth)
- Transactions for atomicity
- FCM for notifications (but DB is fallback)
- Server-side validation for security
- Multi-tenant checks at every step
