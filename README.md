## Working with Beiwe data in the Utah Protected Environment (PE)

### Overview

### Log  into the Utah PE

__Prerequisites:__
* You have obtained a CHPC/PE account
* You have the [_Cisco AnyConnect_](https://oit.colorado.edu/services/network-internet-services/vpn) VPN client on your laptop/desktop computer.

#### Step 1: Connect to the Utah VPN

Step 1: Login to Utah VPN (see attached screen shot)

* Open your Cisco Anytime Connect client (see screenshot)
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


Step 2: Login to “redwood”:

* open a terminal on your laptop
* type `$ ssh uXXXXXXX@redwood.chpc.utah.edu`
  * e.g., for me: ssh `u6028624@redwood.chpc.utah.edu`
* enter password, choose option 1 for Duo push, and accept the push on your smartphone

<p align="middle">
  <img src="https://github.com/ResearchComputing/beiwe_in_utah_pe/blob/master/utah_login.png"/>
</p>

