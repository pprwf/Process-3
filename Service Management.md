# Services Management

## Services

เป็น process ที่ทำงานอยู่บนเซอร์เวอร์อยู่ตลอดเวลา
รอการใช้งานหรือการดำเนินงานตาม tasks, มักจะถูกใช้ในการจัดการ requests ไม่ว่าจะเป็นจาก process อื่น ๆ หรือเครื่อง clients อื่น ๆ . [[1]](https://rimuhosting.com/knowledgebase/linux/managing-services#:~:text=Services%20are%20programs%20or%20processes,services%20are%20Apache%20and%20Postfix)

### systemd

systemd เป็นตัวจัดการ service สำหรับระบบปฏิบัติการ Linux. ถูกออกแบบมาให้มีความเข้ากันได้ย้อนหลัง(Backward Compatible)กับ System V  
มีฟีเจอร์เช่น Socket & D-Bus [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

### systemd Units

ใน systemd, target ของคำสั่งต่าง ๆ จะถูกเรียกว่า units ซึ่งเป็น resources ที่ systemd จัดการได้. Units จะถูกแบ่งกลุ่มตามประเภทของ resources ที่ถูกกำหนดไว้ใน unit configuration files.
[[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

| Unit Type      | File extension |
| -------------- | -------------- |
| Service unit   | .service       |
| Target unit    | .target        |
| Automount unit | .automount     |
| Device unit    | .device        |
| Mount unit     | .mount         |
| Path unit      | .path          |
| Scope unit     | .scope         |
| Slice unit     | .slice         |
| Socket unit    | .swap          |
| Timer unit     | .timer         |

<!-- ### Feature
- ความเร็ว

    การทำงานแบบ Socket and D-Bus based ช่วยลดเวลาทในการ boot OS ด้วยการ
    - ทำงานแค่ process ที่จำเป็น
    - ทำงานหลาย process พร้อม ๆ กันให้มากที่สุด
     -->

### การจัดการ System Service

systemd มีคำสั่ง systemctl ที่ใช้ในการ start, stop, restart, view, enable, และ disable system services. [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

| systemd command                                 | Description                                                                |
| ----------------------------------------------- | -------------------------------------------------------------------------- |
| systemctl start network.service                 | เริ่มการทำงานของ service.                                                  |
| systemctl stop network.service                  | หยุดการทำงานของ service.                                                   |
| systemctl restart network.service               | รีสตาร์ทการทำงานของ service.                                               |
| systemctl reload network.service                | Reloads ไฟล์ configuration โดยไม่ขัดการทำงานของ operation                  |
| systemctl condrestart network.service           | รีสตาร์ท service ถ้าหาก service กำลังทำงานอยู่.                            |
| systemctl status network.service                | ดูสถานะการทำงานของ service.                                                |
| systemctl enable network.service                | Enable\* service เมื่อเงื่อนไขที่ถูกตั้งเอาไว้เป็นจริง                     |
| systemctl disable network.service               | Disable service เมื่อเงื่อนไขที่ถูกตั้งเอาไว้เป็นจริง                      |
| systemctl is-enabled network.service            | ดูว่า service นั้นถูกเปิดการใช้งานอยู่หรือไม่.                             |
| systemctl list-unit-files \-\-type=service      | แสดง Lists ของ services ในแต่ละ runlevel แล้วดูว่าเปิดการใช้งานอยู่หรีอไม่ |
| ls /etc/systemd/system/\*.wants/network.service | แสดง Lists ของ runlevels ที่เปิดการใช้งานและ runlevels ที่ปิดการใช้งานอยู่ |
| systemctl daemon-reload                         | ใช้เมื่อต้องการสร้าง service file หรือเปลี่ยนการตั้งค่า                    |

**\*** Enable ทำให้ service ทำงานอัตโนมัติเมื่อ boot ขึ้นมา ในขณะที่ start จะเป็นการเริ่มการทำงานของ service ในทันที [[4]](https://askubuntu.com/questions/733469/what-is-the-difference-between-systemctl-start-and-systemctl-enable)

### Listing Service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

ใช้คำสั่งต่อไปนี้ในการแสดง list ของ service ที่ถูก load อยู่

    systemctl list-units --type service

ถ้าหากต้องการแสดง list ของ service ทั้งหมดไม่ว่าจะถูก load หรือไม่

    systemctl list-units --type service --all

ผลลัพธ์ที่ได้ :

    $ systemctl list-units --type service
    UNIT                        LOAD   ACTIVE     SUB           DESCRIPTION
    atd.service                 loaded active     running       Deferred execution scheduler
    auditd.service              loaded active     running       Security Auditing Service
    avahi-daemon.service        loaded active     running       Avahi mDNS/DNS-SD Stack
    chronyd.service             loaded active     running       NTP client/server
    crond.service               loaded active     running       Command Scheduler
    dbus.service                loaded active     running       D-Bus System Message Bus
    dracut-shut down.service     loaded active     exited        Restore /run/initramfs on shut down
    firewalld.service           loaded active     running       firewalld - dynamic firewall daemon
    getty@tty1.service          loaded active     running       Getty on tty1
    gssproxy.service            loaded active     running       GSSAPI Proxy Daemon
    ......

### แสดง status ของ Service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

    systemctl status name.service

เป็นคำสั่งในการแสดง status

**ตัวอย่างการใช้คำสั่ง systemctl status gdm.service**

    $ systemctl status gdm.service
    gdm.service - GNOME Display Manager
        Loaded: loaded (/usr/lib/systemd/system/gdm.service; enabled)
        Active: active (running) since Thu 2013-10-17 17:31:23 CEST; 5min ago
    Main PID: 1029 (gdm)
        CGroup: /system.slice/gdm.service
            ├─1029 /usr/sbin/gdm
            ├─1037 /usr/libexec/gdm-simple-slave --display-id /org/gno...
            └─1047 /usr/bin/Xorg :0 -background none -verbose -auth /r...Oct 17 17:31:23 localhost systemd[1]: Started GNOME Display Manager.

| Parameter | Description                                                                    |
| --------- | ------------------------------------------------------------------------------ |
| Loaded    | บอกว่า service ถูกโหลดหรือยัง ตามด้วย Path ไปหา service ตามด้วยสภานะการ enable |
| Active    | บอกว่า service กำลังรันอยู่หรือไม่ และตามด้วย Timestamp                        |
| Main PID  | Process Identification Number                                                  |
| Cgroup    | Control group\* ของ service                                                    |

**\*** Control group เป็น feature ของ kernel ที่ทำให้ process ถูกจัดเรียงแบบ hierarchical ซึ่งทำให้สามารถจัดการ resource ได้ดีกว่า [[5]](https://man7.org/linux/man-pages/man7/cgroups.7.html)

### เช็คว่า service กำลังรันอยู่หรือไม่ [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

    systemctl is-active name.service

ผลลัพธ์ที่ได้จะออกมาดังนี้

| Parameter       | Description                                                    |
| --------------- | -------------------------------------------------------------- |
| active(running) | service ที่กำลังรันอยู่                                        |
| active(exited)  | service ทำงานจบแล้ว                                            |
| active(waiting) | service กำลังรอที่จะทำงานขั้นต่อไปอยู่ อาจจะเกิดจากการรอข้อมูล |
| inactive        | service ไม่ได้กำลังรันอยู่                                     |

### การ Start service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

ในการ start service ใช้คำสั่ง **systemctl start name.service** เช่น

    # systemctl start httpd.service
    //เริ่มการทำงานของ httpd service

### หยุดการทำงานของ service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

สามารถหยุดการทำงานของ service ได้ด้วยคำสั่ง **systemctl stop name.service** เช่น

    # systemctl stop bluetooth.service
    //หยุดการทำงานของ bluetooth service

### Restart service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

    # systemctl restart bluetooth.service
    //รีสตาร์ทการทำงานของ bluetooth service

คำสั่งนี้จะหยุดการทำงานของ service แล้ว start ใหม่ทันที, ถ้าหาก service ไม่ได้กำลังรันอยู่แล้วก็จะรันขึ้นมาให้

### การทำให้ Service Enable [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

สามารถทำได้ด้วยการใช้คำสั่ง **systemctl enable name.service** เช่น

    # systemctl enable httpd.service
    ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'

### การ Disable service [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

ใช้คำสั่ง **systemctl disable name.service**

    # systemctl disable bluetooth.service
    Removed /etc/systemd/system/bluetooth.target.wants/bluetooth.service.
    Removed /etc/systemd/system/dbus-org.bluez.service.

## การเปลี่ยน Runlevels [[2]](https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd)

ใน systemd, มีการใช้งาน systemd target มาแทนที่การใช้งาน runlevels เพื่อความยืดหยุ่นในการใช้งาน เช่น สืบทอด target แล้วแปลงให้เป็น target ของเราเองโดยการเพิ่ม service อื่น ๆ เข้าไป

**ตารางแสดงการเปรียบเทียบระหว่าง runlevels กับ target**

| Runlevels    | systemd Target                                        | Description                                                                                   |
| ------------ | ----------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| 0            | runlevel0.target, poweroff.target                     | OS ปิดอยู่                                                                                    |
| 1, s, single | runlevel1.target, rescue.target                       | OS อยู่ในโหมด single user                                                                     |
| 2, 4         | runlevel2.target, runlevel4.target, multi-user.target | OS อยู่ใน runlevels แบบ user-define หรือ domain-specific (โดยปกติแล้วจะเทียบเท่า runlevels 3) |
| 3            | runlevel3.target, multi-user.target                   | OS อยู่ในโหมด multi-user แบบ non-graphical\* และระบบสามารถ access ได้ผ่านเครือข่าย            |
| 5            | runlevel5.target, graphical.target                    | OS อยู่ในโหมด multi-user แบบเป็น graphical, service จาก level 3 สามารถใช้งานได้ผ่านการ login  |
| 6            | runlevel6.target, reboot.targe                        | OS ทำการ reboot                                                                               |

**\*** non-graphical คือ ไม่มี GUI, user ต้องใช้งานผ่าน interface ที่เป็น text หรือ command-line

### ดู Default Startup Target ของระบบ

ใช้คำสั่งต่อไปนี้ในการดู default startup target ของระบบ

    systemctl get-default

### ดู Startup Target ทั้งหมด

ใช้คำสั่งต่อไปนี้ในการดู startup target ทั้งหมดของระบบ

    systemctl list-units --type=target

### เปลี่ยน Default Target

เพื่อเปลี่ยน default target, รันคำสั่งนี้ด้วย root user

    systemctl set-default name.target

### เปลี่ยน target ปัจจุบัน

    systemctl isolate name.target

### เปลี่ยนไปที่ Rescue Mode

รันคำสั่งนี้ด้วย root user

    systemctl rescue

**\*** คำสั่งนี้ทำงานคล้ายกับ systemctl isolate rescue.target, หลังการ execute จะแสดงข้อความต่อไปนี้ใน serial port

    You are in rescue mode. After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" or "exit"to boot into default mode.

    Give root password for maintenance
    (or press Control-D to continue):

### Changing to Emergency Mode

รันคำสั่งนี้ด้วย root user

    systemctl emergency

**\*** คำสั่งนี้ทำงานคล้ายกับ systemctl isolate emergency.target, หลังการ execute จะแสดงข้อความต่อไปนี้ใน serial port

    You are in emergency mode. After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" or "exit"to boot into default mode.

    Give root password for maintenance
    (or press Control-D to continue):

## การ Shut down, Suspend, Hibernate ระบบ OS

### systemctl Command

systemd ใช้คำสั่ง systemctl ในการ shut down, suspend และ hibernate ระบบ OS, แต่ก็ยังสามารถใช้คำสั่งพวก Linu system management ได้อยู่

| Linux Management Command | systemctl Command  | Description                                                                                                                                     |
| ------------------------ | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| halt                     | systemctl halt     | หยุด process ทั้งหมดและ shut down ระบบ OS [[6]](https://unix.stackexchange.com/questions/205464/whats-the-difference-between-poweroff-and-halt) |
| poweroff                 | systemctl poweroff | ปิด OS ด้วยสัญญาณ ACPI [[6]](https://unix.stackexchange.com/questions/205464/whats-the-difference-between-poweroff-and-halt)                    |
| reboot                   | systemctl reboot   | Reboot ระบบ OS                                                                                                                                  |

### การ shut down ระบบ OS

ในการ shut down ระบบ OS และปิด (poweroff) ระบบ OS, รันคำสั่งต่อไปนี้ด้วย root user

    systemctl poweroff

ในการ shutdown ระบบ OS โดยไม่ปิด ให้รันคำสั่งต่อไปนี้ด้วย root user

    systemctl halt

โดยทั่วไปแล้ว การรันคำสั่ง 2 คำสั่งนี้จะทำให้ systemd ส่ง message ไปหา login user ทั้งหมด. ถ้าไม่ต้องการให้ systemd ส่ง message ให้รันคำสั่งโดยมี --no-wall ด้วย

    systemctl --no-wall poweroff

### การ Restart OS

ในการ restart ระบบ OS ให้รันคำสั่งนี้ด้วย root user

    systemctl reboot

โดยทั่วไปแล้ว การรันคำสั่งนี้จะทำให้ systemd ส่ง message ไปหา login user ทั้งหมด. ถ้าไม่ต้องการให้ systemd ส่ง message ให้รันคำสั่งโดยมี --no-wall ด้วย

    systemctl --no-wall reboot

### การ Suspend ระบบ OS

รันคำสั่งต่อไปนี้ด้วย root user

    systemctl suspend

### การ Hibernate ระบบ OS

รันคำสั่งต่อไปนี้ด้วย root user

    systemctl hibernate

หากต้องการจะ hibernate OS และ Suspend OS ด้วย ให้ใช้คำสั่งนี้ด้วย root user

    systemctl hybrid-sleep

## References

1. "Managing services in Linux," RimuHosting Ltd, [Online]. Available: https://rimuhosting.com/knowledgebase/linux/managing-services#:~:text=Services%20are%20programs%20or%20processes,services%20are%20Apache%20and%20Postfix. [Accessed 4 February 2024].

2. "Service Management," OpenEuler, [Online]. Available: https://docs.openeuler.org/en/docs/22.03_LTS_SP1/docs/Administration/service-management.html#introduction-to-systemd. [Accessed 4 February 2024].

3. V. Gite, "How to view status of a service on Linux using systemctl," cyberciti, 18 January 2023. [Online]. Available: https://www.cyberciti.biz/faq/systemd-systemctl-view-status-of-a-service-on-linux/. [Accessed 4 February 2024].

4. "askUbuntu," StackExchange, [Online]. Available: https://askubuntu.com/questions/733469/what-is-the-difference-between-systemctl-start-and-systemctl-enable. [Accessed 5 February 2024].

5. M. Kerrisk, "cgroups(7) — Linux manual page," man7, 22 December 2023. [Online]. Available: https://man7.org/linux/man-pages/man7/cgroups.7.html. [Accessed 5 February 2024].

6. StackExchange, [Online]. Available: https://unix.stackexchange.com/questions/205464/whats-the-difference-between-poweroff-and-halt. [Accessed 6 February 2024].
