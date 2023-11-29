# Unsupported container for the `sysdig-cli-scanner`

A few notes:

*Secure Api Token is your API Token within Sysdig, under your user profile.

*For support please reach out to your Sales Engineer.  

## Deploying and using sysdig-cli-scanner

This will download the scanner and do a sample scan on the dummy docker image

docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.6.1 --apiurl *api-url* pull://docker.io/sysdiglabs/dummy-vuln-app 

Or view the package page:

https://github.com/dalejrodriguez/secure-inline-scan-examples/pkgs/container/sysdig-cli-scanner

## Sysdig API URLs

US East (North Virgina) - https://secure.sysdig.com/

US West (Oregon) - https://us2.app.sysdig.com/

US West (GCP) - https://app.us4.sysdig.com/

EU - https://eu1.app.sysdig.com/

Asia Pacific (Sydney) - https://app.au1.sysdig.com/

## Integrate in your CI/CD Pipelines

The `sysdig-cli-scanner` can be included as a step in your CI/CD pipelines (i.e. Jenkins, Github actions or others) simply by running the `sysdig-cli-scanner` command as part of your pipeline.

* Make sure that the `sysdig-cli-scanner` binary is available as part of the worker or runner where the pipeline is executing.
  * If you are running an ephemeral environment in the pipeline, include the download and set executable steps in your pipeline to download the tool on every execution.
* Define a secret containing the API-Token and make it available in the pipeline (i.e. via a `SECURE_API_TOKEN` environment variable).
* Include a step in your pipeline to run the `sysdig-cli-scanner` after building the container image, and providing the image name as paremeter. For example:

```yaml
docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.6.1 --apiurl <sysdig-api-url> ${IMAGE_NAME}
```

See some examples on how to use it on different CI/CD pipelines:

