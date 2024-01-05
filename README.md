# Course App - Frontend

## Environment Setup
create front-end with userdata

```sh
#!/bin/bash
echo "step1.install dependencies"
yum install git httpd gcc-c++ make -y
yum install https://rpm.nodesource.com/pub_16.x/nodistro/repo/nodesource-release-nodistro-1.noarch.rpm -y
yum install nodejs -y
systemctl start httpd
systemctl enable httpd
echo "step2.clone git repo"
mkdir /home/ec2-user/lks-course-frontend
git clone https://github.com/betuah/lks-course-frontend.git /home/ec2-user/lks-course-frontend
echo $(ls /home/ec2-user/lks-course-frontend)
echo "step3.create environment"
touch /home/ec2-user/lks-course-frontend/.env
printf "NUXT_ENV_API_URL=http://YOUR_FRONTEND_LB" >> /home/ec2-user/lks-course-frontend/.env
echo "step4.install and run application"
npm install --prefix /home/ec2-user/lks-course-frontend/
npm run generate --prefix /home/ec2-user/lks-course-frontend/ -y
cp -R /home/ec2-user/lks-course-frontend/dist/* /var/www/html
echo "step5.create virtualhost"
touch /etc/httpd/conf.d/proxy.conf
printf "<Virtualhost *:80>\n" >> /etc/httpd/conf.d/proxy.conf
printf 'Header set Access-Control-Allow-Origin "*"\n' >> /etc/httpd/conf.d/proxy.conf
printf 'Header set Access-Control-Allow-Methods "GET, PUT, POST, DELETE, PATCH"\n' >> /etc/httpd/conf.d/proxy.conf
printf "ProxyPreserveHost On\n" >> /etc/httpd/conf.d/proxy.conf
printf "ProxyPass /api/ http://YOUR_BACKEND_INTERNAL_LB/api/\n" >> /etc/httpd/conf.d/proxy.conf
printf "ProxyPassReverse /api/ http://YOUR_BACKEND_INTERNAL_LB/api/\n" >> /etc/httpd/conf.d/proxy.conf
printf "</Virtualhost>\n" >> /etc/httpd/conf.d/proxy.conf
systemctl restart httpd
systemctl status httpd
echo finish
```
