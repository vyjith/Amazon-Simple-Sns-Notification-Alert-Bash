# Amazon Simple Sns Notification Alert Bash
-------------------------------------------------- 

[![Builds](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Description


I think this script is very useful for those who are looking for getting an alert if an service is down. In this script, I have only explianed, how can we get an alert if the Apache or MariaDB service is down. Another advantage of the script is that you can simply notifed through the Amazon Simpele SNS notification Alert, if the Apache or Mariadb and so on. I'll share with you a demo of my script below. Please contact me if you encounter any problems running this script also please let me know if you have any suggestion so that I can improve my script. 


#### **Please note that your server needs the IAM role with Amazon SNS full access permission to execute this bash**

![](https://i.ibb.co/gyWNY5C/sns-alert.png)


## Pre-Requestes (Package-installation)
-------------------------------------------------- 

```sh

sudo yum -y install git 

```

> Please note that this script is not supporting with macOS and windows


## How to use this script

-------------------------------------------------- 

```sh

git clone https://github.com/vyjith/Amazon-Simple-Sns-Notification-Alert-Bash
cd Amazon-Simple-Sns-Notification-Alert-Bash/
chmod +x sns.sh
bash sns.sh

```

## source code

```
#! /bin/bash

echo -n "Please let me know if you want to create an Amazon Simple SNS Notification topic, please enter yes | no : "
read yn
if [[ "$yn" =~ ^([yY][eE][sS]|[yY])$ ]]
then
        echo -n "Please enter your Amazon Simple SNS Notification topic here : "
        read snstopic
        echo -n "Please let me know your region here:"
        read snsregion
        aws sns create-topic --name ${snstopic} --region ${snsregion}
        echo ""
        echo "You Amazon Simple SNS Notification Topic has been created above. Thank you!. Please rerun the script again for setuping the down alert notification with the above-created Amazon Simple SNS Notification arn topic"
else
        echo -n "Please let me know your Amazon Simple SNS Notification arn for subscribe the topic:"
        read snsarn
        echo -n "Please let me know your email id for subscribing the topic :"
        read snsemail
        echo -n "Please let me know your region here:"
        read snsregion
        aws sns subscribe --topic-arn ${snsarn} --region ${snsregion} --protocol email --notification-endpoint ${snsemail}
        echo ""

        sns_apache (){
cat << 'EOT' > /root/apache.sh
#! /bin/bash

SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

UP=$(service httpd status | grep running | grep -v not | wc -l);
host=`hostname`
msg="Apache service is down on the server ${host}"
if [ "$UP" -ne 1 ];
then
        aws sns publish --topic-arn snsarn --region snsregion --message "${msg}";
        sudo service httpd start
        aws sns publish --topic-arn snsarn --region snsregion --message "Apache started again now!";
else
        echo "All is well.";
fi
EOT
sed -i 's|snsarn|'$snsarn'|g' /root/apache.sh
sed -i 's|snsregion|'$snsregion'|g' /root/apache.sh
chmod +x /root/apache.sh
(crontab -l ; echo "* * * * * /root/apache.sh >/dev/null") | crontab -
}

        sns_mariadb (){
cat << 'EOT' > /root/mariadb.sh
#! /bin/bash

SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

UP=$(service mariadb status | grep running | grep -v not | wc -l);
host=`hostname`
msg="MariaDB service is down on the server ${host}"
if [ "$UP" -ne 1 ];
then
        aws sns publish --topic-arn snsarn --region snsregion --message "${msg}";
        sudo service mariadb start
        aws sns publish --topic-arn snsarn --region snsregion --message "MariaDB started again now!";
else
        echo "All is well.";
fi
EOT
sed -i 's|snsarn|'$snsarn'|g' /root/mariadb.sh
sed -i 's|snsregion|'$snsregion'|g' /root/mariadb.sh
chmod +x /root/mariadb.sh
(crontab -l ; echo "* * * * * /root/mariadb.sh >/dev/null") | crontab -
}

        echo ""
        echo "Send a confirmation email to the email ID and you need to confirm. Subscribe by clicking on the confirmation link then only the publisher can send an email to you......."
        echo ""
        sleep 2
        echo "Here, we are going to script for setup a bash script for MariaDB or apache down alert. Please let me know which option from below you would like to receive an notification"
        echo ""
        echo "1) Apache down alert"
        echo ""
        echo "2) MariaDB down alert"
        echo ""
        echo "3) MariaDB and apache down alert"
        echo ""

        read -p "Please choose the value (1 | 2 | 3) : " answer
        case $answer in
                1) echo "you have selected apache down alert, please hold a moment, while we set up the bash. Thank you for choosing my script"
                        sns_apache
                        ;;
                2) echo "You have selected MariaDB down alert, please hold a moment, while we set up the bash. Thank you for choosing my script"
                        sns_mariadb
                        ;;
                3) echo "You have selected MariaDB and apache alert, please hold a moment, while we set up the bash. Thank you for choosing my script"
                        sns_apache
                        sns_mariadb
                        ;;
                *) echo "You have entered an invalid option"
                        ;;
                esac

fi
```

## Conclusion

It is a simple bash script to getting an alert if an Apache or MariaDB service down. Please contact me when you encounter any difficulty error while running this script. Thank you!

### ⚙️ Connect with Me
<p align="center">
<a href="https://www.instagram.com/iamvyjith/"><img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/vyjith-ks-3bb8b7173/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>

