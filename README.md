
## Ubuntu 22.04 LTS Kernel 6.2
```sh
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential -y 
cd ~  # change to your home directory
apt-get source linux-modules-extra-$(uname -r)
cd ~/linux-*/drivers/media/usb/uvc 
mv uvc_driver.c uvc_driver.old
```
Download my version for AN515-58 **Kernel 6.2**
```sh
wget https://raw.githubusercontent.com/rexionmars/Nitro5-AN515-58-WebCam-Module/master/uvc_driver_kernel_6.2/uvc_driver.c
```
Compile new module

```sh
make -j4 -C /lib/modules/$(uname -r)/build M=$(pwd) modules  
sudo cp uvcvideo.ko /lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/ 
```
Finaly
```sh
sudo reboot
```
## Another way to solve the problem ( It works for older and newer versions).
Following the previous steps, plus editing the file `uvc_driver.c`:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential -y 

cd ~  # change to your home directory
apt-get source linux-modules-extra-$(uname -r)
cd ~/linux-*/drivers/media/usb/uvc

vim uvc_driver.c # You can use any text editor
```
In the file, locate the following line.
```c
static const struct usb_device_id uvc_ids[] = {
```
Below the line described above, copy and paste the code below.
```c
/* Quanta ACER HD User Facing  0x4035 - Experimental */
	{ .match_flags 	= USB_DEVICE_ID_MATCH_DEVICE
			| USB_DEVICE_ID_MATCH_INT_INFO,
	  .idVendor = 0x0408,
	  .idProduct = 0x4035,
	  .bInterfaceClass = USB_CLASS_VIDEO,
	  .bInterfaceSubClass = 1,
	  .bInterfaceProtocol =	UVC_PC_PROTOCOL_15,
	  .driver_info = (kernel_ulong_t) &(const struct uvc_device_info ) {
										.uvc_version = 0x010a, } },
										
	/* Quanta ACER HD User Facing 4033 - Experimental !! */
	{ .match_flags 	= USB_DEVICE_ID_MATCH_DEVICE
			| USB_DEVICE_ID_MATCH_INT_INFO,
	  .idVendor = 0x0408,
	  .idProduct = 0x4033,
	  .bInterfaceClass = USB_CLASS_VIDEO,
	  .bInterfaceSubClass = 1,
	  .bInterfaceProtocol =	UVC_PC_PROTOCOL_15,
	  .driver_info = (kernel_ulong_t) &(const struct uvc_device_info ) {
										.uvc_version = 0x010a, } },
```
Save the modified file and close the file. After that, we will now compile the new module.
```sh
make -j4 -C /lib/modules/$(uname -r)/build M=$(pwd) modules  
sudo cp uvcvideo.ko /lib/modules/$(uname -r)/kernel/drivers/media/usb/uvc/ 
```
Now reboot your system.
