

	Pre-requisites:

	• Ensure you have a functioning Windows Server OS running.
	• Assign a static IP address, and make sure it's on the same VLAN as the devices you're imaging.
	• Install the latest CU and patches from Windows Updates.

Enable and Install Windows Deployment Server using Server Manager.

	1. Open "Server Manager" and install the following roles, "Windows Deployment Services", and another window will appear, click on the "Add Features" button, then click the "Next" button:



	2. Click "Next" button once more to skip the 'Features' step.
	



	3. Ensure both "Role services" are selected, and then click next.
	



	4. Click the "Install" button, and now Server Manager will start installing the WDS roles/features.
	


	5. Once the wizard completes the installation process, the setup should appear as shown below, ready for use.




Configure Windows Deployment Server

	1. Locate and open Windows Deployment Server, you will need to configure the server to allow TFTP requests within your organisation. Expand the left pane, and select your server under "Servers", then click "Configure Server".



	2. A setup wizard will appear, click next and ensure you select 'Standalone server'.



	3. Specify a path to the remote installation folder, it is best practice to store the remote installation folder to another partition/drive. Once you've specified a location, click "Next".



	4. Select "Respond to all client computers (known and unknown)", and click next. 




	5. Ensure that your WDS server is running with no errors, refer to the screenshot below.
	



Configuring and adding boot images into Windows Deployment Server.

	MDT Microsoft Deployment Tool Kit
	
	1. Find your deployment share, and inside the Boot folder, you should see the "LiteTouchPE_x64.wim" file. Note that your file might have been renamed, along with your deployment share. For this tutorial, I'm using the original folder path and boot image.



	2. Copy the "LiteTouchPE_x64.wim" to your Windows Deployment Server, then import the .wim boot image file into the Windows Deployment Server. To do this, expand the left pane, right-click the "Boot Images" folder, and select "Add Boot Image…"



	3. A "Add Image Wizard" will appear, specify the boot image location to import and click 'Next' when you're ready.
	


	4. You can specify the image name or description if you like, otherwise you can leave it as default and click next to import.





	5. It should start importing the boot image successfully and you should be able to see the below screenshot.
	




Adding the SCCM boot image into WDS

	1. You will need to create a bootable image through the Task Sequence Wizard and copy it to your WDS working directory.
		○ Open Configuration Manager Console
		○ Expand the Software Library on the left pane
		○ Click on the Task Sequences tab on the left pane
		○ Click on the "Create Task Sequence" wizard, follow the prompts to create a bootable boot image iso

	2. Copy the existing boot image to a working directory of your WDS server. If you're using the default boot image in ConfigMgr, you can find the x64 version under "C:\Program Files\Microsoft Configuration Manager\OSD\boot\x64".

Note: Be sure to copy the boot.{PackageID}.wim file, as this is the one that will have all the ConfigMgr binaries. You can rename it to whatever you like when it's copied to a working directory. 



	3. On your WDS servers, create a working directory named "boot_image" and create a sub-folder named "mount", which will be used to mount the wim. Ensure the boot.{PackageID}.wim and the Configuration Manager bootable image are in your working directory.
	



	4. Open an elevated command prompt and mount the boot wim file to a temporary mount directory, mine will be in C:\Users\bmasri\desktop\boot_image\mount. Here's the command that I will also be using.

dism /mount-wim /wimfile:"C:\Users\bmasri\Desktop\boot_image\boot.P0100005.wim" /index:1 /mountdir:"C:\Users\bmasri\Desktop\boot_image\mount"



	5. Now mount the sccm_boot.iso image by double clicking on the file and navigate to /SMS/data. You will see two files named "TsmBootstrap.ini" and "Variables.dat" file. Copy those two files into your WDS working directory. 



	6. Create a folder called "data" in the mount/sms directory, then copy the "TsmBootstrap.ini" and "Variables.dat" files into it



	7. Exit out of the mount directory and open the evaluated command prompt window, type the following command to unmount the wim.
	
dism /unmount-wim /mountdir:"C:\Users\bmasri\Desktop\boot_image\mount" /commit



	8. Import the SCCM boot image you just created using DISM into WDS. Once imported, restart the WDS service by right-clicking on your server in the WDS console, selecting All Tasks, and then choosing restart.



	9. Use a device to boot into PXE and you should be able to boot from two bootable images.


