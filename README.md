

	Pre-requisites:

	• Ensure you have a functioning Windows Server OS running.
	• Assign a static IP address, and make sure it's on the same VLAN as the devices you're imaging.
	• Install the latest CU and patches from Windows Updates.

Enable and Install Windows Deployment Server using Server Manager.

	1. Open "Server Manager" and install the following roles, "Windows Deployment Services", and another window will appear, click on the "Add Features" button, then click the "Next" button:

![image](https://github.com/user-attachments/assets/9000d478-ae09-4599-b743-843cdf11b0a5)


	2. Click "Next" button once more to skip the 'Features' step.
	

![image](https://github.com/user-attachments/assets/c64c6a08-62ec-4c86-8514-da169757339d)


	3. Ensure both "Role services" are selected, and then click next.

 
![image](https://github.com/user-attachments/assets/f776e1c9-ba2b-4914-b4f7-77cf530a7e12)


	4. Click the "Install" button, and now Server Manager will start installing the WDS roles/features.
	
![image](https://github.com/user-attachments/assets/696fb9d0-25b0-4e7f-80fa-7c5c83c5ad76)


	5. Once the wizard completes the installation process, the setup should appear as shown below, ready for use.

![image](https://github.com/user-attachments/assets/071351f4-a1e1-4471-8704-9e5d23bdf301)



Configure Windows Deployment Server

	1. Locate and open Windows Deployment Server, you will need to configure the server to allow TFTP requests within your organisation. Expand the left pane, and select your server under "Servers", then click "Configure Server".

![image](https://github.com/user-attachments/assets/f37fc64a-2627-4ba0-98a9-4765d50fd9b0)


	2. A setup wizard will appear, click next and ensure you select 'Standalone server'.

![image](https://github.com/user-attachments/assets/bdff8d5f-0cb4-44f7-80cf-47a7e671941f)


	3. Specify a path to the remote installation folder, it is best practice to store the remote installation folder to another partition/drive. Once you've specified a location, click "Next".

![image](https://github.com/user-attachments/assets/6c5b3264-5bb9-4f72-b781-254e068f632f)


	4. Select "Respond to all client computers (known and unknown)", and click next. 

![image](https://github.com/user-attachments/assets/c0bef003-292a-44d6-aeb1-5de2c2d002e6)



	5. Ensure that your WDS server is running with no errors, refer to the screenshot below.
	
![image](https://github.com/user-attachments/assets/4497e9e9-b15c-4faf-bcf0-afcef3a93f74)



Configuring and adding boot images into Windows Deployment Server.

	MDT Microsoft Deployment Tool Kit
	
	1. Find your deployment share, and inside the Boot folder, you should see the "LiteTouchPE_x64.wim" file. Note that your file might have been renamed, along with your deployment share. For this tutorial, I'm using the original folder path and boot image.

![image](https://github.com/user-attachments/assets/82522f87-5c97-46dd-a8c7-6618751e6701)


	2. Copy the "LiteTouchPE_x64.wim" to your Windows Deployment Server, then import the .wim boot image file into the Windows Deployment Server. To do this, expand the left pane, right-click the "Boot Images" folder, and select "Add Boot Image…"

![image](https://github.com/user-attachments/assets/78098c5a-9222-4be4-a7ce-114eed3fbffe)


	3. A "Add Image Wizard" will appear, specify the boot image location to import and click 'Next' when you're ready.
	
![image](https://github.com/user-attachments/assets/e54e109c-7310-47c6-9931-fa0622722bc4)


	4. You can specify the image name or description if you like, otherwise you can leave it as default and click next to import.

![image](https://github.com/user-attachments/assets/558428a2-2ebf-4ed9-b86c-2a4bbf7dd5d8)



	5. It should start importing the boot image successfully and you should be able to see the below screenshot.
	
![image](https://github.com/user-attachments/assets/e53dc556-a6b2-4cfa-b781-5f8eee278684)
![image](https://github.com/user-attachments/assets/61bd1e08-eec0-4946-996b-9414764e71e8)




Adding the SCCM boot image into WDS

	1. You will need to create a bootable image through the Task Sequence Wizard and copy it to your WDS working directory.
		○ Open Configuration Manager Console
		○ Expand the Software Library on the left pane
		○ Click on the Task Sequences tab on the left pane
		○ Click on the "Create Task Sequence" wizard, follow the prompts to create a bootable boot image iso

	2. Copy the existing boot image to a working directory of your WDS server. If you're using the default boot image in ConfigMgr, you can find the x64 version under "C:\Program Files\Microsoft Configuration Manager\OSD\boot\x64".

Note: Be sure to copy the boot.{PackageID}.wim file, as this is the one that will have all the ConfigMgr binaries. You can rename it to whatever you like when it's copied to a working directory. 

![image](https://github.com/user-attachments/assets/3f4ab1d8-60be-4aba-8fc6-8e05a8903bd8)


	3. On your WDS servers, create a working directory named "boot_image" and create a sub-folder named "mount", which will be used to mount the wim. Ensure the boot.{PackageID}.wim and the Configuration Manager bootable image are in your working directory.
	
![image](https://github.com/user-attachments/assets/aaf94a7e-0b2f-48c0-9353-34c232164be6)


	4. Open an elevated command prompt and mount the boot wim file to a temporary mount directory, mine will be in C:\Users\bmasri\desktop\boot_image\mount. Here's the command that I will also be using.

dism /mount-wim /wimfile:"C:\Users\bmasri\Desktop\boot_image\boot.P0100005.wim" /index:1 /mountdir:"C:\Users\bmasri\Desktop\boot_image\mount"

![image](https://github.com/user-attachments/assets/e218067f-cede-4361-9db2-b9cb123b649e)


	5. Now mount the sccm_boot.iso image by double clicking on the file and navigate to /SMS/data. You will see two files named "TsmBootstrap.ini" and "Variables.dat" file. Copy those two files into your WDS working directory. 

![image](https://github.com/user-attachments/assets/e90058f8-d0dc-4a05-92d0-7a9b6fcf7ad5)


	6. Create a folder called "data" in the mount/sms directory, then copy the "TsmBootstrap.ini" and "Variables.dat" files into it

![image](https://github.com/user-attachments/assets/a9d21a13-6190-4708-8bc1-fbe8883316f6)


	7. Exit out of the mount directory and open the evaluated command prompt window, type the following command to unmount the wim.
	
dism /unmount-wim /mountdir:"C:\Users\bmasri\Desktop\boot_image\mount" /commit

![image](https://github.com/user-attachments/assets/4e03709c-4d13-487e-bef7-00937cf7b683)


	8. Import the SCCM boot image you just created using DISM into WDS. Once imported, restart the WDS service by right-clicking on your server in the WDS console, selecting All Tasks, and then choosing restart.

![image](https://github.com/user-attachments/assets/c87bfddc-82fc-4130-9c18-823f2241018d)


	9. Use a device to boot into PXE and you should be able to boot from two bootable images.

![image](https://github.com/user-attachments/assets/31271361-4832-468b-8777-5e51f0af9b59)

