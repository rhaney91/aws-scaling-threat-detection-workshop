# Module 4: Exporting for Forensic Analysis

In this module we will discuss methods for exporting a machine image of a compromised EC2 instance for forensic analysis.  We will be exploring two different export scenarios:

    1. Creating a snapshot of the compromised machine and sharing it with a second AWS account (such as the central infosec account).
    2. Creating an image of the server's block devices in S3 for download to a non-AWS environment for analysis (such as exporting to an on-prem forensic environment).
    
The CloudFormation script you executed in Module 2 installed an EC2 instance in us-west-2.  

First, pair off with a partner for this exercise.  You'll be sharing your snapshot of the compromised machine with your partner's AWS account, simulating that their account is the cloud forensic account.

Next, log in to your account and make sure you're in Oregon (us-west-2).  Make a note of your account ID by clicking on the role drop-down in the upper right hand side of the AWS console:

![Account ID](./images/mod4-1.png)

 Navigate to the EC2 service and select the EC2 instance with the name "threat-detection-wksp: Compromised Instance".  Make a note of the instance id, you'll need it to find the EBS volume(s) associated with the account.

Next, navigate to the "Volumes" option on the left-hand side of the page.  On the Volumes page, find the volume(s) attached to the EC2 instance ID:

![Volume ID](./images/mod5-1.png)

Select the volume, and go to Actions -> Create Snapshot.  Add a description for the snapshot, and create a new tag with a key of "Description" and a value of "forensics".  Note: in a real life scenario you can tag a snapshot with a ticket or incident ID to facilitate tracking.  

![Create Snapshot](./images/mod5-2.png)

Next, go to the Snapshots menu on the left hand side.  You'll see the  forensic snapshot listed:

Select the snapshot, and go to Actions -> Modify Permissions.  In this section, enter the AWS account number of your partner's account.  

![Snapshot Copy](./images/mod5-4.png)

After you and your partner have completed this section, you should be able to refresh the Snapshots screen and see your partner's shared snapshot.  In this scenario, the source account still owns the snapshot, and can choose to stop sharing or delete the snapshot at will.  Since this is part of a forensic investigation, we're going to copy the shared snapshot in to your account, the forensics account, so that there is a permanent record.  

Select the snapshot shared with your account, and go to Actions -> Copy.  Here you can choose to encrypt the snapshot with a key in your own account as well.  Copy the snapshot to your account.  Note down the description and Snapshot ID of the newly copied snapshots.  

Reminder: We're doing this step to fully copy the snapshot into our local account, which is the information security/forensics account.

Next, we're going to create an EC2 instance where we can mount our snapshot for investigation.  Go to the Instances menu on the left hand side and select "Launch Instance".  

![EC2 start](./images/mod5-7.png)

Select the Amazon Linux 2 AMI using 64-bit (x86) architecture, and hit "Select".  

![AMI](./images/mod5-8.png)

On the next screen, select a t2.micro instance and select "Review and Launch", then hit the blue "Launch" button in the lower-right hand side of the screen.    

Create a new key pair, and download the PEM key.  Note, if you use Putty for your terminal you'll need to convert the PEM key to a PPK key using Puttygen, <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html" target="_blank">click here for more information</a>.  

![PEM Key](./images/mod5-9.png)

Next, find the IP address for your newly create instance (if you click on the instance it's the Public DNS (IPv4) address listed, it will look like ec2-00-00-00-00.us-west2.compute.amazonaws.com).  Log in to the EC2 instance using Putty or a terminal of your choice.  The login user is "ec2-user" and no password is needed since we're using keyed SSH.  Make a note of which availability zone your instance is in, and the instance ID - we'll need that for the next step! 

![Login](./images/mod5-10.png)

Next we're going to convert the snapshot to an EBS volume so we can mount it to a server for additional investigation.  Browse to the snapshots menu on the left hand side, then select the newly created snapshot and go to Actions -> Create volume and create the volume.  Make sure you change the availability zone to match the zone your instance was created in just a moment ago, EBS volumes can only be mapped to EC2 instances in the same availability zone.  Note the volume ID after creation:

![Convert](./images/mod5-5.png)

Browse to the Volumes menu on the left hand side and you should see your new volume.  Select the volume, go to Actions -> Attach volume, and click in the "Instance" field of the pop-up.  This should contextually bring up the EC2 instances you can attach the volume to - attach it to the instance ID for the instance we just created. 

![Attach](./images/mod5-11.png)




