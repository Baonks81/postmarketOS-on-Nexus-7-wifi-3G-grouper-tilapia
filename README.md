# postmarketOS-on-Nexus-7-wifi-3G-grouper-tilapia

Không biết tại sao bài bị xoá, đăng lại link pdf cho ai cần



http://www.mediafire.com/file/r37ylhmk7wsb0a4



Trang devs của postmarketOS/pmaports, kernel grouper đã lên 5.9-rc2 mainline



https://gitlab.com/postmarketOS/pmaports/-/blob/master/device/testing/linux-asus-grouper/APKBUILD



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

https://github.com/tytydraco/KTweak



File sysctl.conf đã chỉnh sửa

http://www.mediafire.com/file/j32igia3g02x8k6/sysctl.conf/file



Cpufreq:

http://www.mediafire.com/file/v65csn6d9gptit2/cpufreq.start/file



$ sudo nano /etc/sysctl.conf



# content of this file will override /etc/sysctl.d/*

vm.swappiness=100

vm.vfs_cache_pressure=200

vm.dirty_background_bytes=16777216

vm.dirty_bytes=33554432

vm.dirty_background_ratio=3

vm.dirty_ratio=30

vm.dirty_writeback_centisecs=3000

vm.dirty_expire_centisecs=3000

vm.lowmem_reserve_ratio=256 32 32

vm.min_free_kbytes=32768

vm.user_reserve_kbytes=32768

vm.admin_reserve_kbytes=16384

vm.panic_on_oom=1

kernel.panic=5

vm.overcommit_memory=0

vm.overcommit_ratio=50

vm.drop_caches=3

vm.laptop_mode=5

vm.mmap_min_addr=32768

vm.oom_kill_allocating_task=1

vm.extfrag_threshold=750

vm.oom_dump_tasks=0

vm.page-cluster=0

vm.stat-interval=10

vm.compact_unevictable_allowed=0



# fs.file-max = 76385



kernel.tainted=0

kernel.threads-max=15502

kernel.usermodehelper.bset=4294967295 4294967295

kernel.usermodehelper.inheritable=4294967295 4294967295

kernel.printk=4 4 1 7

kernel.kptr_restrict=1

kernel.randomize_va_space=2

kernel.keys.root_maxkeys=200

kernel.keys.root_maxbytes=20000

kernel.perf_event_paranoid=1

kernel.perf_cpu_time_max_percent=3

kernel.shmmax=33554432

kernel.shmall=2097152

kernel.msgmni=721

kernel.sem=250 32000 32 128

kernel.auto_msgmni=1

kernel.sched_domain.cpu0.domain0.min_interval=1

kernel.sched_domain.cpu0.domain0.max_interval=4

kernel.sched_domain.cpu0.domain0.busy_factor=64

kernel.sched_domain.cpu1.domain0.min_interval=1

kernel.sched_domain.cpu1.domain0.max_interval=4

kernel.sched_domain.cpu1.domain0.busy_factor=64

kernel.sched_domain.cpu2.domain0.min_interval=1

kernel.sched_domain.cpu2.domain0.max_interval=4

kernel.sched_domain.cpu2.domain0.busy_factor=64

kernel.sched_domain.cpu3.domain0.min_interval=1

kernel.sched_domain.cpu3.domain0.max_interval=4

kernel.sched_domain.cpu3.domain0.busy_factor=64

kernel.sched_child_runs_first=1

kernel.sched_tunable_scaling=0

kernel.sched_latency_ns=500000

kernel.sched_min_granularity_ns=100000

kernel.sched_migration_cost_ns=5000000

kernel.sched_nr_migrate=2

kernel.sched_wakeup_granularity_ns=250000



net.ipv4.tcp_ecn=1

net.ipv4.tcp_fastopen=3

net.ipv4.tcp_timestamps=0



$ sudo sysctl -p



$ sudo nano /etc/local.d/cpufreq.start



# Set the governor to ondemand for all processors

for cpu in /sys/devices/system/cpu/cpufreq/policy*; do

echo ondemand > ${cpu}/scaling_governor

done



# Reduce the boost ignore_nice_load to 0

echo 0 > /sys/devices/system/cpu/cpufreq/ondemand/ignore_nice_load



# Reduce the boost io_is_busy to 1

echo 1 > /sys/devices/system/cpu/cpufreq/ondemand/io_is_busy



# Reduce the boost powersave_bias to 0 <-- tăng giảm xung của cpu/gpu

echo 0 > /sys/devices/system/cpu/cpufreq/ondemand/powersave_bias



# Reduce the boost sampling_down_factor to 2

echo 2 > /sys/devices/system/cpu/cpufreq/ondemand/sampling_down_factor



# Reduce the boost sampling_rate to 30000

echo 30000 > /sys/devices/system/cpu/cpufreq/ondemand/sampling_rate



# Reduce the boost threshold to 95%

echo 95 > /sys/devices/system/cpu/cpufreq/ondemand/up_threshold



$ sudo chmod +x /etc/local.d/cpufreq.start

$ sudo rc-update add local default

$ sudo lbu commit



Tooltips:



Epiphany browser và midori khi xem youtube phải uninstall xfce4-pulseaudio-plugin, pulse, pulseaudio, pulseaudio-alsa, và pavucontrol



User-agent:

$ gsettings set org.gnome.Epiphany.web:/org.gnome.epiphany/web/ user-agent 'Mozilla/5.0 (Android 9.0; Mobile; rv:70.0) Gecko/20100101 Firefox/70.0'

Ẩn con trỏ chuột trong xfce4
$sudo nano /etc/lightdm/lightdm.conf

Bỏ dấu '#' trong [:Seat]

xserver-command=X -nocursor


Cài dual boot Android và Ubuntu dùng MultiROM

https://tinhte.vn/thread/multirom-cai-ubuntu-14-04-6-lts-len-nexus-7-2012-grouper-dualboot-android.3134764/



[MEDIA=youtube]p-rdr1oKnWo[/MEDIA]
