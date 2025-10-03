# SBOM

```shell

curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

syft docker.io/kodekloud/webapp-color:latest -o spdx > /root/webapp-spdx.sbom

syft docker.io/kodekloud/webapp-color:latest -o cyclonedx-json > /root/webapp-sbom.json

curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b /usr/local/bin

grype sbom:/root/webapp-sbom.json -o json > /root/grype-report.json

jq -r '[.matches[] | select(.vulnerability.severity == "Critical")] | length' /root/grype-report.json

cat grype-report.json | jq -e '.matches[] | select(.vulnerability.id == "CVE-2022-48174")'

jq -e '.matches[] | select(.vulnerability.id == "CVE-2018-1000517") | .vulnerability.description' grype-report.json
```

```shell

```