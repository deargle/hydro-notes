# Get an always-free Google Cloud Platform GCP instance

Assuming:
* you've already signed up for a gcp account
* you already created a project where you want your gcp instance to live

go to [console.cloud.google.com](https://console.cloud.google.com), select your project, and navigate to the `/compute/instances` route, see screenshot below. (Hamburger, Compute Engine submenu, "VM Instances" sub menu.) You might need to "enable" some gcp APIs -- you'll be prompted if necessary.


![image](https://user-images.githubusercontent.com/1174653/123358015-e8234b00-d527-11eb-8acd-7d3b53ac65ba.png)

create a new instance.

* To be on the free tier, you need to use an "f1-micro" instance, which is only available on the "N1" series.
* And you need to be using HDD, which is a "standard persistent disk", _not_ the default "balanced persistent disk." I'm leaving the default Debian 10 Buster image. I boost disk size to 30 GB -- the more disk size you ask for, the better disk IO speeds GCP gives you. Shown in screenshot.
* Tick the boxes to allow HTTP and HTTPS through the gcp firewall (screenshot 2 below).
* I leave all other defaults.
* click the fatty "create" button

![image](https://user-images.githubusercontent.com/1174653/123358233-5a942b00-d528-11eb-93e9-b82500428a33.png)

![image](https://user-images.githubusercontent.com/1174653/123358387-a941c500-d528-11eb-9873-1a73548a8d54.png)

When your new instance shows it's ready, click the SSH button to get a browser-based ssh connection. GCP will auto-connect you to your instance without you having to authenticate further, since you're logged in to a google account that has admin access to the instance you just created.

## create a swapfile

GCP instances by default have no swap space. Create some (following [this itsfoss guide](https://itsfoss.com/create-swap-file-linux/)):

    sudo fallocate -l 1G /swapfile
    sudo chmod 600 /swapfile
    sudo mkswap /swapfile
    sudo swapon /swapfile
    sudo swapon --show # confirm that it's working

    # add permanently
    echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
