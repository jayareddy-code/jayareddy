mkdir -p /usr/admin/log\_rotation\_logs/$(uname -n)/

cp -pr /etc/rsyslog.conf /etc/rsyslog.conf.$(date +%F)

cp -pr /etc/profile /etc/profile.$(date +%F)

cp -pr /etc/audit/auditd.conf /etc/audit/auditd.conf.$(date +%F)

cp -pr /etc/logrotate.d/cmdline /usr/admin/log\_rotation\_logs/$(uname -n)/

cp -pr /etc/logrotate.d/btmp /usr/admin/log\_rotation\_logs/$(uname -n)/

cp -pr /etc/logrotate.d/wtmp /usr/admin/log\_rotation\_logs/$(uname -n)/

cp -pr /etc/logrotate.d/syslog /usr/admin/log\_rotation\_logs/$(uname -n)/



cp /usr/admin/log\_rotation\_logs/original\_files/rsyslog.conf /etc/rsyslog.conf

cp /usr/admin/log\_rotation\_logs/original\_files/btmp /etc/logrotate.d/btmp

cp /usr/admin/log\_rotation\_logs/original\_files/wtmp /etc/logrotate.d/wtmp

cp /usr/admin/log\_rotation\_logs/original\_files/cmdline /etc/logrotate.d/cmdline

cp /usr/admin/log\_rotation\_logs/original\_files/sudo /etc/logrotate.d/sudo

cp /usr/admin/log\_rotation\_logs/original\_files/syslog /etc/logrotate.d/syslog



ls -l /var/log/sudo.log

cat /etc/sudoers | egrep "/var/log/sudo.log"



vi /etc/audit/auditd.conf

100

6



max\_log\_file = 100

num\_logs = 6



service auditd restart

auditctl -s



vi /etc/profile



\# ===============================

\# Global History Enhancement

\# ===============================

HISTTIMEFORMAT="%F %T : "

HISTFILESIZE=10000

HISTSIZE=10000

PROMPT\_COMMAND="history -a; history -n; $PROMPT\_COMMAND"



\# ===============================

\# Command Logging to Syslog

\# ===============================



\# Global variable to track last logged command

function log2syslog\_custom {

&#x20;   local COMMAND TERMINAL



&#x20;   case "$BASH\_COMMAND" in

&#x20;       history\*|logger\*|"trap"\*|"") return ;;

&#x20;   esac



&#x20;   COMMAND=$(fc -ln -0 2>/dev/null)

&#x20;   TERMINAL=$(tty 2>/dev/null)

&#x20;   \[\[ $TERMINAL == "not a tty" || -z $TERMINAL ]] \&\& TERMINAL=${SSH\_TTY:-unknown}

&#x20;   TERMINAL=$(echo "$TERMINAL" | awk -F/ '{print $(NF-1) "/" $NF}')



&#x20;   \[ -n "$COMMAND" ] \&\& logger -p local1.notice -t bash -- "${TERMINAL}:${USER}:${COMMAND}"

}



trap 'log2syslog\_custom' DEBUG









grep -R "log2syslog" /etc/\* /root 2>/dev/null



trap -p DEBUG



logrotate -d /etc/logrotate.d/btmp

logrotate -d /etc/logrotate.d/wtmp

logrotate -d /etc/logrotate.d/cmdline

logrotate -d /etc/logrotate.d/sudo

logrotate -d /etc/logrotate.d/syslog



logrotate -v /etc/logrotate.d/btmp

logrotate -v /etc/logrotate.d/wtmp

logrotate -v /etc/logrotate.d/cmdline

logrotate -v /etc/logrotate.d/sudo

logrotate -v /etc/logrotate.d/syslog



systemctl restart rsyslog.service

systemctl status rsyslog.service



ls -lrt /var/log/wtmp\*

ls -lrt /var/log/btmp\*

ls -lrt /var/log/cmdline\*

ls -lrt /var/log/messages\*

ls -lrt /var/log/spooler\*

ls -lrt /var/log/secure\*

ls -lrt /var/log/maillog\*

ls -lrt /var/log/cron\*



cd /usr/admin/jay/scripts

./logrotation\_after.sh

