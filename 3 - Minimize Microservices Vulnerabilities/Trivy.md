# Trivy

```shell
#Add the trivy-repo
apt-get  update
apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

#Update Repo and Install trivy
apt-get update
apt-get install trivy
```

```shell
# Scan the vulnerability
trivy image --output /root/python_12.txt public.ecr.aws/docker/library/python:3.12.4

trivy image --input alpine.tar --format json --output /root/alpine.json
```