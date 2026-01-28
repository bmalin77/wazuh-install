# Wazuh Server install on Ubuntu V’box. Agent install on home laptop…with some hiccups
Goal is to mimic a small, localized SIEM deployment and kick off the server to capture data coming through my home laptop. From there, monitory, identify and potentially triage or remediate threats or concerns
Step 1 - Download and install the Ubuntu 24.04 LTS on a virtual box machine.

<img width="696" height="402" alt="image" src="https://github.com/user-attachments/assets/1200df98-e587-49a1-9960-baeced8a9371" />

For the Wazuh installation, I followed their Quickstart all-in-one installation commands. You want to be very cognizant of their requirements and operating system guidelines. Not meeting these requirements could have the installation fail or not perform as designed.

<img width="1126" height="237" alt="image" src="https://github.com/user-attachments/assets/82e7b9f1-f0f2-40ca-9792-c26ddaea5d89" />

Download and run the Wazuh installation assistant

curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
For me everything was running good. Manager, indexer and filebeat installed successfully. The dashboard installation failed. From there I went into /var/log/wazuh-install.log to go through what went wrong. A faster way was to grep -i "error" wazuh-install.log and that was a quicker way to see the issues. I noticed a few errors, one that stuck out was disk full error. A "disk full" error in a Wazuh installation signifies that the storage partition allocated for Wazuh components - typically /var/ossec/ for the manager or /var/lib/wazuh-indexer/ for the indexer-has reached 100% capacity, resulting in a halt to data indexing, alerting, and log processing.

<img width="690" height="65" alt="image" src="https://github.com/user-attachments/assets/ec632068-47ff-4bc1-9229-81b38afdf6e4" />

To start fresh, I checked all the items that may have been leftover with the prior install. 
Finding Hidden or Leftover Directories
If you are cleaning up a failed install, use these commands to locate any remaining Wazuh-related data:
Search all log paths: find /var/log -name "*wazuh*"
Check OSSEC core: ls -ld /var/ossec (If this exists, configuration or log data remains).
Check Package Metadata: dpkg -l | grep wazuh (to see if packages are in an rc state, meaning "removed but configuration remains").

It is also a good practice to clean up your system's package list: sudo apt-get update && sudo apt-get autoclean && sudo apt-get autoremove -y. 
There is a chance that the wazuh-install.log wasn't removed. The wazuh-install.sh script automatically redirects its verbose output to /var/log/wazuh-install.log each time it runs. If the file is not deleted, new logs are typically appended to it, making it harder to find where the new attempt begins. You can remove it using -rm wazuh-install.log.Cleanup Tip: If you need to perform a fresh install, it is often necessary to "purge" these packages (e.g., apt purge wazuh-manager) to ensure all configuration and log files in /var/ossec are deleted.
I somewhat low-balled my VM build at first and believed I needed to resolve that and make some changes. After discussing it with my cousin, we agreed to make some changes to base memory and storage.

<img width="1036" height="465" alt="image" src="https://github.com/user-attachments/assets/4b54bea5-a887-43a9-ac94-6b82fa6874bc" />

