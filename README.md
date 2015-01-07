# iOS8Push
The Push iOS 8 service helps to send push notifications to iOS devices. The devices can be targetted using tags or device identifier. Use simple and uniform REST APIs to configure, subscribe, send, and monitor push notifications to iOS devices.

    Monitoring

Creating Mobile Apps>iOS>Push for iOS8
Getting started with Push for iOS8
Last Updated: 12/18/2014
TUTORIALS AND SAMPLES
SDK REFERENCE
API REFERENCE
COMPATIBLE RUNTIMES
RELATED LINKS

    Bluelist sample

IBM® Push for iOS8 for Bluemix™ provides the capability to use iOS 8 features for managing and interacting with notifications in your application (app). An intuitive and easy-to-use dashboard helps you to interactively configure the Apple Push Notification service (APNs) certificates, manage tags, send notifications, and view notification analytics.

After you create a mobile application for iOS and set up your mobile development environment (see Getting Started, steps 1-6), follow these steps to get started with Push for iOS8:

    Open your mobile iOS application in the Bluemix dashboard and then click the Push for iOS8 service to open the Push dashboard.
    On the Configuration tab, upload the certificates that are required for APNs.
    Next, go to the Tags tab and create tags so that you can later send notifications based on tag subscriptions. Tags provide a way to send targeted notifications to users based their interests. For example, if the app is for a retail store, you might create a clearance events tag that customers can subscribe to.
    Go to the Notifications tab to send push notifications directly from the dashboard. To reach a specific group of users, you can select tags or customer numbers. For example, if the notification contains information about an upcoming clearance sale, you might select the clearance events tag.
    Using the SDK for Push for iOS8, you can code your application to perform the following functions:
        Register applications
        Get lists of tags or subscriptions and subscribe or unsubscribe to tags
        Handle notifications (including silent notifications and interactive notifications)
        Enable event monitoring so that you can view push metrics in the dashboard

Overview

Push for iOS8 is a service that you can use to broadcast notifications to iOS 8 applications and send notifications to targeted groups of users. Push for iOS8 provides an intuitive dashboard to administer devices, tags, and notifications. There is also an SDK and REST APIs to further develop your client applications.
Push for iOS8 process
Figure 1. Push for iOS8 process flow
Mobile clients can subscribe and register for push notifications. The backend app owner configures and sends notifications to Push. The notifications are dispatched to the Apple Push Notification service (APNs) and then sent to registered mobile clients.
Push for iOS8 dashboard

You can access the dashboard for Push for iOS8 on Bluemix. From your mobile iOS application, click the Push for iOS8 service.
Use the Push dashboard to perform the following tasks:

    Send different types of notifications, including silent and interactive, as well as mixed notifications that can be broadcast to all devices, multicast to specific devices (identified by user IDs), and multicast to targeted devices that are subscribed to a specific tag.
    Administer tags for tag-based notifications that reach targeted recipients.
    Manage the subscriptions of devices to tags.
    Monitor metric data for push registrations and notifications, including the number of registered devices over time and the number of notifications sent.

Configuring the Apple Push Notification service (APNs)

To use Push for iOS8 to send notifications, you must upload the SSL certificates that are required for Apple Push Notification service (APNs).

The certificates that are needed for APNs are .p12 certificates, which contain the private key and SSL certificates that are required to build and publish your application. You must generate the certificates from the Member Center of the Apple Developer website (for which a valid Apple Developer account is required). Separate certificates are required for the development environment (also known as sandbox) and the production environment.

You can add the p12 certificates to your application from the Configuration tab in the Push dashboard. After the certificates are uploaded successfully, you can start sending notifications.

For more information about using the APNs, see iOS Developer Library: Local and Push Notification Programming Guide.
Managing tags and subscriptions

You can use the Push dashboard or REST APIs to create, edit, and remove tags from an application. Tags provide a means to send targeted notifications to recipients based on subscriptions, unlike general broadcasts that are sent to all applications. Subscriptions are based on predefined tags.

When a mobile application registers itself with Push, it can receive broadcasts. Broadcasts are notifications that are general and are sent to all applications. The mobile application can also receive notifications for specific tags by subscribing to Push and including the tag name.

The retrieveAvailableTagsWithCompletionHandler API returns the list of available tags to which the device can subscribe. After the device is subscribed to a particular tag, the device can receive any push notifications that are sent for that tag.
Calling the push service to get subscriptions to a tag

[push retrieveSubscriptionsWithCompletionHandler: ^(IMFResponse *response, NSError *error) { if(error){ [self updateMessage:@"Error while retrieving subscriptions."]; [self updateMessage:error.description]; } else { [self updateMessage:@"Successfully retrieved subscriptions."]; NSDictionary *subscribedTags = [[NSDictionary alloc]init]; subscribedTags = [response subscriptions]; [self updateMessage:@"Subscribed tags array is: "]; [self updateMessage:subscribedTags.description]; } }];

