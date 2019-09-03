# Two-Factor Authentication

Pharos supports two-factor authentication for users and institutions who want an extra layer of security. We recommend you turn on two-factor authentication for institutional administrators in the production repository, since institutional admins have the ability to delete materials.

!!! tip
    Two-factor settings are separate on the production and demo repositories. Enabling or disabling this feature in the Pharos production system has no effect on the demo system and vice-versa. You'll have to set your preferences separately on each system.


## Enabling Two-Factor Authentication

Institutional users can enable two-factor authentication for themselves. Institutional administrators can enable it for all users at their institution. Users who enable two-factor auth for themselves can choose to disable it later. When an administrator enables two-factor auth for their institution, users cannot opt out or disable the setting.

### Enabling Two-Factor for Yourself

To enable two-factor authentication for yourself, click the __Enable 2FA__ button on the screen you see immediately after login and follow the onscreen instructions.

![Enable two-factor authentication for yourself](../img/pharos/pharos_2fa_section.png)

#### Update Your Mobile Number

If your phone number isn't up to date, you'll need to update it in the confirmation dialog. Enter the number of your __mobile__ phone to receive two-factor authentication codes via text message or push notification. Enter your number using the international format, beginning with a plus sign and the country code. The US country code is +1.

![Dialog requesting you update your mobile phone number](../img/pharos/UpdateMobileNumber.png)

#### Copy Your Backup Codes

Before proceeding any further, copy your backup codes to a safe place on your computer or phone. These codes are valid for one-time use, and can help you log in to Pharos when you're not able to authenticate via text message or push notification.

![List of backup codes](../img/pharos/BackupCodes.png)

#### Verify Your Mobile Number

Click one of the two buttons to verify your mobile number. Choose __Verify Phone Number via SMS__ to receive a text message. If you already have the Authy app installed, choose __Verify Phone Number via Push Notification__.

![Options for verifying your mobile phone number](../img/pharos/VerifyMobileNumber.png)

You should receive a text message or push notification within a minute or so. If you chose the text/SMS option, enter the code you received in the text message into the verification field in Pharos.

<span style="color:red;font-weight:bold;background-color:yellow;">NEED SCREENSHOT</span>

If you have Authy installed on your phone and you chose to verify via push notification, click the __Approve__ button when the Authy verification request appears.

![Authy request to verify phone number](../img/pharos/pharos_2fa_authy_app_request_phone_verification.jpg)

### Enabling Two-Factor for Other Users or Your Entire Institution

<span style="color:red;font-weight:bold;background-color:yellow;">This section needs review and verification.</span>

Institutional administrators can enable two-factor authentication for specific users at their institution, or for the institution as a whole.

To enable two-factor auth for a specific user:

1. Choose __Admin > Manage Users__ from the menu in the upper right corner of the page.

1. Click the green __Enable 2FA__ button to the right of the user for whom you want to enable two-factor auth.

To enable two-factor auth for your institution:

1. Choose __Admin > Manage Institutions__ from the menu in the upper right corner of the page.

1. Click the green __Enable Mandatory Institution-Wide 2FA__ button.


### Choosing Your Second Factor

After enabling two-factor authentication, you'll be presented with an option to verify your identity each time you log in to Pharos. Choose the option that best suits your needs, and remember that if you don't have access to phone service, you can use each of your backup codes once.

![List of authentication options](../img/pharos/2faAuthOptions.png)

### Grace Period for New Users

<span style="color:red;font-weight:bold;background-color:yellow;">When does the grace period start? To whom does it apply, and for how long?</span>

### If You're Locked Out

If you can't log in and you don't have access to any valid backup codes, contact help@aptrust.org.
