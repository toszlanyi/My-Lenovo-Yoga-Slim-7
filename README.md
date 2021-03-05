### My Lenovo Yoga Slim 7 as media Notebook

**!! this is a guidance - use at your own risk !!**

#### Lenovo Yoga Slim 7 14ARE05 Notebook (35,56 cm/14 Zoll, AMD Ryzen 7, Radeon™, 1000 GB SSD)
- AMD Ryzen 7 4800U 1,8 GHz (bis zu 4,2 GHz)
- 35,56 cm (14") Full HD IPS-Display (1920 x 1080)
- 16 GB LPDDR4x-RAM
- Harddrive: 1000 GB SSD

#### 1 Erase all Windows partitions and install KDE neon 5.21 (based upon Ubuntu 20.04 LTS)
  
  - make sure that Firmware / BIOS settings show
    ```
    SECURE BOOT = disabled
    ```
  - create partitions for /boot/efi, swap and /
  - install KDE neon
  - First start with KDE neon is a mess using default Ubuntu Kernel 5.4.xx (`dmesg` shows errors for amdgpu, iommu, audio, network etc)
    
#### 2 Fixing some issues

  2a) upgrade Kernel
  - best to use Lowlatency 5.8.44-xx Ubuntu patched Kernel
  - most parts working now
  - later Mainline Kernels brake sound and vga
    
  2b) Sound is choppy and doesn't work always. To fix this blacklist the realtec sound module 
  ```
  sudo tee /etc/modprobe.d/blacklist-realtek.conf <<<'blacklist snd_hda_codec_realtek'
  ```
        
  2c) get microphone working
  - add Kernel parameter to `/etc/default/grub`
    ```
    GRUB_CMDLINE_LINUX="snd_rn_pci_acp3x.dmic_acpi_check=1"
    ```
  - and
    ```
    sudo update-grub
    ```

  2d) To get AMDGPU properly working download linux-firmware from
  
  https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/
    
  and
    
  `sudo cp linux-firmware/amdgpu/renoir* /lib/firmware/amdgpu`
    
  2e) Touchpad does not wake up from Suspend - to enable it after resume
  
  `xinput --enable 'PNP0C50:00 06CB:CDB0 Touchpad'`
  
  **!! Todo:** Find a way to automize the process

#### 3 Optimizations

  3a) Battery life should be safed when always at AC Power by limiting the max power charge to 60%
  
  `sudo echo 1 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode`
    
  disable (get battery charge back to 100%)
    
  `sudo echo 0 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode`
    
  3b) push /tmp and /var/log into RAM by adding those lines into `/etc/fstab`
  
  ```
  tmpfs     /tmp        tmpfs       nosuid      0 0
  tmpfs     /var/log    tmpfs       nosuid      0 0
  ```
