🛡️ Unattended-Upgrades — Installation & Secure Configuration Guide

This guide explains how to install, configure, harden, and verify unattended-upgrades on Ubuntu servers.

📌 1. Install Required Packages
sudo apt update
sudo apt install unattended-upgrades apt-listchanges


Enable automatic updates:

sudo dpkg-reconfigure --priority=low unattended-upgrades


This creates /etc/apt/apt.conf.d/20auto-upgrades.

📌 2. Configure Automatic Update Schedule

Edit the periodic update file:

sudo nano /etc/apt/apt.conf.d/20auto-upgrades


Recommended configuration:

APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";

📌 3. Main Configuration File (Secure Setup)

Edit the core configuration:

sudo nano /etc/apt/apt.conf.d/50unattended-upgrades


Use the following:

Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
    "${distro_id}:${distro_codename}-updates";
};

Unattended-Upgrade::Package-Blacklist {
};

// Safety
Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::MinimalSteps "true";
Unattended-Upgrade::InstallOnShutdown "false";

// Cleanup
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

// Logging
Unattended-Upgrade::SyslogEnable "true";
Unattended-Upgrade::SyslogFacility "daemon";

📌 4. Email Notifications (Optional but Recommended)

Install a mail agent:

sudo apt install postfix


Add to 50unattended-upgrades:

Unattended-Upgrade::Mail "your-email@example.com";
Unattended-Upgrade::MailOnlyOnError "true";

📌 5. Automatic Reboot Policy

Enable reboots only when required:

Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-WithUsers "false";
Unattended-Upgrade::Automatic-Reboot-Time "03:00";

📌 6. Complete Hardened Configuration (Recommended)

Put this entire block inside:

/etc/apt/apt.conf.d/50unattended-upgrades

Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}-security";
        "${distro_id}:${distro_codename}-updates";
};

Unattended-Upgrade::Package-Blacklist {};

// Cleanup old packages
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

// Safety mechanisms
Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::MinimalSteps "true";
Unattended-Upgrade::InstallOnShutdown "false";

// Notifications
Unattended-Upgrade::Mail "your-email@example.com";
Unattended-Upgrade::MailOnlyOnError "true";
Unattended-Upgrade::SyslogEnable "true";
Unattended-Upgrade::SyslogFacility "daemon";

// Reboot rules
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-WithUsers "false";
Unattended-Upgrade::Automatic-Reboot-Time "03:00";

📌 7. Verify Installation & Operation
Dry run (see what updates would install)
sudo unattended-upgrades --dry-run --debug

Check logs
sudo tail -f /var/log/unattended-upgrades/unattended-upgrades.log

See what packages were upgraded
grep "Packages that were upgraded" /var/log/unattended-upgrades/unattended-upgrades.log

Check timers
systemctl status apt-daily.timer apt-daily-upgrade.timer

📌 8. Trigger Update Manually
sudo unattended-upgrade -v
