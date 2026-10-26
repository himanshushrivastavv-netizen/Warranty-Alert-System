# Warranty & Bill Alert System 📱💼

A native Android application built with **Kotlin**, **Jetpack Compose (Material 3)**, **Room Database**, **WorkManager**, and **Clean Architecture (MVVM)**.

---

## 🎯 Features

1. **Input & Upload**
   - Form fields: Product Name, Category, Purchase Date, Warranty Duration (Months/Years), Auto-calculated Expiry Date (with manual override), Store/Brand Name, Notes.
   - Camera Capture & Gallery Picker: Receipts are downscaled and compressed to JPEG (~80% quality) and safely persisted in the app's internal private storage (`context.filesDir/bills`).
2. **Background Alert & Notification System**
   - Background scheduling via Android Jetpack `WorkManager` (runs every 24 hours with battery-conscious constraints).
   - Dynamic alerts: **10 days before expiry**, **3 days before expiry**, and on the **exact expiration date**.
   - Android 13+ (`POST_NOTIFICATIONS`) runtime permission support.
   - High importance notification channel (`warranty_expirations`) with action buttons:
     - **View Bill**: Launches app directly to the bill's detail view using intent deeplink (`warranty://item/{itemId}`).
     - **Dismiss**: Cancels the notification immediately via a broadcast receiver.
3. **Dashboard & Management**
   - Categorized tabs: **All**, **Expiring Soon (≤30 days)**, **Active**, and **Expired**.
   - Search bar across Product Name, Brand/Store, Notes, and Category.
   - Horizontal category filter chips (Electronics, Appliances, Furniture, Automobile, etc.).
   - Metric overview cards for quick status visibility.
   - Manual background check trigger button in the top app bar for instant testing.
4. **Bill Details & Viewer**
   - Full metadata breakdown with badges.
   - Interactive **Zoomable and Pan Bill Viewer** (multi-touch pinch-to-zoom up to 4x, pan gesture, double-tap reset).
   - Share bill receipt image (JPEG) or textual warranty details via standard Android Sharesheet and `FileProvider`.

---

## 🏗️ Architecture & Tech Stack

```
com.warranty.alert/
├── WarrantyAlertApp.kt            # Application setup (Channels, WorkManager, DB)
├── data/
│   ├── local/
│   │   ├── AppDatabase.kt         # Room Database Singleton
│   │   ├── entity/WarrantyEntity.kt # Table entity with indexes
│   │   ├── dao/WarrantyDao.kt     # SQL queries & reactive flows
│   │   └── converters/Converters.kt
│   └── repository/
│       └── WarrantyRepositoryImpl.kt # Implements domain repository
├── domain/
│   ├── model/
│   │   ├── WarrantyItem.kt        # Domain model + status computation
│   │   └── WarrantyStatus.kt      # ACTIVE, EXPIRING_SOON, EXPIRED
│   └── repository/
│       └── WarrantyRepository.kt  # Contract
├── worker/
│   ├── WarrantyCheckWorker.kt     # CoroutineWorker checking 10d/3d/0d deadlines
│   └── WorkScheduler.kt           # Periodic & One-Time WorkManager configuration
├── notification/
│   ├── NotificationHelper.kt      # Notification channel & rich alert builder
│   └── NotificationActionReceiver.kt # BroadcastReceiver for dismiss action
├── util/
│   ├── DateUtils.kt               # Expiry math & human countdown formatting
│   ├── ImageStorageManager.kt     # Internal storage compression & FileProvider Uri
│   └── ShareHelper.kt             # Intent-based sharing
└── ui/
    ├── MainActivity.kt            # Entry activity & runtime permission handling
    ├── navigation/
    │   ├── Screen.kt              # Sealed route definitions
    │   └── AppNavHost.kt          # Compose NavHost & deeplinks
    ├── components/
    │   ├── StatusBadge.kt         # Emerald / Amber / Rose status pill
    │   ├── WarrantyCard.kt        # Card item with thumbnail and countdown
    │   ├── CategoryChipRow.kt     # Horizontal category filter chips
    │   └── ZoomableImageView.kt   # Pinch-to-zoom bill preview modal
    ├── dashboard/                 # Metrics, live search, tabs, list
    ├── add_edit/                  # Form, auto-calculation, camera/gallery
    └── detail/                    # Full view, zoom dialog, share, delete
```

---

## 🚀 How to Open in Android Studio

1. Open **Android Studio** (Hedgehog, Iguana, Jellyfish, or newer).
2. Select **Open** and navigate to:
   ```
   C:\Users\hp\.gemini\antigravity-ide\scratch\WarrantyBillAlertApp
   ```
3. Allow Gradle to sync dependencies.
4. Connect an Android device or start an Android Emulator (API 24 to API 34).
5. Click **Run** (`Shift + F10`).

---

## 🔔 How to Test Background Alerts

1. Create a warranty with an expiry date set to **10 days**, **3 days**, or **Today** (or use the "Edit Date" button to pick custom expiration).
2. Click the **Notification Bell** icon in the top app bar of the Dashboard, which immediately runs `WorkScheduler.triggerImmediateCheck(context)`.
3. An alert notification will immediately appear in your notification shade with the **View Bill** and **Dismiss** actions!
