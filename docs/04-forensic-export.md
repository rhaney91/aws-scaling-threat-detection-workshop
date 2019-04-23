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


Next, go to the Snapshots menu on the left hand side.  You'll see the  forensic snapshot listed:

Select the snapshot, and go to Actions -> Modify Permissions.  In this section, enter the AWS account number of your partner's account.  After you and your partner have completed this section, you should be able to refresh the Snapshots screen and see your partner's shared snapshot.  In this scenario, the source account still owns the snapshot, and can choose to stop sharing or delete the snapshot at will.  Since this is part of a forensic investigation, we're going to copy the shared snapshot in to your account, the forensics account, so that there is a permanent record.  

Select the snapshot shared with your account, and go to Actions -> Copy.  Here you can choose to encrypt the snapshot with a key in your own account as well.  Copy the snapshot to your account.  Note down the description and Snapshot ID of the newly copied snapshots.  

Reminder: We're doing this step to fully copy the snapshot into our local account, which is the information security/forensics account.

Next we're going to convert the snapshot to an EBS volume so we can mount it to a server for additional investigation.  From the snapshots menu, select the newly created snapshot and go to Actions -> Create volume and create the volume:

Make a note of which availability zone in which the volume is created and the volume ID.  EBS volumes can only be mapped to EC2 instances in the same availability zone!  

Next, go to the Instances menu on the left hand side and select "Launch Instance".  

Select the Amazon Linux 2 AMI using 64-bit (x86) architecture, and hit "Select".  

On the next screen, select a t2.micro instance and select "Review and Launch", then hit the blue "Launch" button in the lower-right hand side of the screen.    

Create a new key pair, and download the PEM key.  Note, if you use Putty for your terminal you'll need to convert the PEM key to a PPK key using Puttygen.  