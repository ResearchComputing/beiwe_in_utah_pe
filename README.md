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

* _`./containers`_
  * has the containers for each of the three software packages noted above. This *is* the software.
* _`./data`_
  * has the PHOENIX directory that is used to store the Biewe data hierarchy
* _`./pipeline`_
  * has the scripts/protocols needed to run the three steps of the Biewe pipeline in these three subdirectories:
    * _`./pipeline/step1_download`_
      * Runs [Lochness](http://docs.neuroinfo.org/lochness/en/latest/quick_start.html) for data setup and download
    * _`./pipeline/step2_postproc`_
      * Runs [Logbook](http://docs.neuroinfo.org/logbook/en/latest/quick_start.html) for data postprocessing
      * _Running this package may not be necessary_
    * _`./pipeline/step3_viz`_ 
      * Runs [Dpdash](http://docs.neuroinfo.org/dpdash/en/latest/index.html) for data visualization
      * _Running this package may not be necessary. I presently have been unable to get it running and this is not presently covered in this tutorial._
      
...and we also see there are two files:
* a `_README_` file that has instructions on how to run your pipeline.  This is essentially the same material in this tutorial.
* `_TERA_test_metadata.csv_`: This is a template that you'll use when you add subjects. More on using this below. 

In the following sections we will go through each step of the pipeline: _Lochness_, _Logbook_, and _DPDash_.

### Running _Lochness_ to set up and download data to the PHOENIX data directory

#### Step 1:  Setting up your keyring file

There is presently an unencrypted keyring file called _lochness.json_ in the _./data_ subdirectory.  The file contains a url for the Beiwe instance as well as passwords to access data. We will remove this after our tutorial as you will have a copy in your home directory (which is more secure). Note that when you encrypt this file you will also you will be asked to generate a password that will be used in subsequent steps. Remember this password. 

Encrypt the file _(you can run this from the command line - it only takes a few seconds)_:

```
module load singularity/3.3.0 
cp /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/.lochness.json ~
singularity exec /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/containers/lochness.sif \
crypt.py --encrypt ~/.lochness.json --output-file ~/.lochness.enc
```
_enter any password you want to use._

Now add your password _(substitute it for <password> below)_ to a file in your home directory, where it is protected and can be referenced by other scripts:

```echo <password> > ~/.lochness.pass```

### Step 2: Now generate a PHOENIX directory. This is where data will be downloaded

_(you can run this from the command line - it only takes a few seconds)_:

```
singularity exec /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/containers/lochness.sif \
phoenix-generator.py --study TERA_test /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/PHOENIX
```

### Step 3: Now edit and copy a beiwe.colorado-specific TERA_test_metadata.csv file to the PHOENIX directory.


#note that you'll need to add new patients to this csv file and copy it into the PHOENIX directory each
#time you enroll new patients

cp ./TERA_test_metadata.csv \
/uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/PHOENIX/GENERAL/TERA_test/TERA_test_metadata.csv

#############
#Step 1d: Now dowload the data with the lochness "sync.py" script.
#This will download everything the first time you run it.
#Each subsequent time you run it, it only downloads new data.
#Note that you'll need to use the password you set above when you used "crpyt.py" to encrypt the json file

#this how you do it from the command line:
singularity exec ./containers/lochness.sif sync.py --config ./pipeline/step1_download/config.yaml --source beiwe
[enter password when prompted]

#but it is better to do it from a job (as it may take a while and you may want to automate)
#note that the password will be automatically passed to the batch job from your ~/.lochness.pass file
cd /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/pipeline/step1_download
sbatch batch_lochness.sh

# You are done with lochness!
# Note that you won't need to rerun Steps 1a and 1b unless you develop a new study
# You'll need to rerun Step 1c when you add new participants
# And you'll need to run Step 1d intermittently to bring your data up-to-date.

##########################################################################################
##########################################################################################
# Step 2: run logbook to postprocess the raw datastream that you downloaded with logbook

#############
#e.g., for phone accelerometer data data 1 to 2 for TERA_test study SUBJECT_1
singularity exec ./containers/logbook.sif lb.py --phoenix-dir /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/PHOENIX --consent-dir /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/PHOENIX/GENERAL --log-dir /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/pipeline/step2_postproc/logs --data-type phone --phone-stream accelerometer --day-from 1 --day-to 2 --study TERA_test --subject SUBJECT_1

#but it is better to do it from a job (as it may take a while and you may want to automate)
cd /uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/pipeline/step2_postproc
sbatch batch_logbook.sh
#note that you may want to edit batch_logbook should you need to postprocess specific
#subjects or data streams
#
##########################################################################################
##########################################################################################
# Step 3: run dpdash to vizualize the postprocessed data from logbook
#
#Step 3a: start an interactive job
#e.g., for a 2 hour job
srun --time=2:00:00 --ntasks 2 --account=owner-guest --partition=redwood-guest --qos=redwood-guest  --pty /bin/bash -l
module load singularity/3.3.0

#Step 3b: export needed variables
cd ./pipeline/step3_viz/dpdash/singularity/
export state=/uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/pipeline/step3_viz/state
export data=/uufs/chpc.utah.edu/common/HIPAA/proj_TERA/beiwe/data/PHOENIX

#Step 3c: initialize dpdash
bash init.sh ${data} ${state}

#Step 3d: start dpdash
singularity instance start --bind ${state}:/data --bind ${data}:${data} dpdash2.img dpdash
# (note that this command may fail the first time. Try again if that happens)

# Step 3e:

