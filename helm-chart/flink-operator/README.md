# Helm Chart for Flink Operator

This is the Helm chart for the Flink operator.

## Installing the Chart

The instructions to install the Flink operator chart:

1. Prepare a Flink operator image. You can either use a released image e.g., `ghcr.io/spotify/flink-operator:latest` or follow the instructions [here](https://github.com/spotify/flink-on-k8s-operator/blob/master/docs/developer_guide.md#build-and-push-the-operator-image) to build and push an image from the source code.

2. Run the bash script `update_template.sh` to update the manifest files in templates from the Flink operator source repo (This step is only required if you want to install from the local chart repo).
    You can set the following env vars to customize the script's behaviour -
    * `export IMG=<image-name>` - Operator image, defaults to `flink-operator:latest`
    * `export NS=<namespace-name>` - Namespace to install the operator in, defaults to `flink-operator-system`

3. Register CRD - Don't manually register CRD unless helm install below fails (You can skip this step if your helm version is v3).

    ```bash
    kubectl create -f https://raw.githubusercontent.com/spotify/flink-on-k8s-operator/master/config/crd/bases/flinkoperator.k8s.io_flinkclusters.yaml
    ```

4. Finally operator chart can be installed by running:

    ```bash
    helm install --name [RELEASE_NAME] . --set operatorImage.name=[IMAGE_NAME]
    ```

    If you're installing the chart into a preexisting namespace, make sure to set the namespace name and instruct the chart not create one like so:

    ```bash
    helm install --name [RELEASE_NAME] . --set operatorImage.name=[IMAGE_NAME],flinkOperatorNamespace.name=[NAMESPACE_NAME]
    ```
## Uninstalling the Chart

To uninstall your release:

  ```bash
  helm delete [RELEASE_NAME]
  ```

CRD created by this chart are not deleted by helm uninstall. CRD deletion causes terminating all FlinkCluster CRs and related kubernetes resources, therefore it should be deleted carefully, You can manually clean up CRD:

  ```bash
  kubectl delete crd flinkclusters.flinkoperator.k8s.io
  ```
