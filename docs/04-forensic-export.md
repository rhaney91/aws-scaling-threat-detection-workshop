# Module 4: Exporting for Forensic Analysis

In this module we will discuss methods for exporting a machine image of a compromised EC2 instance for forensic analysis.  We will be exploring two different export scenarios:

    1. Creating a snapshot of the compromised machine and sharing it with a second AWS account (such as the central infosec account).
    2. Creating an image of the server's block devices in S3 for download to a non-AWS environment for analysis (such as exporting to an on-prem forensic environment).
    
The CloudFormation script you executed in Module 2 installed an EC2 instance in us-west-2.  There are a few things you should know about the instance:

    1. It's CentOS 7.6 x86_64.
    2. An attacker compromised the instance at some point in the last few days.
    3. The attack vector is unknown, but the instance does have public access enabled on a few different ports.
    4. The attacker seems to have disabled remote SSH logins.

First, pair off with a partner for this exercise.  You'll be sharing your snapshot of the compromised machine with your partner's AWS account, simulating that their account is the cloud forensic account.

Next, log in to your account and make sure you're in Oregon (us-west-2).  Navigate to the EC2 service and select the EC2 instance with the tag "Description" set to "forensics".  Make a note of the instance id, you'll need it to find the EBS volume(s) associated with the account.

Next, navigate to the "Volumes" option on the left-hand side of the page.  On the Volumes page, find the volume(s) attached to the EC2 instance ID:

Select the first volume, and go to Actions -> Create Snapshot.  Add a description for the snapshot, and create a new tag with a key of "Description" and a value of "forensics".  Note: in a real life scenario you can tag a snapshot with a ticket or incident ID to facilitate tracking.  

Take a snapshot of the second volume as well:

Next, go to the Snapshots menu on the left hand side.  You'll see the two forensic snapshots listed:

Select the first snapshot, and go to Actions -> Modify Permissions.  In this section, enter the AWS account number of your partner's account.  Repeat this process for the second snapshot.  After you and your partner have completed this section, you should be able to refresh the Snapshots screen and see your partner's shared snapshots.  In this scenario, the source account still owns the snapshot, and can choose to stop sharing or delete the snapshot at will.  Since this is part of a forensic investigation, we're going to copy the shared snapshot in to your account, the forensics account, so that there is a perminant record.  