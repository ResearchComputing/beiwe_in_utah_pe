## Working with Beiwe data in the Utah Protected Environment (PE)

__Overview__: Data collected by the [Beiwe](https://www.beiwe.org) smartphone app is managed by a backend server running in a HIPPA-protected environment in the public cloud on [Amazon Web Services](https://aws.amazon.com) (AWS).  In order to postprocess and analyze this data, it must be transferred to a locally-accessible HIPAA-protected storage and computing environment.  Because CU Boulder Research Computing cannot presently accommodate sensitive data, the [Center for High Performance Computing](https://www.chpc.utah.edu) (CHPC) at the University of Utah, through a collaborative grant with CU and CSU, has made their [Protected Environment](https://www.chpc.utah.edu/resources/ProtectedEnvironment.php) (PE) available to CU Researchers with HIPAA-sensitive data needs. This tutorial will demonstrate how to access the Utah PE and work with Beiwe data within it.

### Log  into the Utah PE

__Prerequisites:__
* You have obtained a CHPC/PE account
* You have the [_Cisco AnyConnect_](https://oit.colorado.edu/services/network-internet-services/vpn) VPN client on your laptop/desktop computer.

#### Step 1: Connect to the Utah VPN

* Open your Cisco Anytime Connect client 
* In the “Connect” section manually type “vpnaccess.utah.edu” and then click the “connect” button. This will open a dialog box. Enter the following:
  * _Group:_ “2FA-DuoSecurity”
  * _Username:_ uXXXXXXX (make sure to use your 7-digit numeric ID, not preceded by a zero (0): for example, I used u6028624 and not u06028624
  * _Password:_ (enter your Utah password)
  * _Second Password:_ literally type “push”
  * Click “OK”
* Accept the Duo push on your smartphone. 
* This will log you into the Utah VPN

<p align="middle">
  <img src="https://github.com/ResearchComputing/beiwe_in_utah_pe/blob/master/utah_vpn.png"/>
</p>


#### Step 2: Login to “redwood”:

* open a terminal on your laptop
* type `$ ssh uXXXXXXX@redwood.chpc.utah.edu`
  * e.g., for me: 
    * `$ ssh u6028624@redwood.chpc.utah.edu`
* enter password, choose option 1 for Duo push, and accept the push on your smartphone

<p align="middle">
  <img src="https://github.com/ResearchComputing/beiwe_in_utah_pe/blob/master/utah_login.png"/>
</p>

### Familiarization with PE environment

When you login you'll be in your home directory on _"redwood"_

```
[u6028624@redwood1 ~]$ pwd
/uufs/chpc.utah.edu/common/HIPAA/u6028624
```

The group directory where Beiwe data is located is called _proj_TERA_. Let's "cd" to the directory and look around:
```
[u6028624@redwood1 ~]$ cd ../proj_TERA
[u6028624@redwood1 proj_TERA]$ pwd
/uufs/chpc.utah.edu/common/HIPAA/proj_TERA
[u6028624@redwood1 proj_TERA]$ ls
beiwe
```

You'll see a directory within called _"beiwe"_.  Let's go in:

```
[u6028624@redwood1 proj_TERA]$ cd beiwe/
[u6028624@redwood1 beiwe]$ ls -l
total 12
drwxrwx---+ 2 u6028624 kaiser      79 Apr 22 21:49 containers
drwxrwx---+ 3 u6028624 kaiser      29 Apr 29 21:00 data
drwxrwx---+ 5 u6028624 kaiser      83 Apr 29 22:15 pipeline
-rw-rw----  1 u6028624 proj_TERA 6083 Apr 29 23:50 README
-rw-rw----  1 u6028624 proj_TERA  471 Apr 29 21:21 TERA_test_metadata.csv
```

We can see there are three subdirectories:

* _./containers_
  * has the containers for each of the three software packages noted above. This *is* the software.
* _./data_
  * has the PHOENIX directory that is used to store the Biewe data hierarchy
* _./pipeline_
  * has the scripts/protocols needed to run the three steps of the Biewe pipeline in these three subdirectories:
    * _./pipeline/step1_download_
    * _./pipeline/step2_postproc_
    * _./pipeline/step3_viz_ (we won't be using this)

...and we also see there are two files:
* a _README_ file that has instructions on how to run your pipeline.  This is essentially the same material in this tutorial.
* _TERA_test_metadata.csv_: This is a template that you'll use when you add subjects. More below. 

### Submitting jobs to download data
### Submitting jobs to postprocess data
