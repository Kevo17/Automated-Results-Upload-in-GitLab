<h1> Automated Results Upload in GitLab</h1>


<h2>Description</h2>
This lab focuses on automating the results upload process in GitLab, enabling seamless integration of test and analysis results into the development workflow. By automating the upload of test results, code coverage reports, and other analysis outputs, developers can easily access and analyze the information within the GitLab platform, facilitating efficient collaboration and decision-making.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Bandit</b>
- <b>ZAP</b>
- <b>JSON</b>
- <b>GitLab</b>
- <b>DefectDojo</b>

<h2>Environments Used </h2>

- <b>Windows 11</b>
- <b>Linux</b> 

<h2>Program walk-through:</h2>

To work with git repositories, we first need to set up a username and email. We can use git config commands to set it up: <br/>
```
git config --global user.email "student@pdevsecops.com"
```
```
git config --global user.name "student"
``` 
<p align="center">
<img src="https://i.imgur.com/il0mquz.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

We can use the git clone command to download the django.nv git repository to our local machine: <br/>
```
git clone git@gitlab-ce-rcgsg0ei:root/django-nv.git
``` 
<p align="center">
<img src="https://i.imgur.com/s5UNaJj.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Lets cd into this repository to explore its content: <br/>
```
cd django-nv
``` 
<p align="center">
<img src="https://i.imgur.com/4tBPGYd.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

First, lets download the upload-results.py script using the following curl command: <br/>
```
curl https://gitlab.practical-devsecops.training/-/snippets/3/raw -o upload-results.py
``` 
<p align="center">
<img src="https://i.imgur.com/1d2aQuL.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Add the file and push it to the django.nv repository: <br/>
```
git add upload-results.py
git commit -m "Add upload-results.py file"
``` 
<p align="center">
<img src="https://i.imgur.com/BKb6R9G.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

We have internet connectivity, lets push it to the remote git repository using the git push command: <br/>
```
git push origin main
``` 
<p align="center">
<img src="https://i.imgur.com/PokEBAv.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Use the terminal provided on the right to scan the production machine https://prod-rcgsg0ei.lab.practical-devsecops.training in DevSecOps Box with the help of the ZAP docker image owasp/zap2docker-stable:2.10.0 and save the result at /django-nv/zap-output.xml: <br/>
```
docker run --user $(id -u):$(id -g) -w /zap -v $(pwd):/zap/wrk:rw --rm owasp/zap2docker-stable:2.10.0 zap-baseline.py -t https://prod-rcgsg0ei.lab.practical-devsecops.training -d -x zap-output.xml
``` 
<p align="center">
<img src="https://i.imgur.com/NbIbd5N.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

After you store the ZAP scan results, please upload the result manually to Defect Dojo using the terminal on the right: <br/>
```
export API_KEY=$(curl -s -XPOST -H 'content-type: application/json' https://dojo-rcgsg0ei.lab.practical-devsecops.training/api/v2/api-token-auth/ -d '{"username": "root", "password": "pdso-training"}' | jq -r '.token' )<br/>
python3 upload-results.py --host dojo-rcgsg0ei.lab.practical-devsecops.training --api_key $API_KEY --engagement_id 1 --product_id 1 --lead_id 1 --environment "Production" --result_file zap-output.xml --scanner "ZAP Scan"<br/>
``` 
<p align="center">
<img src="https://i.imgur.com/ijwpg06.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Please update the .gitlab-ci.yml file and add the upload-results.py as part of the ZAP Scan in the CI/CD pipeline inside the after_script attribute: <br/>
 
<p align="center">
<img src="https://i.imgur.com/a9gXYcq.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>
<p align="center">
<img src="https://i.imgur.com/xwFw0mc.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Also add neccessary variables to authenticate to DefectDojo: <br/>
 
<p align="center">
<img src="https://i.imgur.com/diClZj4.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>
<p align="center">
<img src="https://i.imgur.com/vWTgJMk.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Here are the automated uploaded results in DefectDojo: <br/>
 
<p align="center">
<img src="https://i.imgur.com/DwWJhuk.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>
<p align="center">
<img src="https://i.imgur.com/jKwhOVU.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