* [AWS CodeBuild](https://github.com/sysdiglabs/secure-inline-scan-examples/tree/main/aws-codebuild/new-scan-engine)
* [Azure Pipelines](https://github.com/sysdiglabs/secure-inline-scan-examples/tree/main/azure-pipelines/new-scan-engine)
* [GitHub](https://github.com/sysdiglabs/secure-inline-scan-examples/tree/main/github/new-scan-engine)
* [GitLab](https://github.com/sysdiglabs/secure-inline-scan-examples/tree/main/gitlab/new-scan-engine)
* [Jenkins](https://github.com/sysdiglabs/secure-inline-scan-examples/tree/main/jenkins/new-scan-engine)

## Integrating Vulnerability Policies

Policies allow you to define a set of rules that will evaluate each scan result. After the evaluation, each policy will pass or fail. A policy failure or non-compliance happens if the scan result doesn't meet all the rules in a policy.

For CI/CD and manual image scans, you can tell the `sysdig-cli-scanner` tool to explicitly evaluate one or more policies using the `--policy= policy1,policy2,...` flag and provide a comma-separated list of policy IDs.

CI/CD policies can be configured as **Always apply**. If a policy has the **Always apply** flag, it will be evaluated on every scanned image even if you don't specify it explicitly.

Learn more about Vulnerability Management policies, the available rules, and how to define policies in [Vulnerability Policies](https://docs.sysdig.com/en/docs/sysdig-secure/policies/vulnerability-policies/).

## Parameters

Basic usage of the sysdig-cli-scanner:

`sysdig-cli-scanner [OPTIONS] <ImageName>`

#### Required

| Option                | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| `SECURE_API_TOKEN`    | Provide the API token as environment variable `SECURE_API_TOKEN` . You can retrieve this from [Settings > User Profile](https://docs.sysdig.com/en/docs/administration/administration-settings/user-profile-and-password/retrieve-the-sysdig-api-token/) in Sysdig Secure. |
| `--apiurl=<endpoint>` | Sysdig Secure Endpoint. In SaaS, this value is [region-dependent](https://docs.sysdig.com/en/docs/administration/saas-regions-and-ip-ranges/)  and is auto-completed on the Get Started page in the UI. |
| `ImageName`           | The image that you want to scan. For example `mongo-express:0.54.0`. |

* The Sysdig CLI scanner will try to find a local image in Docker, ContainerD or other container runtimes, or try to pull if from the remote registry.
* Once the scan is complete, you will see the results directly in the console, and they will be available in the [Pipeline](https://docs.sysdig.com/en/docs/sysdig-secure/vulnerabilities/pipeline/#review-pipeline-scan) section of the UI.

### Registry Credentials

Registry credentials can be supplied via the following environment variables

| Option              | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `REGISTRY_USER`     | Provide the registry username as environment variable `REGISTRY_USER`. |
| `REGISTRY_PASSWORD` | Provide the registry password as environment variable `REGISTRY_PASSWORD`. |

Example

```
$ REGISTRY_USER=<YOUR_REGISTRY_USERNAME> REGISTRY_PASSWORD=<YOUR_REGISTRY_PASSWORD> SECURE_API_TOKEN=<YOUR_API_TOKEN> docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.5.2 --apiurl *api-url* ${REPO_NAME}/${IMAGE_NAME}

```

### Additional Parameters

To display a list of all available command line parameters:

```
docker run ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.5.2 -h
```

Example output: 

```
Usage:
  sysdig-cli-scanner [OPTIONS] [ImageName]

Application Options:
  -a, --apiurl=                 Secure API base URL
  -t, --apitimeout=             Secure API timeout (seconds) (default: 120)
  -n, --standalone              Do not depend on Sysdig backend for execution, avoiding the need of specifying --apiurl and SECURE_API_TOKEN. Implies activating all flags marked with [standalone mode]
                                (default: false)
      --no-cache                Do not use any cache throughout the scan; other cache-related parameters will be ignored (default: false)
      --offline-analyzer        [standalone mode] The analyzer does not perform backend calls (default: false)
      --override-pullstring=    Specify a custom image name that will be displayed on the Sysdig UI
      --output-json=            Output path of the scan result report in json format
      --json-scan-result=       Output file of the json scan result in the new format in technical preview (breaking changes are possible)
      --policy=                 Identifier of policy to apply
  -d, --dbpath=                 Database directory absolute path. PLEASE NOTE THAT FILES IN THIS DIRECTORY COULD BE WIPED OUT. Defaults to {executablePath}/main.db
  -p, --cachepath=              Cache path
  -c, --clearcache              Clear the cache before to run (default: false)
  -l, --loglevel=               Log level (default: info)
  -o, --logfile=                File destination for logs, mutually exclusive with --console-log
      --console-log             Force logs to console, mutually exclusive with --logfile
  -s, --skiptlsverify           Skip TLS certificate verification (default: false)
  -u, --skipupload              [standalone mode] Do not upload the scan results (default: false)
      --skip-get-database       [standalone mode] Do not download DB even if obsolete or corrupted (default: false)
      --full-vulns-table        Show the entire list of packages found
      --detailed-policies-eval  Show a detailed view of the policies evaluation
      --version                 Show the version and exit

Help Options:
  -h, --help                    Show this help message

Arguments:
  ImageName:                    Image name

```

## CLI Scanner Exit Codes

Access the container exit codes with `-h`

The codes are:

-   **0** - **image** **passed** policy evaluation

-   **1** - **image** **failed** policy evaluation

-   **2** - **incorrect** parameters (i.e. no API token)

-   **3** - **other** execution errors

Use the exit code, for example, to decide whether to abort the CI/CD pipeline.

## Image Sources

The Sysdig CLI scanner can load images from different sources. By default, it will try to automatically find the provided image name from all supported sources, in the order specified by the following list. However, you can explicitly select the image source by using the corresponding prefix for the image name:

- `file://` - Load the image from a .tar file
- `docker://` - Load the image from the Docker daemon (honoring `DOCKER_HOST` environment variable or other Docker configuration files)
- `podman://` - Load the image from the Podman daemon
- `pull://` - Force pulling the image from a remote repository (ignoring local images with same name)
- `containerd://` - Load the image from Containerd daemon
- `crio://` - Load the image from Containers Storage location


i.e. pull the image from remote registry even if it is locally available:

```yaml
docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.6.1 -a *api-url* pull://nginx:latest
```

### Sample Result in Terminal

It is possible to view scan results in the terminal window (see below)

```
$ docker run -e SECURE_API_TOKEN="X" ghcr.io/dalejrodriguez/sysdig-cli-scanner:1.6.1 --apiurl https://us2.app.sysdig.com docker.io/sysdiglabs/dummy-vuln-app

Type: dockerImage
ImageID: sha256:b670c067178c876d17363baec279d483ae07384351d1a0be7646230442471ac6
Digest: sha256:bc86e8ba5741ab71ce50f13fbf89a1f27dc4e1d3b0c3345cee8e3238bc30022b
BaseOS: debian 9.13
PullString: docker.io/sysdiglabs/dummy-vuln-app

18 vulnerabilities found
2 Critical (0 fixable)
8 High (6 fixable)
7 Medium (6 fixable)
1 Low (1 fixable)
0 Negligible (0 fixable)

   PACKAGE     TYPE   VERSION  SUGGESTED FIX  CRITICAL  HIGH  MEDIUM  LOW  NEGLIGIBLE  EXPLOIT
  Werkzeug    python   1.0.1       2.2.3         1       1      0      1       0          0
  pip         python   9.0.1       19.2          0       2      1      0       0          0
  numpy       python  1.12.1      1.14.0         0       1      3      0       0          0
  Flask       python   1.1.2       2.2.5         0       1      0      0       0          0
  pyxdg       python   0.25        0.26          0       1      0      0       0          0
  wheel       python  0.29.0      0.38.0         0       1      0      0       0          0
  Jinja2      python  2.11.2      2.11.3         0       0      1      0       0          0
  setuptools  python  33.1.1      65.5.1         0       0      1      0       0          0

                                                                POLICIES EVALUATION
    Policy: Sysdig Best Practices FAILED (12 failures - 0 risks accepted)
    Policy: Dale_Policy_2         FAILED (12 failures - 0 risks accepted)
```

You can use `--full-vulns-table` or `--detailed-policies-eval` flags to include further details in the output.

For a more user-friendly scan result, find the image in the UI.

## JSON Report Output

{{% infobox type="note" %}}

This feature is in Technical Preview status.

{{% /infobox %}}

You can use the `--output-json=/path/to/file.json` to write a JSON report of the scan result


## Scan Logs (Troubleshooting)

The `sysdig-cli-scanner` automatically writes a log file on every execution. You can change the output path using `-o` or `--logfile` flags. For troubleshooting purposes, you can change the log level by setting `--loglevel=debug`. This will increase the verbosity of the log messages to the `debug` level.

## Next Steps

Review the scan results in the [Vulnerabilities > Pipeline UI](https://docs.sysdig.com/en/docs/sysdig-secure/vulnerabilities/pipeline/#review-pipeline-scan). 