Subscribing to a tag
Use the subscribeToTags API to subscribe to a tag.

[push subscribeToTags:tags completionHandler: ^(IMFResponse *response, NSError *error) { if(error){ [self updateMessage:error.description]; }else{ NSDictionary* subStatus = [[NSDictionary alloc]init]; subStatus = [response subscribeStatus]; [self updateMessage:@"Parsed subscribe status is:"]; [self updateMessage:subStatus.description]; } }];

Unsubscribing from a tag
Use the unsubscribeFromTags API to unsubscribe from a tag.

[push unsubscribeFromTags:tags completionHandler: ^(IMFResponse *response, NSError *error) { if (error){ [self updateMessage:error.description]; } else { NSDictionary* subStatus = [[NSDictionary alloc]init]; subStatus = [response unsubscribeStatus]; [self updateMessage:subStatus.description]; } }];

Getting a list of available tags
Use the GetSubscriptions API to get a list of tags that to which the device is subscribed.

[push retrieveSubscriptionsWithCompletionHandler: ^(IMFResponse *response, NSError *error) { if(error){ [self updateMessage:error.description]; } else { [self updateMessage:@"Successfully retrieved subscriptions."]; NSDictionary *subscribedTags = [[NSDictionary alloc]init]; subscribedTags = [response subscriptions]; [self.appDelegateVC updateMessage:subscribedTags.description]; } }];

Unregistering a device
Use the unregisterDevice API to unregister your device from the Push service.

[push unregisterDevice:^(IMFResponse *response, NSError *error) { if (error){[ self updateMessage:error.description];} else {[ self updateMessage: @"Device successfully unregistered."];} }];

Managing notifications

You can use the Push for iOS8 dashboard to send regular, mixed, and silent notifications and you can further configure your application for interactive notifications.

    Registering the application with APNs
    Enabling a device to receive notifications
    Enabling actionable notifications
    Handling actionable notifications
    Sending silent notifications
    Interactive notifications

Registering the application with APNs

