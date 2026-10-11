# TimeKeep

TimeKeep is a small utility to keep track of time & date since the RTC 
driver on Qualcomm chipset is read-only.

It is a single native tool, timekeep, to store and restore time. Whenever the system clock
is set, the time delta between the set date (in seconds) and
/sys/class/rtc/rtc0/since_epoch is stored to a persistent property (persist.vendor.timeadjust)
and to the ATS file (/data/vendor/time/ats_2).

To restore the time timekeep is called with the restore parameter. The persistent property
containing the time delta is then read together with the since_epoch value and settimeofday
is set with the sum of the two. timekeep with the restore parameter needs CAP_SYS_TIME.
The call to restore the time can for example be done in init as the example below.

timekeep with the monitor parameter restores the time, then waits for the system clock to
be set (a timerfd with TFD_TIMER_CANCEL_ON_SET) and stores the time on every change. The
vendor.timekeep service in timekeep.rc runs it, so no other component has to call timekeep.

init.example.rc:

on boot
  /system/bin/timekeep restore

