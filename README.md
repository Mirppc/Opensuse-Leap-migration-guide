This is suppose to be a guide on how to do an in-place upgrade of Opensuse to Opensuse Leap 15.2 with minimal issues.

Tested from 42.1 to 15.2 Beta with minimal issues.  See linked youtube series in the video section.


First back up your repos

*sudo cp -Rv /etc/zypp/repos.d /etc/zypp/repos.d.Old*

Then we need to change the existing repos to 15.2.  

    From 42.1
        sudo sed -i 's/42.1/15.2/g' /etc/zypp/repos.d/*.repo
    From 42.2
        sudo sed -i 's/42.2/15.2/g' /etc/zypp/repos.d/*.repo
    From 42.3
        sudo sed -i 's/42.3/15.2/g' /etc/zypp/repos.d/*.repo
    From 15.0
        sudo sed -i 's/15.0/15.2/g' /etc/zypp/repos.d/*.repo
    From 15.1
        sudo sed -i 's/15.1/15.2/g' /etc/zypp/repos.d/*.repo

Now we need to refresh the repositories to see if there have been any that had name changes or no longer exist.  This is time consuming but must be done if you have lots of packages from multiple repos.  Quick and easy way is to turn off all but the OSS, Non-OSS, Update and Packman repo (see mentioned video)

*sudo zypper --gpg-auto-import-keys ref*

Any issues with the above command, make note of the url and open the url in a web browser.  An example of a troublesome one is XFCE having changed it's repo naming scheme in 15.1 verses 15.0 with 15.1 going back to a normal naming scheme.  Some repos also may not be yet built for 15.2 or have been abandoned.  You can manage any of the repo's that need the url changed or need to be deleted using Yast>Software Repositories.  If there is something you need you can make an OpenBuildService account, clone the repo you need and try to build the packages targeting Leap 15.2.

Now that you have your repos all set up, fixed, and updated we will need to do a distro upgrade with downloading the packages only first.  This will download all the packages  needed for a succesful upgrade, if something times out or errors out you can reissue the command with no ill effect as nothing has been changed yet.  **WARNING** Make sure you have enough space in / for the caching of the packages, otherwise this will not work out as both an error log if something goes wrong and the size of cached packages could exceed the default / size set up by older Opensuse installs (using the automated partition setup).

*sudo zypper dup --download-only*

Now once you have all the packages cached it is reccomended you drop into a TTY using **ALT-CTRL-F#** with # being 1-4 being the ones i reccomend so *ALT-CTRL-F2* is the one I would choose.  This step was omitted in the video due to issues with capturing the TTY over HDMI.

Kill X

*sudo init 3*

or

*sudo rcxdm stop*

or 

*sudo systemctl isolate multi-user.target*

Now you can do the Dup

*sudo zypper dup*

Now if all worked out well you can just reboot and see if it worked.  Otherwise as some backup steps continue below.



Lets make sure the kernel images have been set up.

*sudo mkinitrd*

Now reinstall Grub manualy unless you are on EFI.  I have another guide on Grub reinstallation but the commands are as follows presuming /dev/sda is your main boot drive where / /boot are installed.

>sudo grub2-mkconfig -o /boot/grub2/grub.cfg

>sudo grub2-install /dev/sda

Now you should be set to reboot.  

If you are running intel graphics or AMD graphics (without AMDGPU-Pro) then you should be good.  

Drivers:Nvidia

After you reboot and if you are using the Nvidia .Run then here you need to drop into run level 3.  make sure before you rebooted and before you drop into a TTY.  i would do this after you have all the packages cached or during the downloading of the packages.  Download the correct .run for your graphics card.  For Kepler and newer the latest will work.

URL
https://http.download.nvidia.com/XFree86/Linux-x86_64/

During the reboot when you are at the GRUB screen press E to edit.  Look for the Command line part.  Mine looks like as follows

>net.ifnames=0 resume=/dev/disk/by-uuid/fe92bee9-2db5-44fd-85c3-335d1c4ce367 splash=verbose plymouth.enable=0 showopts

We need to add a 3 to that to boot to a TTY with networking enabled.  If things go wrong like source packages are missing or to use irssi or a terminal web interface to communicate and look up help is useful for when things go afoul.

Example using the above as a default basis.

>*net.ifnames=0 resume=/dev/disk/by-uuid/fe92bee9-2db5-44fd-85c3-335d1c4ce367 splash=verbose plymouth.enable=0 showopts 3*

Now hit CTRL-X or F10 to boot (should say something about continuing)

Now Login as root if you can or log in as yourself.  The following command is if you are logged in as root.

Change Directories to where the NVIDIA.Run file you downloaded is located.  in my case i always keep it in the bin directory of my home.

*cd /home/mirppc/bin*

Now we need to execute the run file.  I reccomend using the -e flag to get as many options as possible.  Linked in the video section will be a tutorial on guiding you through the Nvidia install process. Make sure you have DKMS installed and the kernel headers and sources.

*sh ./NVIDIA*.run -e*

Follow the instructions and once that is complete you can do one of two things...

*rcxdm restart*

or

*systemctl reboot*

And now if all went well you should have a working system that has been migrated to Opensuse Leap 15.2.


VIDEOS:

Nvidia Install Video Guide https://youtu.be/kytT_ucujjg

Grub Recovery Guide https://youtu.be/QbZmCo1weLo

Leap 42.1 to 15.2 Migration Videos
    Part 1 the Setup, preperation and package download
    https://www.youtube.com/watch?v=9c_M7itDSeU
    
    Part 2 The execution and post migration cleanup
    https://www.youtube.com/watch?v=bS_kk9pkAkw

If you liked this please help me buy a cup of tea or some food via Librepay or via Paypal.


<noscript><a href="https://liberapay.com/Mir/donate"><img alt="Donate using Liberapay" src="https://liberapay.com/assets/widgets/donate.svg"></a></noscript>

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=9D5J99QNAN88W)


https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=9D5J99QNAN88W&source=url