An application (app) must register with Apple Push Notification service (APNs) to receive remote notifications, which usually happen after the app is installed on a device. After the device token generated by APNs is received by the app, it must be passed back to Push for iOS8.

    To enable your app to retrieve the token and receive notifications, add the following method to the application delegate of your application.

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions { if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0) { [[UIApplication sharedApplication] registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge) categories:categories]]; [[UIApplication sharedApplication] registerForRemoteNotifications]; }else{ [[UIApplication sharedApplication] registerForRemoteNotificationTypes: (UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)]; } return YES; }

    After the token is received from APNs, pass the token to Push for iOS8 as part of the registerDevice:withConsumerId:withDeviceToken method.

    -(void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken{ IMFClient *client = [IMFClient sharedInstance]; [client initializeWithBackendRoute:@"your-backend-route-here" backendGUID:@"Your-backend-GUID-here"]; [[IMFGoogleAuthenticationHandler sharedInstance] registerWithDefaultDelegate]; // get Push instance IMFPushClient* push = [IMFPushClient sharedInstance]; // set current working environment push.environment = @"production"; [push registerDeviceToken:deviceToken completionHandler:^(IMFResponse *response, NSError *error) { if (error){ [self updateMessage:error.description]; } else { [self updateMessage:response.responseJson.description]; } }];

Enabling a device to receive notifications
To receive push notifications on a device, implement the following method in the application delegate of the application.

-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo { //userInfo dictionary will contain data sent from server. }

Enabling actionable notifications

Unlike traditional push notifications, actionable notifications prompt users to make a selection upon receipt of the notification alert without opening the app. Use the following instructions to enable actionable push notifications in your application.

    Create a user response action, such as accept.

    UIMutableUserNotificationAction *acceptAction = [[UIMutableUserNotificationAction alloc] init]; acceptAction.identifier = @"ACCEPT_ACTION"; acceptAction.title = @"Accept"; /* Optional properties acceptAction.destructive = NO; acceptAction.authenticationRequired = NO; */

    Create the notification category and set an action. UIUserNotificationActionContextDefault or UIUserNotificationActionContextMinimal are valid contexts.

    UIMutableUserNotificationCategory *callCat = [[UIMutableUserNotificationCategory alloc] init]; callCat.identifier = @"POLL_CATEGORY"; [callCat setActions:@[acceptAction, declineAction] forContext:UIUserNotificationActionContextDefault];

    Create the notification setting and assign the categories from the previous step.

    NSSet *categories = [NSSet setWithObjects:callCat, nil];

    Create the local or remote notification and assign it the identity of the category.

    [[UIApplication sharedApplication] registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge) categories:categories]]; [[UIApplication sharedApplication] registerForRemoteNotifications];

Handling actionable notifications
When an actionable notification is received, the control is passed onto the following method based on the identifier chosen.

- (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification: ( UILocalNotification *)notification completionHandler:(void (^)())completionHandler { NSLog(@"actionable notification received."); //TODO: Client //must call completion handler when finished completionHandler(); }

Sending silent notifications

Silent notifications do not appear on the device screen. These notifications are received by the application in the background, which wakes up the application for up to 30 seconds to perform the specified background task. A user might not be aware of the notification arrival.
To send silent push notifications, implement the following method in the appDelegate.m. For silent notifications, the contentAvailable value that is sent by the server is equal to 1.

- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler { NSNumber *contentAvailable = userInfo[@"aps"][@"content-available"]; if([contentAvailable intValue]== 1){ [[IMFPushClient sharedInstance] application:application didReceiveRemoteNotification:userInfo]; //Perform background task NSLog(@"Received a silent push.."); NSLog(@"userInfo: %@", userInfo.description); _appDelegateVC.result.text = userInfo.description; handler(UIBackgroundFetchResultNewData); } else{ //Normal Notification [[IMFPushAppManager get] notificationReceived:userInfo]; NSLog(@"Received a normal notification."); NSLog(@"userInfo: %@", userInfo.description); _appDelegateVC.result.text = userInfo.description; handler(UIBackgroundFetchResultNoData); } //Success }

Interactive notifications

Interactive notifications allow users to respond to notification alerts when they arrive and without opening an app to do so. When an interactive notification arrives on a device, an alert prompts the user to make a selection.
Tip: Interactive notifications are supported on iOS devices with version 8 and later. If an interactive notification is sent to an iOS device with a previous version, the notification actions are not displayed.
Sending interactive push notifications
You can use the Push dashboard or REST API to send interactive notifications. To send interactive notifications from the dashboard, start with the following steps:

    On the Notifications tab, click Send Notification, choose recipients, and then click Next.
    For an interactive notification, you must select either the Default or Mixed notification type.
    Under Advanced Options, you must specify a Category.

To configure a category value for interactive notifications, which is required for receiving interactive notifications, enable the application to perform background tasks for receiving remote notifications. (This task is also required for some actions that are background-enabled.)
Configuring interactive push notifications

    Before you set the deviceToken on WLPush Object, set the categories in the AppDelegate.

    if([application respondsToSelector:@selector(registerUserNotificationSettings:)]){ UIUserNotificationType userNotificationTypes = UIUserNotificationTypeNone | UIUserNotificationTypeSound | UIUserNotificationTypeAlert | UIUserNotificationTypeBadge; UIMutableUserNotificationAction *acceptAction = [[UIMutableUserNotificationAction alloc] init]; acceptAction.identifier = @"OK"; acceptAction.title = @"OK"; UIMutableUserNotificationAction *rejetAction = [[UIMutableUserNotificationAction alloc] init]; rejetAction.identifier = @"NOK"; rejetAction.title = @"NOK"; UIMutableUserNotificationCategory *cateogory = [[UIMutableUserNotificationCategory alloc] init]; cateogory.identifier = @"poll"; [cateogory setActions:@[acceptAction,rejetAction] forContext:UIUserNotificationActionContextDefault]; [cateogory setActions:@[acceptAction,rejetAction] forContext:UIUserNotificationActionContextMinimal]; NSSet *catgories = [NSSet setWithObject:cateogory]; [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:userNotificationTypes categories:catgories]]; }

    Implement the new callback method in AppDelegate:

    -(void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forRemoteNotification:(NSDictionary *)userInfo completionHandler:(void (ˆ)())completionHandler

    The new callback method gets invoked when the user clicks the action button. The method must perform the action that is associated with the specified identifier and run the block in the completionHandler parameter.

Monitoring

Push for iOS8 provides the capability to track and monitor data about push-related events. After you implement the code in your application, the resulting data is displayed in graphs and charts in the Bluemix dashboard for the push service.
To enable tracking for Push analytics, add the following code to your application.

//Example for using silent notifications - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler { NSNumber *contentAvailable = userInfo[@"aps"][@"content-available"]; if([contentAvailable intValue]== 1){ [[IMFPushClient sharedInstance] application:application didReceiveRemoteNotification:userInfo]; } else{ //Normal Notification [[IMFPushClient sharedInstance] application:application didReceiveRemoteNotification:userInfo]; } }} //Example for using the default notifications - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo { [[IMFPushClient sharedInstance] application:application didReceiveRemoteNotification:userInfo]; } //

The following metrics are presented in the Push dashboard:

    The number of push notifications that were sent during the past 30 days.
    The number of mobile devices that registered for push notifications during the past 30 days.
    The distribution of notifications among the different types of registered devices.
    Information about notifications that devices received successfully and notifications that were viewed by the user.
    Instances where the application was opened on a device, which might or might not be a result of receiving the push notification.

