# Cài PostmarketOS lên Nexus 7 2012 wifi 3G dung lượng 8GB 16GB 32GB kernel 5.14-rc3 support USB-OTG

Không biết tại sao bài bị xoá, đăng lại link pdf cho ai cần



http://www.mediafire.com/file/r37ylhmk7wsb0a4



Trang devs của postmarketOS/pmaports, kernel grouper đã lên 5.14-rc3 mainline



https://gitlab.com/postmarketOS/pmaports/-/blob/master/device/testing/linux-asus-grouper/APKBUILD



Cách kiểm tra Nexus 7 2012 là mã cũ PM269 hay E1565



Variants

grouper rev. PM269 - without GSM (oldest)
grouper rev. E1565 - without GSM (modern revision)
tilapia rev. E1565 - with GSM



Do I have grouper or tilapia?





TWRP (adb shell) $ grep androidboot.baseband=unknown /proc/cmdline && echo grouper || echo tilapia



Which hardware revision of grouper do I have?





TWRP (adb shell) $ find /sys/devices/ | grep -c max776 && echo You have E1565



TWRP (adb shell) $ find /sys/devices/ | grep -c tps6591 && echo You have PM269



Để PC nhận Nexus 7 2012 và flash kernel và push rootfs nhanh thì nên rút hết các thiết bị usb khác ngoài Nexus 7



Install log file

http://www.mediafire.com/file/znq1ijgnf8ygiey



Install log file for Phosh trên Nexus 2012 8GB

http://www.mediafire.com/file/rl8sfc8jr83vnvr/file



*** CÀI ĐẶT VÀO /userdata ***

Phải sửa file deviceinfo trong



$ nano /home/<username>/.local/var/pmbootstrap/cache_git/pmaports/device/testing/deviceinfo



Sửa dòng: deviceinfo_flash_fastboot_max_size="650" thành "13773" (Nexus 7 8gb là 5829,

nexus 7 32gb là 27944)



Config sysctl.conf có sự chỉnh sửa theo KTweak trên github của tytydraco (xda-developers)



Tham khảo:

https://github.com/tytydraco/KTweak/tree/latency



File sysctl.conf đã chỉnh sửa

http://www.mediafire.com/file/j32igia3g02x8k6/sysctl.conf/file



Cpufreq:

http://www.mediafire.com/file/v65csn6d9gptit2/cpufreq.start/file



$ sudo nano /etc/sysctl.conf



# content of this file will override /etc/sysctl.d/*



vm.swappiness=15

vm.vfs_cache_pressure=80

vm.dirty_background_ratio=3

vm.dirty_ratio=30

vm.dirty_writeback_centisecs=3000

vm.dirty_expire_centisecs=3000

vm.lowmem_reserve_ratio=32     32     0

vm.min_free_kbytes=3072

vm.user_reserve_kbytes=6144

vm.admin_reserve_kbytes=3072

vm.panic_on_oom=0

vm.overcommit_memory=1

vm.overcommit_ratio=50

vm.drop_caches=3

vm.laptop_mode=0

vm.mmap_min_addr=4096

vm.oom_kill_allocating_task=0

vm.extfrag_threshold=500

vm.oom_dump_tasks=1

vm.page-cluster=0

vm.stat_interval=10

vm.compact_unevictable_allowed=0

vm.highmem_is_dirtyable=0



kernel.panic=10

kernel.panic_on_oops=1

kernel.tainted = 0

kernel.ctrl-alt-del = 1

kernel.threads-max = 15503

kernel.random.write_wakeup_threshold = 128

kernel.usermodehelper.bset=4294967295 4294967295

kernel.usermodehelper.inheritable=4294967295 4294967295

kernel.printk = 15 4 1 7

kernel.kptr_restrict = 2

kernel.randomize_va_space = 2

kernel.perf_event_paranoid = 3

kernel.keys.root_maxkeys=200

kernel.keys.root_maxbytes=20000

kernel.perf_event_paranoid=1

kernel.perf_cpu_time_max_percent=3

kernel.shmmax=268435456

kernel.shmall=2097152

kernel.msgmni=2048

kernel.msgmax=64000

kernel.sem=500 512000 64 2048

kernel.auto_msgmni=1

kernel.sched_child_runs_first=1

kernel.hung_task_timeout_secs=0



fs.file-max = 99200

fs.epoll.max_user_watches = 138922



net.core.somaxconn = 128

net.core.wmem_max = 131072

net.core.rmem_max = 131072

net.core.wmem_default = 112640

net.core.rmem_default = 112640

net.core.warnings = 1



net.ipv4.tcp_ecn=1

net.ipv4.tcp_fastopen=3

net.ipv4.tcp_timestamps=0

net.ipv4.tcp_tw_reuse=1

net.ipv4.tcp_wmem=1536 21845 131072

net.ipv4.tcp_rmem=1536 21845 131072



$ sudo sysctl -p



$ sudo nano /etc/local.d/cpufreq.start



# Set the governor to ondemand for all processors

for cpu in /sys/devices/system/cpu/cpufreq/policy*; do

echo ondemand > ${cpu}/scaling_governor

done



# Reduce the boost ignore_nice_load to 0

echo 0 > /sys/devices/system/cpu/cpufreq/ondemand/ignore_nice_load



# Reduce the boost io_is_busy to 0

echo 0 > /sys/devices/system/cpu/cpufreq/ondemand/io_is_busy



# Reduce the boost powersave_bias to 0 <-- tăng giảm xung của cpu/gpu

