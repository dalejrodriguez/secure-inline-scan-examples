# Unsupported container for the `sysdig-cli-scanner`

A few notes:

*This is the current workaround, please reach out to your Sales Engineer for assistance with this container. 

*Secure Api Token is your API Token within Sysdig, under your user profile.

## Deploying and using sysdig-cli-scanner

This will download the scanner and do a sample scan on the dummy docker image

docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.5.2 --apiurl *api-urls-below* pull://docker.io/sysdiglabs/dummy-vuln-app 

## Sysdig API URLs

US East (North Virgina) - https://secure.sysdig.com/

US West (Oregon) - https://us2.app.sysdig.com/

US West (GCP) - https://app.us4.sysdig.com/

EU - https://eu1.app.sysdig.com/

Asia Pacific (Sydney) - https://app.au1.sysdig.com/

