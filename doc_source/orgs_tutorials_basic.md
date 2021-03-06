# Tutorial: Creating and Configuring an Organization<a name="orgs_tutorials_basic"></a>

In this tutorial, you create your organization and configure it with two AWS member accounts\. You create one of the member accounts in your organization, and you invite the other account to join your organization\. Next, you use the whitelisting technique to specify that account administrators can delegate only explicitly listed services and actions\. This allows administrators to validate any new service that AWS introduces before they permit its use by anyone else in your company\. That way, if AWS introduces a new service it remains prohibited until an administrator adds the service to the whitelist in the appropriate policy\. The tutorial also shows you how to use blacklisting to ensure that no users in a member account can change the configuration for the auditing logs that are created by AWS CloudTrail\. 

The following illustration shows the main steps of the tutorial\.



**[Step 1: Create Your Organization](#tutorial-orgs-step1)**  
In this step, you create an organization with your current AWS account as the master account\. You also invite one AWS account to join your organization, and you create a second account as a member account\.

**[Step 2: Create the Organizational Units ](#tutorial-orgs-step2)**  
Next, you create two organizational units \(OUs\) in your new organization and place the member accounts in those OUs\.

**[Step 3: Create the Service Control Policies](#tutorial-orgs-step3)**  
You can apply restrictions to what actions can be delegated to users and roles in the member accounts by using service control policies \(SCPs\)\. An SCP is a type of organization control policy\. In this step, you create two SCPs and attach them to the OUs in your organization\.

**[Step 4: Testing Your Organization's Policies](#tutorial-orgs-step4)**  
You can sign in as users from each of the test accounts and see the affects that the SCPs have on the accounts\.

None of the steps in this tutorial incur costs to your AWS bill\. AWS Organizations is a free service\.

## Prerequisites<a name="tut-basic-prereqs"></a>

This tutorial assumes that you have access to two existing AWS accounts \(you create a third as part of this tutorial\), and that you can sign in to each as an administrator\.

The tutorial refers to the accounts as the following:

+ `111111111111` — The account that you use to create the organization\. This account becomes the master account\. The owner of this account has an email address of `masteraccount@example.com`\.

+ `222222222222` — An account that you invite to join the organization as a member account\. The owner of this account has an email address of `member222@example.com`\.

+ `333333333333` — An account that you create as a member of the organization\. The owner of this account has an email address of `member333@example.com`\.

Substitute the values above with the values that are associated with your test accounts\. We recommend that you do not use production accounts for this tutorial\.

## Step 1: Create Your Organization<a name="tutorial-orgs-step1"></a>

In this step, you sign in to account 111111111111 as an administrator, create an organization with that account as the master, and then invite an existing account, 222222222222, to join as a member account\.

1. Sign in to AWS as an administrator of account 111111111111 and open the AWS Organizations console at [https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\.

1. On the introduction page, choose **Create organization**\.

1. In the **Create new organization** dialog box, choose **ENABLE ALL FEATURES**, and then choose **Create organization**\.

1. Choose **Settings** in the upper\-right corner, and confirm that your organization has all features enabled\. 
**Note**  
Instead of enabling all features, which gives you the ability to use the full range of advanced features, you can choose to create an organization that supports only the consolidated billing features\. For more information, see Consolidated billing and All features\. If you previously had a Consolidated Billing family of accounts that you created through the AWS Billing and Cost Management service, that Consolidated Billing family has been converted to an organization with only the consolidated billing features enabled\. You can choose to enable all features in your organization\. To complete this tutorial, you must have all features enabled, or you cannot attach service control policies to your accounts and organizational units\.

You now have an organization with your account as its only member\. This is the master account of the organization\.

### Invite an Existing Account to Join Your Organization<a name="tut-basic-invite-existing"></a>

Now that you have an organization, you can begin to populate it with accounts\. In the steps in this section, you invite an existing account to join and become a member of your organization\.

**To invite an existing account to join**

1. Open the Organizations console at [https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\.

1. Choose the **Accounts** tab\. The star next to the account name indicates that it is the master account\.

   Now you can invite other accounts to join as member accounts\.

1. On the **Accounts** tab, choose **Add account**, and then choose **Invite account**\.

1. In the **Account ID or email** box, type the email address of the owner of the account that you want to invite, similar to the following: **account222@example\.com**

1. Type any text that you want into the **Notes** box\. This text is included in the email that is sent to the owner of the account\.

1. Choose **Invite**\. AWS Organizations sends the invitation to the account owner\.
**Important**  
If you get an exception that indicates that you exceeded your account limits for the organization or that you can't add an account because your organization is still initializing, please contact [AWS Customer Support](https://console.aws.amazon.com/support/home#/)\.

1. For the purposes of this tutorial, you now need to accept your own invitation\. Do one of the following to get to the **Invitations** page in the console:

   + Open the email that was sent by AWS from the master account\. When prompted to sign in, do so as an administrator in the invited member account\. 

   + Open the AWS Organizations console \([https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\) and sign in as an administrator of the member account\. Choose **Invitations**\. The number beside the link indicates how many invitations this account has\.

1. On the **Invitations** page, choose **Accept**\.

1. Sign out of your member account, and sign in again as an administrator in your master account\. 

### Create a Member Account<a name="tut-basic-create-new"></a>

In the steps in this section, you create an AWS account that is automatically a member of the organization\. We refer to this account in the tutorial as 333333333333\.

**Important**  
Currently, if you create an account from within Organizations, you cannot later delete that account\. This, in turn, prevents you from deleting the organization\.

**To create a member account**

1. On the AWS Organizations console, on the **Accounts** tab, choose **Add account**\.

1. For **Full name**, type a name for the account, such as **MainApp Account**\.

1. For **Email**, type the email address of the individual who is to receive communications on behalf of the account\. This value must be globally unique\. No two accounts can have the same email address\. For example, you might use something like **mainapp@example\.com**\.

1. For **IAM role name**, you can leave this blank to automatically use the default role name of `OrganizationAccountAccessRole`, or you can supply your own name\. This role enables you to access the new member account when signed in as an IAM user in the master account\. For this tutorial, leave it blank to instruct Organizations to create the role with the default name\.

1. Choose **Create**\. You might need to wait a short while and refresh the page to see the new account appear on the **Accounts** tab\.
**Important**  
If you get an exception that indicates that you exceeded your account limits for the organization or that you can't add an account because your organization is still initializing, please contact [AWS Customer Support](https://console.aws.amazon.com/support/home#/)\.

## Step 2: Create the Organizational Units<a name="tutorial-orgs-step2"></a>

In the steps in this section, you create organizational units \(OUs\) and place your member accounts in them\. Your hierarchy looks like the following illustration when you are done\. The master account remains in the root\. One member account is moved to the Production OU and the other member account is moved to the MainApp OU, which is a child of Production\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/organizations/latest/userguide/images/orgs-lab-structure.jpg)

**To create and populate the OUs**

1. On the AWS Organizations console, choose the **Organize Accounts** tab, and then choose **Create organizational unit**\.

1. For the name of the OU, type **Production**, and then choose **Create organizational unit**\.

1. Choose your new **Production** OU to navigate into it, and then choose **Create organizational unit \(OU\)**\.

1. For the name of the second OU, type **MainApp**, and then choose **Create organizational unit**\.

   Now you can move your member accounts into these OUs\.

1. At the top of the **Organize accounts** tab, choose your root\.

1. Select the first member account 222222222222, and then choose **Move account**\.

1. In the **Move accounts** dialog box, choose **Production**, and then choose **Select**\.

1. Select the second member account 333333333333, and then choose **Move account**\.

1. In the **Move accounts** dialog box, choose **Production** to expose **MainApp**\. Choose **MainApp**, and then choose **Select**\.

## Step 3: Create the Service Control Policies<a name="tutorial-orgs-step3"></a>

In the steps in this section, you create three service control policies \(SCPs\) and attach them to the root and to the OUs to restrict what users in the organization's accounts can do\. The first SCP prevents anyone in any of the member accounts from creating or modifying any CloudTrail logs that you configure\. The master account is not affected by any SCP, so after you apply the CloudTrail SCP you must create any logs from the master account\.

**To create the first SCP that blocks CloudTrail configuration actions**

1. Choose the **Policies** tab, and then choose **Create policy**\.

1. Choose **Policy generator**\.

1. For **Policy name**, type **Block CloudTrail Configuration Actions**\.

1. For **Choose Overall Effect**, choose **Deny**\.

1. In the **Statement builder**, for **Select service** select **AWS CloudTrail**\. For **Select action**, choose the following actions: **AddTags**, **CreateTrail**, **DeleteTrail**, **RemoveTags**, **StartLogging**, **StopLogging**, and **UpdateTrail**\.

1. Choose **Add statement** to add it to the list of statements for the SCP, and then choose **Create policy** to save it to your organization\.

1. Choose the new policy in the list, and then choose **Policy detail** to view the new policy's JSON content\. It looks similar to the following:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "Stmt1234567890123",
         "Effect": "Deny",
         "Action": [       
           "cloudtrail:AddTags",    
           "cloudtrail:CreateTrail",       
           "cloudtrail:DeleteTrail",       
           "cloudtrail:RemoveTags",       
           "cloudtrail:StartLogging",       
           "cloudtrail:StopLogging",       
           "cloudtrail:UpdateTrail"
         ],
         "Resource": [
           "*"
         ]
       }
     ]
   }
   ```

The second policy defines a whitelist of all the services and actions that you want to enable for users and roles in the Production OU\. When you are done, users in the Production OU are able to access ***only*** the listed services and actions\.

**To create the second policy that whitelists approved services for the Production OU**

1. Choose **All policies** to go back to the list, choose **Create policy**, and then choose **Policy generator**\.

1. For **Policy name**, type **Whitelist for All Approved Services**\.

1. For **Choose Overall Effect**, choose **Allow**\.

1. In the **Statement builder**, select **Amazon EC2**\. For **Select action**, choose **Select All**, and then choose **Add statement**\.

1. Repeat the preceding step for each of the following services: **Amazon DynamoDB**, **Amazon S3**, **Elastic Load Balancing**, **AWS CodeCommit**, **AWS CloudTrail**, and **AWS CodeDeploy**\.

   Choose **Select All** for the actions for each service, and then choose **Add another statement**\.

1. When you are done, choose **Create policy** to save the SCP\.

1. Choose the new policy in the list, then choose **Policy detail** to view the new policy's JSON content\. It looks similar to the following \(shown here with some formatting compressed to save space\):

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [ "ec2:*" ],
         "Resource": [ "*" ]
       },
       {
         "Effect": "Allow",
         "Action": [ "s3:*" ],
         "Resource": [ "*" ]
       },
       {
         "Effect": "Allow",
         "Action": [ "elasticloadbalancing:*" ],
         "Resource": [ "*" ]
       },
       {
         "Effect": "Allow",
         "Action": [ "codecommit:*" ],
         "Resource": [ "*" ]
       },
       {
         "Effect": "Allow",
         "Action": [ "cloudtrail:*" ],
         "Resource": [ "*" ]
       },
       {
         "Effect": "Allow",
         "Action": [ "codedeploy:*" ],
         "Resource": [ "*" ]
       }
     ]
   }
   ```

The final policy provides a blacklist of services that are blocked from use in the MainApp OU\. For this tutorial, you block access to DynamoDB in any accounts that are in the MainApp OU\.

**To create the third policy that blacklists services that cannot be used in the MainApp OU**

1. Choose **All policies** to go back to the list, choose **Create policy**, and then choose **Policy generator**\.

1. For **Policy name**, type **Blacklist for MainApp Prohibited Services**\.

1. For **Choose Overall Effect**, choose **Deny**\.

1. In the **Statement builder**, select **Amazon DynamoDB**\. For **Select action**, choose **Select All**, and then choose **Add another statement**\.

1. Choose **Create policy** to save the SCP\.

1. Choose the new policy in the list, and then choose **Policy detail** to view the new policy's JSON content\. It looks similar to the following \(shown here with some formatting compressed to save space\):

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Deny",
         "Action": [ "dynamodb:*" ],
         "Resource": [ "*" ]
       }
     ]
   }
   ```

### Enable the Service Control Policy Type in the Root<a name="tut-basic-enable-scp"></a>

Before you can attach a policy of any type to a root or to any OU within a root, you must enable the policy type for that root\. Policy types are not enabled in any root by default\. The steps in this section show you how to enable the service control policy \(SCP\) type for the root in your organization\.

**Note**  
Currently, you can have only one root in your organization\. It is created for you and named **Root** when you create your organization\.

**To enable SCPs for your root**

1. On the **Organize accounts** tab, choose **Home** to see the list of available roots, and then select the check box for your root\.

1. In the Details pane on the right, under **Service Control** and next to **Service control policies are not enabled**, choose **Enable**\.

### Attach the SCPs to Your OUs<a name="tut-basic-attach-scp"></a>

Now that the SCPs exist and they are enabled for your root, you can attach them to the root and OUs\.

**To attach the policies to the root and the OUs**

1. On the **Organize accounts** tab, choose **Home** to see the list of available roots, and then select the check box for your root\.
**Note**  
Currently, an organization can contain only one root\. It is created automatically when you create the organization, and is named "Root"\.

1. In the information panel on the right side of the window, expand **CONTROL POLICIES**, and then choose **Attach policy**\.

1. Attach the SCP named `Block CloudTrail Configuration Actions` to prevent anyone from altering the way that you configured CloudTrail\. In this tutorial, you attach it to the root so that it affects all member accounts\. 

   In the list of SCPs that are available to be attached to the selected entity, next to **Block CloudTrail Configuration Actions**, select **Attach**\.

   The information panel now shows that two SCPs are attached to the root: the one you just created and the default `FullAWSAccess` SCP\. 

1. Choose the main box for the **Root** \(not the check box\) to navigate to its contents\.

1. Attach the SCP named `Whitelist for All Approved Services` to enable users or roles in member accounts in the Production OU to access the approved services\.

   Select the check box for the **Production** OU\.

1. In the information panel on the right side of the window, expand **CONTROL POLICIES**, and then choose **Attach policy**\.

1. In the list of SCPs that are available to be attached to the selected entity, next to **Whitelist for All Approved Services**, choose **Attach**\.

   The information panel now shows that two SCPs are attached to the root: the one that you just attached and the default `FullAWSAccess` SCP\. However, because the `FullAWSAccess` SCP is also a whitelist that allows all services and actions, you must detach this SCP to ensure that only your approved services are allowed\.

1. To remove the default policy from the Production OU, next to **FullAWSAccess**, choose the **X**\. After you remove this default policy, all member accounts under the root immediately lose access to all actions and services that are not on the whitelist SCP that you attached in the preceding step\. Even if an administrator in one of the accounts grants full access to another service by attaching an IAM permission policy to a user in one of the member accounts, any requests to use actions that aren't included in the **Whitelist for All Approved Services** SCP are denied\.

1. Now you can attach the SCP named `Blacklist for MainApp Prohibited services` to prevent anyone in the accounts in the MainApp OU from using any of the restricted services\.

   To do this, select the check box for the **MainApp** OU\.

1. In the information panel, under **POLICIES**, expand the **Service control policies** section\. In the list of available policies, next to **Blacklist for MainApp Prohibited Services**, choose **Attach**\.

## Step 4: Testing Your Organization's Policies<a name="tutorial-orgs-step4"></a>

You now can sign in as a user in any of the member accounts and try to perform various AWS actions:

+ If you sign in as a user in the master account, you can perform any operation that is allowed by your IAM permission policies\. The service control policies do not affect any user or role in the master account, no matter which root or OU the account is located in\.

+ If you sign in as the root user or an IAM user in account 222222222222, you can perform any actions that are allowed by the whitelist\. AWS Organizations denies any attempt to perform an action in any service that isn't in the whitelist\. Also, AWS Organizations denies any attempt to perform one of the CloudTrail configuration actions\.

+ If you sign in as a user in account 333333333333, you can perform any actions that are allowed by the whitelist and not blocked by the blacklist\. AWS Organizations denies any attempt to perform an action that isn't in the whitelist policy and any action that is in the blacklist policy\. Also, AWS Organizations denies any attempt to perform one of the CloudTrail configuration actions\.