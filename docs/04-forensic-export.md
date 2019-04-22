# Module 4: Exporting for Forensic Analysis

In this module we will discuss methods for exporting a machine image of a compromised EC2 instance for forensic analysis.  We will be exploring two different export scenarios:

    1. Creating a snapshot of the compromised machine and sharing it with a second AWS account (such as the central infosec account).
    2. Creating an image of the server's block devices in S3 for download to a non-AWS environment for analysis (such as exporting to an on-prem forensic environment).
    
