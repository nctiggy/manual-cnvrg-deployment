## Install Notes
This install will deploy converge into environments that do not permit an operator
to have cluster admin privaleges. Normally this is encountered when deploying into
large shared clusters. The copctl tool is only supported on linux or macOS environments

## Assumptions
- Metrics and logging are not handled by cnvrg and would need custom work in integrate
- Customer is providing either a istio gateway or ingress controller
- Platform will provide core functionality however certain features will not
function as intended

## CLI Tool Requirements
- [kubectl](https://kubernetes.io/docs/tasks/tools/) - Must have permissions to
at least a namespace in the cluster
- [copctl](https://github.com/AccessibleAI/cnvrg-operator/releases/tag/4.3.6-DEV-14449-export-manifests-cli) -
Tool to generate kubernetes manifests

## Information Gathering
- Wildcard Domain Suffix
- Control Plane Image (*cnvrg to provide*)
- Registry Username (*cnvrg to provide*)
- Registry Password (*cnvrg to provide*)
- Ingress Type (*istio, openshift, nodeport or ingress*)
- Container Runtime (*containerd or docker*)
- Namespace (*Must already exist*)
- https (*only include if needing https*)

## Manifest Generation
1. Download the kubectl and copctl binaries
2. Install to path and make executable
    ```
    cp kubectl /usr/local/bin && chmod +x /usr/local/bin/kubectl
    cp copctl /usr/local/bin && chmod +x /usr/local/bin/copctl
    ```
3. The copctl command will create a directory in your current working directory named
`cnvrg-manifests`
4. Run the following command to generate manifest files. Make sure to replace the
arguments with the information gathered above
    ```
    copctl profile dump control-plane \
    --wildcard-domain <dnssuffix> \
    --control-plane-image <image:version> \
    --registry-user <registry username> \
    --registry-password <registry password> \
    --ingress <ingress_type> \
    --cri <container_runtime> \
    --namespace <namespace> \
    --https
    ```
4. Verify manifests were generated successfully. You should have ~69 files generated
    ```
    cd cnvrg-manifests
    ls | wc -l
    ```

## Installation
1. Now that all the manifests have been generated, you are able to make changes
if needed. For example if annotations are needed to be added to ingress objects
these can now be made
2. Once the manifests are to your liking consider storing these files in some kind
of source control system such as GitHub
3. Make sure you are back in the original working directory
    ```
    cd ..
    ```
4. To apply the manifests into the cluster run the following command
    ```
    kubectl apply -f cnvrg-manifests
    ```
5. If everything applied correctly the app should be available via browser at
`https://app.<dns-suffx>` (without the https if no tls)
