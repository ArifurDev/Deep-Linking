Deep Linking — Clear & Simple Guide (Backend + App)
1) What is Deep Linking?
Deep linking lets a URL open a specific screen inside a mobile app.
Example
```
https://yourdomain.com/product/123
```
If the app is installed → opens the Product screen (id=123)
If not installed → opens in the browser (web page)
---
2) Who Does What?
Two roles work together:
A) Backend Developer (You)
Your job is to prove that your domain belongs to your app.
B) App Developer (Android/iOS/Flutter)
Their job is to read the URL and open the correct screen inside the app.
---
3) Backend Responsibilities (Simple Checklist)
3.1 Create the verification folder
```
public/.well-known/
 ├── apple-app-site-association   (for iOS)
 └── assetlinks.json              (for Android)
```
3.2 Add correct content (given by app dev)
Android — assetlinks.json
```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "com.yourapp",
      "sha256_cert_fingerprints": [
        "AA:BB:CC:..."
      ]
    }
  }
]
```
iOS — apple-app-site-association (no .json extension)
```json
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appID": "TEAM_ID.BUNDLE_ID",
        "paths": ["*"]
      }
    ]
  }
}
```
> The **package name, SHA256 fingerprint, Team ID, and Bundle ID** must be provided by the app developer.
3.3 Make files publicly accessible
Open in browser to verify:
```
https://yourdomain.com/.well-known/assetlinks.json
https://yourdomain.com/.well-known/apple-app-site-association
```
Must load successfully
Must be served over HTTPS
iOS file must have Content-Type: application/json
3.4 Define clean URL structure
Design URLs that map to app screens:
```
/product/{id}
/profile/{id}
/order/{id}
```
3.5 Provide web fallback (optional but recommended)
If the app is not installed, the same URL should render a normal web page.
---
4) App Developer Responsibilities
4.1 Configure app for links
Android: add intent-filter with `autoVerify=true`
iOS: enable Associated Domains and add `applinks:yourdomain.com`
4.2 Provide IDs to backend
Android: package name + SHA256 fingerprint
iOS: Team ID + Bundle ID
4.3 Handle incoming URL
Read the URL when the app opens
Extract parameters (e.g., `product/123` → id=123)
4.4 Navigate to the right screen
```
/product/123  → ProductScreen(123)
/profile/5    → ProfileScreen(5)
```
---
5) End-to-End Flow (Very Simple)
User taps a link:
```
https://yourdomain.com/product/123
```
The OS checks your domain’s `.well-known` files.
If verified → opens your app.
The app reads the URL and opens the correct screen.
If not verified or app not installed → opens the website.
---
6) Common Problems (Quick Fixes)
❌ Not using HTTPS → Use HTTPS only
❌ Wrong SHA256 / Bundle ID → Match exactly
❌ Files not reachable → Check URLs above
❌ Wrong content type (iOS) → application/json
---
7) Responsibility Summary
Task	Backend	App Dev
Create `.well-known` files	✅	❌
Host domain (HTTPS)	✅	❌
Provide IDs (package/bundle/SHA)	❌	✅
Configure app (intent/domains)	❌	✅
Design URL structure	✅	⚠️
Open correct screen	❌	✅
Web fallback page	✅	❌
---
8) Key Takeaway
Backend = verifies the domain for the app
App = decides which screen to open
If both sides are correct, deep linking works reliably.
---
9) Next Steps (Optional)
Map URLs to Laravel APIs
Add payment return links (e.g., Stripe → app)
Implement deep link handling in Flutter/Android/iOS