echo 0 > /sys/devices/system/cpu/cpufreq/ondemand/powersave_bias



# Reduce the boost sampling_down_factor to 2

echo 2 > /sys/devices/system/cpu/cpufreq/ondemand/sampling_down_factor



# Reduce the boost sampling_rate to 30000

echo 30000 > /sys/devices/system/cpu/cpufreq/ondemand/sampling_rate



# Reduce the boost threshold to 75%

echo 75 > /sys/devices/system/cpu/cpufreq/ondemand/up_threshold



for queue in /sys/block/*/queue

do

# Choose the first governor available

avail_scheds="$(cat "$queue/scheduler")"

for sched in cfq noop kyber bfq mq-deadline none

do

 if [[ "$avail_scheds" == *"$sched"* ]]

 then

  echo "$sched" > "$queue/scheduler"

  break

 fi

done



# Do not use I/O as a source of randomness

# echo 0 > "$queue/add_random"



# Disable I/O statistics accounting

echo 0 > "$queue/iostats"



# Reduce heuristic read-ahead in exchange for I/O latency

echo 256 > "$queue/read_ahead_kb"



# Reduce the maximum number of I/O requests in exchange for latency

echo 512 > "$queue/nr_requests"



echo 128 > "$queue/max_sectors_kb"



echo 2 > "$queue/rq_affinity"

done



$ sudo chmod +x /etc/local.d/cpufreq.start

$ sudo rc-update add local default

$ sudo lbu commit



***Tooltips:



Install 2D GPU accelerate driver:



$ sudo apk add xf86-video-opentegra



Change opentegra.conf to 00-opentegra.conf in /usr/share/X11/xorg.conf.d

Let's opentegra driver loaded before fbdev driver



Epiphany browser và midori khi xem youtube phải uninstall xfce4-pulseaudio-plugin, pulse, pulseaudio, pulseaudio-alsa, và pavucontrol



User-agent:

$ gsettings set org.gnome.Epiphany.web:/org.gnome.epiphany/web/ user-agent 'Mozilla/5.0 (Android 11; Mobile; rv:91.0) Gecko/91.0 Firefox/91.0'



Ẩn con trỏ chuột trong xfce4

$ sudo nano /etc/lightdm/lightdm.conf



Bỏ dấu '#' trong [:Seat]



xserver-command=X -nocursor



Cài ibus và bộ gõ Vietnamese



$ sudo apk add ibus



Chọn thêm input language là Vietnamese. Đồng thời tạo settings → session and startup



Name: IBus

Description: input keyboard methods

Command: /usr/bin/ibus-daemon -d

start: on logon



Fix error: (1) not authorized to control networking



https://wiki.gentoo.org/wiki/NetworkManager#Fixing_nm-applet_insufficient_privileges



Fixing nm-applet insufficient privileges

If nm-applet fails to create new networks with the error "Insufficient Privileges," then it could be a policy kit issue. Create the following file:





FILE: /etc/polkit-1/rules.d/50-org.freedesktop.NetworkManager.rules



polkit.addRule(function(action, subject) {

if (action.id.indexOf("org.freedesktop.NetworkManager.") == 0 && subject.isInGroup("plugdev")) {        return polkit.Result.YES;

 }

});



This lets all users in the plugdev group control network manager.



Control CPU frequency match with thermal, using shell scripts



https://github.com/Sepero/temp-throttle/tree/4e6fa06ea036129c4a815fc5d4494556578624e



step freq throttle 100000



Low_temp=max_temp - 2



sleep 3



$ sh temp_throttle.sh 53



https://www.mediafire.com/file/7vh0qxu1r28aeia/



Create startup temperature for cpu throttle at login

$ visudo



ALL ALL=(root) NOPASSWD: /path/to/temp_throttle.sh



$ sudo chown root /path/to/temp_throttle.sh



$ sudo chmod 755 /path/to/themp_throttle.sh



Settings → Sessions and applications startup



Command: sudo /path/to/temp_throttle.sh 53



Activate sensor auto rotate screen:



https://gitlab.com/gullradriel/asus-grouper-nexus-7-sensor-daemon



$ sudo apk iio-sensor-proxy perl xinput xrandr xset



$ sudo rc-service -v iio-sensor-proxy start



$ sudo rc-update add iio-sensor-proxy default



$ sudo monitor-sensor



Check all program allow running permission and run



$ sudo /usr/init.d/asus_grouper_sensors start



$ sudo rc-update add asus_grouper_sensors default



*Connecting keyboard and mouse bluetooth, NFC checking:



$ sudo apk bluez bluez-utils bluez-mgmt bluez-btmon bluez-hid2hci bluez-alsa neard



$ sudo rc-service -v bluetooth start



$ sudo bluetoothctl power on



$ sudo bluetoothctl



#<bluetoothctl>: scan on



#<bluetoothctl>: help



NFC:



$ sudo rc-service -v neard start



$ sudo nfctool -d nfc0 -1 -p



***Config firefox 90, install Ublock origin and h264ify addons



https://gitlab.com/postmarketOS/mobile-config-firefox/blob/master/src/mobile-config-prefs.js



Cài dual boot Android và Ubuntu dùng MultiROM

https://tinhte.vn/thread/multirom-cai-ubuntu-14-04-6-lts-len-nexus-7-2012-grouper-dualboot-android.3134764/



[MEDIA=youtube]EM28cRMyvoc[/MEDIA]



[MEDIA=youtube]ZcgeXCY3_6E[/MEDIA]
