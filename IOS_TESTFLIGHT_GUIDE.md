# iOS TestFlight publishing for this MAUI app with Codemagic

This guide is tailored to this repository:

- Project file: `LightningLabIOS/LightningLabIOS.csproj`
- iOS plist: `LightningLabIOS/Platforms/iOS/Info.plist`
- Codemagic workflow: `codemagic.yaml`

## Current project status

The project is now prepared with safer defaults for iOS publishing:

- `ApplicationId`: `com.yourcompany.lightninglabios`
- `ApplicationDisplayVersion`: `1.0.0`
- `ApplicationVersion`: `1`
- A Codemagic workflow has been added for building an `.ipa` and uploading it to TestFlight

You still need to replace the placeholder values with your real Apple app data before the first release.

## 1. Update the MAUI project

Open `LightningLabIOS/LightningLabIOS.csproj` and confirm these values:

```xml
<ApplicationTitle>LightningLabIOS</ApplicationTitle>
<ApplicationId>com.yourcompany.lightninglabios</ApplicationId>
<ApplicationDisplayVersion>1.0.0</ApplicationDisplayVersion>
<ApplicationVersion>1</ApplicationVersion>
```

Rules:

- `ApplicationId` must exactly match your Apple bundle identifier.
- `ApplicationDisplayVersion` is the visible version shown to users, for example `1.0.0`.
- `ApplicationVersion` is the internal build number and must increase with every new TestFlight upload.

## 2. Replace the default app branding

This project still uses the default MAUI assets. Replace them before publishing:

- `LightningLabIOS/Resources/AppIcon/appicon.svg`
- `LightningLabIOS/Resources/AppIcon/appiconfg.svg`
- `LightningLabIOS/Resources/Splash/splash.svg`

You do not need to change the `.csproj` references unless you rename those files.

## 3. Prepare Apple account items

Before Codemagic can build and upload your iOS app, you need:

- An active Apple Developer Program membership
- A bundle ID created in Apple Developer
- An app record created in App Store Connect
- An App Store Connect API key with at least `App Manager` access
- An `Apple Distribution` certificate
- An `App Store` provisioning profile for the same bundle ID

Important:

- The bundle ID must match in Apple Developer, App Store Connect, Codemagic, and your `.csproj`.
- For the first upload, it is best to create the app in App Store Connect first.

## 4. Configure Codemagic

### Add the App Store Connect API key

In Codemagic:

1. Open `Team settings`
2. Go to `Developer Portal`
3. Open `Manage keys`
4. Upload your App Store Connect API key

Use a clear reference name, for example:

- `apple-api-key-lightninglab`

### Add signing assets

In Codemagic:

1. Open `codemagic.yaml settings`
2. Open `Code signing identities`
3. Upload or generate an iOS certificate
4. Upload or fetch the provisioning profile

Recommended for TestFlight:

- Certificate type: `Apple Distribution`
- Provisioning profile type: `App Store`

## 5. Update `codemagic.yaml`

The repository already contains a ready-to-edit workflow:

- `codemagic.yaml`

You must replace these placeholders:

1. `app_store_connect`
2. `BUNDLE_ID`
3. `APP_STORE_APPLE_ID`
4. `APP_DISPLAY_VERSION` when you ship a new visible app version

Example:

```yaml
integrations:
  app_store_connect: APP_STORE_CONNECT_INTEGRATION_NAME
```

```yaml
BUNDLE_ID: "com.yourcompany.lightninglabios"
APP_STORE_APPLE_ID: "1234567890"
APP_DISPLAY_VERSION: "1.0.0"
```

Notes:

- `app_store_connect` is the Codemagic integration name, not the Apple Key ID.
- `APP_STORE_APPLE_ID` is the numeric app ID from App Store Connect.
- The workflow already auto-increments the build number from the latest TestFlight build.

## 6. What the Codemagic workflow does

The `maui-ios-testflight` workflow will:

1. Install the .NET 9 SDK
2. Install the MAUI and iOS workloads
3. Update the iOS plist with `ITSAppUsesNonExemptEncryption=false`
4. Read the latest TestFlight build number
5. Build a signed `net9.0-ios` `.ipa`
6. Upload the build to App Store Connect
7. Submit it to TestFlight

## 7. Build and publish

Once the placeholders are updated:

1. Push the repository to GitHub, GitLab, or Bitbucket
2. Connect the repository in Codemagic
3. Start the `maui-ios-testflight` workflow
4. Wait for the build and upload to finish
5. Open App Store Connect and check the `TestFlight` tab

Apple processing may take several minutes after upload.

## 8. Common reasons builds fail

- Bundle ID mismatch between project and Apple account
- Wrong certificate type, such as Development instead of Distribution
- Wrong provisioning profile type
- Missing app record in App Store Connect
- Build number not increasing
- Missing required App Store Connect metadata for the first submission

## 9. Files to manage in this repo

Main files for your Codemagic iOS release flow:

- `LightningLabIOS/LightningLabIOS.csproj`
- `LightningLabIOS/Platforms/iOS/Info.plist`
- `codemagic.yaml`

## 10. Official documentation

- Codemagic .NET MAUI: https://docs.codemagic.io/yaml-quick-start/building-a-dotnet-maui-app/
- Codemagic iOS signing: https://docs.codemagic.io/yaml-code-signing/signing-ios/
- Codemagic App Store Connect publishing: https://docs.codemagic.io/yaml-publishing/app-store-connect/
- Microsoft .NET MAUI iOS publishing: https://learn.microsoft.com/en-us/dotnet/maui/ios/deployment/publish-cli?view=net-maui-10.0
