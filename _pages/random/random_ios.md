---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Apple iOS Mobile App
layout: single
permalink: /random_ios/
sidebar:
   nav: "ios"
---

## iOS Mobile 
### iOS Distribution certificate Expired
 
When fastlane reports the following, its time to create/refresh the certificate.
{% highlight bash linenos %}
WARN [2020-10-16 15:58:18.79]: Lane Context:
INFO [2020-10-16 15:58:18.79]: {:DEFAULT_PLATFORM=>:ios, :PLATFORM_NAME=>:ios, :LANE_NAME=>"ios beta"}
ERROR [2020-10-16 15:58:18.79]: Your certificate 'VZ8278993K.cer' is not valid, please check end date and renew it if necessary
{% endhighlight %}
in Bitrise its shown as;
{% highlight bash linenos %}
[12:07:32]: Cloning remote git repo...
[12:07:32]: If cloning the repo takes too long, you can use the `clone_branch_directly` option in match.
[12:07:33]: Checking out branch master...
[12:07:33]: 🔓  Successfully decrypted certificates repo
+------------------+----------+
|        Lane Context         |
+------------------+----------+
| DEFAULT_PLATFORM | ios      |
| PLATFORM_NAME    | ios      |
| LANE_NAME        | ios beta |
+------------------+----------+
[12:07:33]: Your certificate '24RP2HA5NG.cer' is not valid, please check end date and renew it if necessary
{% endhighlight %}

Here are the steps to rectify the problem.

#### (Old notes) Create a new certificate for iOS app distribution
  * Generate a CSR from the KeyChainAccess app on your Mac and save the file to disk
  * In the Apple Developer console, under certificates and IDs, Generate a new iOS distribution certificate
  * when prompted upload the CSR file generated/saved above
  * download the newly generated distribution.cer
  * double click in the Finder to add to Keychain
  * export private key as Certificates.p12 and store the .p12 safely in LP
  * Rebuild shared provisioning profiles
  * cd ios/
  * fastlane match nuke distribution
  * fastlane match appstore
Now when a developer or the CI platform (Bitrise) attempts a build, by running fastlane match, it will pull the new distribution profile containing the new distribution certificate and all should be well again.

  * Add it to lastpass
  * Update BITRISE CI system
  * share so developers can build locally?
#### p12 to pem file format.
{% highlight bash %}
openssl pkcs12 -in Certificates.p12 -out Certificates.pem -nodes -clcerts
# Inspect p12 file (not much info, better to convert to and inspect pem file)
openssl pkcs12 -nokeys -info -in ../DistCertificates.p12
# Inspect contents of pem file
openssl x509 -noout -text -in Certificate.pem
# Inspect contents of CSR
openssl req -in mycsr.csr -noout -text
{% endhighlight %}


### Renew Distribution Certificate
This is the procedure to follow when the distribution certificate expires.
This is an annual occurrence.

The fastlane build tool will let you know of the problem with an error message like the following.

```
WARN [2020-10-16 15:58:18.79]: Lane Context:
INFO [2020-10-16 15:58:18.79]: {:DEFAULT_PLATFORM=>:ios, :PLATFORM_NAME=>:ios, :LANE_NAME=>"ios beta"}
ERROR [2020-10-16 15:58:18.79]: Your certificate 'VZ8278993K.cer' is not valid, please check end date and renew it if necessary
```

#### Steps to rectify the problem.

   1. Generate a Certificate Signing Request (CSR) file
   1. Create Distribution Certificate 
   1. Revoke/Nuke current provisioning profiles
   1. Generate new provisioning profiles using new certificate.

<a name="CSR">
##### Generate a Certificate Signing Request
To renew distribution or APNS certificates a CSR file is required.

   1. Visit this link for detailed steps on how to [generate a certificate signing request](https://help.apple.com/developer-account/#/devbfa00fef7) (CSR).
   1. The CSR file is generated Using the KeyChainAccess app on your Mac and saved to disk
      1. On MacBook launch KeyChainAccess app (CMD+SPACE)
      1. From menu, KeyChainAccess -> Certificate Assistant -> Request a certificate from an authority 
      1. `User Email Address` : 'myappleID@gmail.com'
      1. `Common Name` : 'Atul Patel'
      1. `CA Email Address` : LEAVE_FIELD_BLANK
      1. `Request is` : Save to disk
   1. Locate this saved file `CertificateSigningRequest.certSigningRequest` in Finder (required for next steps).

##### Create distribution certificate
* Create a new certificate for iOS app distribution
    1. [Navigate to Apple Developer console](https://developer.apple.com/account/resources/certificates/list) and sign in with your personal appleID.
    1. Within the Developer console, under certificates and IDs
        1. Click `+` to generate and add a new iOS distribution certificate
        1. From list of options select `iOS Distribution (App Store and Ad Hoc)` 
        1. Click continue
    1. When prompted, upload the CSR file generated/saved from previous section. 
	`i.e. CertificateSigningRequest.certSigningRequest`. 
    1. Click continue
    1. Download the newly generated `distribution.cer`
    1. Double click the `distribution.cer` in a Finder window to add it to your KeyChain Access
    1. Within KeyChain, under "My Certificates", locate the newly added cert.
    1. Right click to export private key as Certificates.p12 and store the .p12 safely in LP

##### Revoke old/generate new provisioing profiles
* Rebuild shared provisioning profile
    1. `$ git clone git@github.com:myproject/myproject.git`
    1. `$ cd project/ios/`
    1. `$ fastlane match nuke distribution`
    	- if this command runs without error, a new commit `[fastlane] Nuked files for distribution` will be added to git repo `ios-profiles` confirming profiles have been nuked.
    1. `$ fastlane match appstore`
    	- Check `ios-profiles` for git commit `[fastlane] Updated appstore and platform ios` confirming new certs/p12 have been added

Now when a developer or the CI platform (Bitrise) attempts a build, by running `fastlane match`, it will pull the new distribution profile containing the new distribution certificate and all should be well again.


### Renew APNS Certificate

Amazon's pinpoint service is used to send out Push Notifications to mobile devices. Pinpoint needs a valid Apple Push Notification Service (APNS) certificate, and this certificate needs to be renewed annually. 

In brief the steps invovled are;

   1. Generate a Certificate Signing Request (CSR) file (See above)
   1. Create an APNS Certificate 
   1. Upload APNS .p12 file to Amazon Pinpoint 


##### Generate a Certificate Signing Request (CSR*
To renew distribution or APNS certificates a CSR file is required. [See above for steps](#CSR)

##### Create an APNS Certificate

   1. [Navigate to Apple Developer console](https://developer.apple.com/account/resources/certificates/list) and sign in with your personal appleID.
   1. Within the Developer console, under certificates and IDs;
      1. Click `+` to add a new APNS certificate
      1. From list of options select `Apple Push Notification service SSL (Sandbox & Production)`
      1. Click continue
      1. Select AppID `com.mycompany.myapp`
      1. When prompted, upload the CSR file generated/saved from previous section i.e. `CertificateSigningRequest.certSigningRequest`
   1. Click continue
   1. Download the newly generated `distribution.cer`
   1. Double click the `distribution.cer` in a Finder window to add it to your KeyChain Access
   1. Within KeyChain, under "My Certificates", locate the newly added cert.
   1. Right click to export private key as APNs_Certificates.p12 and store the .p12 safely in LP

##### Upload APNS p12 to Pinpoint

   - See [How to upload APNs certificate to pinpoint](https://aws.amazon.com/premiumsupport/knowledge-center/pinpoint-apns-certificates-aws-cli/)
   - Read current APNS sandbox settings
      - NOTE: `--application-id` is displayed as the `Project ID` in AWS console.
```
        $ aws pinpoint get-apns-sandbox-channel --application-id fb1882ecf5fd4cef9eb7019d9e4ca625
        {
            "APNSSandboxChannelResponse": {
                "ApplicationId": "fb1882ecf5fd4cef9eb7019d9e4ca625",
                "CreationDate": "2019-10-18T09:58:24.719Z",
                "DefaultAuthenticationMethod": "CERTIFICATE",
                "Enabled": true,
                "HasCredential": true,
                "HasTokenKey": false,
                "Id": "apns_sandbox",
                "IsArchived": false,
                "LastModifiedDate": "2020-10-22T12:27:44.601Z",
                "Platform": "APNS_SANDBOX",
                "Version": 2
            }
        }
```

   - Update APNs sandbox channel for an Amazon Pinpoint app

```
$ aws pinpoint update-apns-sandbox-channel --application-id fb1882ecf5fd4cef9eb7019d9e4ca625 --apns-sandbox-channel-request "Certificate=$(openssl pkcs12 -in APNs_Certificates.p12 -nodes -nokeys), Enabled=true,PrivateKey=$(openssl pkcs12 -in APNs_Certificates.p12 -nodes -nocerts)" --region eu-west-1
```
   - Read current APNs production channel settings
```
       $ aws pinpoint get-apns-channel --application-id fb1882ecf5fd4cef9eb7019d9e4ca625
       {
           "APNSChannelResponse": {
               "ApplicationId": "fb1882ecf5fd4cef9eb7019d9e4ca625",
               "CreationDate": "2019-05-22T08:28:21.763Z",
               "DefaultAuthenticationMethod": "CERTIFICATE",
               "Enabled": true,
               "HasCredential": true,
               "HasTokenKey": false,
               "Id": "apns",
               "IsArchived": false,
               "LastModifiedDate": "2020-10-22T12:27:07.850Z",
               "Platform": "APNS",
               "Version": 4
           }
       }
```

   - Update APNs production channel

```
$ aws pinpoint update-apns-channel --application-id fb1882ecf5fd4cef9eb7019d9e4ca625 apns-channel-request "Certificate=$(openssl pkcs12 -in APNs_Certificates.p12 -nodes -nokeys), Enabled=true,PrivateKey=$(openssl pkcs12 -in APNs_Certificates.p12 -nodes -nocerts)" --region eu-west-1
```

### Provisioning profiles in Xcode

* Xcode -> Preferences -> Accounts
   * SignIn using appleID
   * Select the Team associated with App/Project
   * Click "Manage Certificates", this pulls out all certs within your mac's KeyChain
   * Selecting the '+' icon allows you to add, caution!! this appears to add certs into Apple portal, under certificates & ID. Automatically adds it your KeyChain (God knows what else it does)
   * Click "Download Manual Profiles" , downloads provisioning profiles to a folder used by Xcode builds `~/Library/MobileDevice/Provisioning\ Profiles/`
       * Inspect the contents of these files using `security cms -D -i  ~/Library/MobileDevice/Provisioning\ Profiles/94783961-41c2-4c23-9b49-0e9702eea911.mobileprovision`
   * 

### How to check certificates
   * Which type of certificate is it?
   * Is it signed corretly?
       * Xcode -> Projects? -> Targets -> Signing & Capabilities
       * ![alt Foo](/assets/images/XcodeCertSigningError.png "Certificates & Signing") 
   * After fixing certificates, had to regenerate the provisioning profiles on local PC i.e. built from new certificates
       * fastlane match nuke development
           (Note, in developer console, under Certs,Identifiers,Profiles the entry is removed)
           * fastlane match development
       * fastlane match nuke distribution
           * fastlane match adhoc
           * fastlane match appstore

Each developer needs to run;
  * fastlane match development
  * fastlane match adhoc


### Testing Push notifcation

#### Retrieve device Address & endpoint ID
When App is launched code functions in `AppDelegate.m` register a handsets device for Push notifications. This unique ID (along with an endpoint id) is regsitered in AWS pinpoint service and can be used for sending Push notifications to a specific handset or user. Both can be retrieved using the aws cli, --user-id is the user-sub assigned to the account in cognito;
{% highlight bash %}
ws pinpoint get-user-endpoints --application-id fb1882ecf5fd4cef9eb7019d9e4ca625 --region eu-west-1 --user-id 7f841138-f04f-4d60-9b92-ee987dd58481 | jq '.EndpointsResponse.Item[] | "\(.Id) \(.EndpointStatus) \(.EffectiveDate) \(.Demographic.AppVersion) \(.Address)"'

"776dfb80-3e13-11ec-a524-c114cbb3a1be ACTIVE 2021-11-05T08:37:21.013Z 861 null"
"96e26980-3e02-11ec-bc53-e5f83404761d ACTIVE 2021-11-05T06:36:35.226Z 45 06113c21e915082e93eb7a54b7d13e116e03dc4c42d686a56db1ef538a66d8b9"
"29d98b30-3d7a-11ec-a218-f3979257820d ACTIVE 2021-11-05T01:03:46.929Z 45 null"
"c680a5e0-3d84-11ec-a0d3-91762723b785 ACTIVE 2021-11-05T01:03:46.981Z 45 null"
"fcfa1520-3e01-11ec-86a3-31dbad4e733d ACTIVE 2021-11-05T06:31:30.722Z 45 6bd8a58690b21f74494b9714ea66379a17474295d0562ab489dae25a895f6643"
"7ef1d850-3d8b-11ec-9dbc-014110c83797 INACTIVE 2021-11-05T01:03:47.037Z 45 6bd8a58690b21f74494b9714ea66379a17474295d0562ab489dae25a895f6643"
"d5a846e0-3d61-11ec-8598-617cb458c72e ACTIVE 2021-11-05T01:03:47.102Z 861 null"
"add17000-3e07-11ec-ad3e-49693abc14b1 ACTIVE 2021-11-05T07:17:41.389Z 45 21df54cbd135e81d01a5a5a113628efbe528face4f91300cc2575f18889cc877"
"c41f8560-3d79-11ec-8b76-d3e4b4b83f8d ACTIVE 2021-11-05T01:03:47.155Z 45 null"

{% endhighlight %}

#### Send Push using Address (production)
To send a test push notification to an App that has been created with a production `appstore` distribution provisioning profile;
{% highlight bash %}
curl -v -d '{"aps":{"alert":"Hello Atul","badge":42}}' -H "apns-priority: 10" -H "apns-topic: com.mycompany.myapp" --http2 --cert-type P12 --cert ~/Downloads/NoPassword_Sandbox_Prod_Certificates.p12 https://api.push.apple.com/3/device/11408ce9446a0fe9bd210d813bfd5eef5c46c968c9343188485d596734fc276f
{% endhighlight %}

#### Send Push using Address (sandbox)
Test builds using a `development` or `adhoc` provisioning profile change the url to `https://api.development.push.apple.com`.  This assumes the certificate is created for Sandbox environment.

{% highlight bash %}
curl -v -d '{"aps":{"alert":"Eureka","badge":4}}' -H "apns-priority: 10" -H "apns-topic: com.mycompany.myapp" --http2 --cert-type P12 --cert ~/Downloads/NoPassword_Sandbox_Prod_Certificates.p12 https://api.development.push.apple.com/3/device/06113c21e915082e93eb7a54b7d13e116db1ef538a66d8b9
{% endhighlight %}

TODO: Need to work out how to embed a deeplink into the message body, also use aws cli to send using a endpoint ID.

#### Send push using endpoint ID (aws pinpoint)
    * Login to AWS console, [pinpoint](https://eu-west-1.console.aws.amazon.com/pinpoint/home?region=eu-west-1#/about)

  * Select `All Projects` -> Your `Project Name`
  * Selet `Test Messaging`
  * Destination Type = `Destination IDs` (retrieve via above cmd).
  * Fill in subject + body, optionally set a deeplink. 

#### Send Push using awscl
{% highlight bash %}

aws pinpoint send-messages --application-id fb1882ecf5fd4cef9eb7019d9e4ca625 --region eu-west-1  --message-request='{ "MessageConfiguration": { "APNSMessage": { "Body": "Message body", "Title": "foo", "Action": "URL", "Url": "loo://foobar", "Badge": 0 } }, "Endpoints": { "a04f0240-3e24-11ec-a63f-69de43ad956e": {} } }'

Where endpoint ID is retrieved using above cmd.
{% endhighlight %}

### Xcode

To see logging from code running on a device attached to a mac book (NSLog) via USB

 * Open the 'Console' app i.e. CMD+SHIFT 'Console'
 * Select the mobile device to inspect
 * ensure the XCode is setup to print NSLog to console
   * XCode -> Product -> Scheme -> Edit Scheme
   * Under Run, set an environment variable "OS_ACTIVITY_MODE" Leave the value blank
