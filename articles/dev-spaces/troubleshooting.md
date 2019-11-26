---
title: risoluzione dei problemi
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 64b9cda61e5af3e8b9ea52477b5bf4fa879f48e6
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483862"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces troubleshooting

Questa guida contiene informazioni sui problemi comuni in cui si potrebbe incorrere quando si usa Azure Dev Spaces.

If you have a problem when using Azure Dev Spaces, create an [issue in the Azure Dev Spaces GitHub repository](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Prima di iniziare

Per risolvere i problemi in modo più efficace, può essere utile creare log maggiormente dettagliati per la revisione.

Per l'estensione di Visual Studio impostare la variabile di ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` su 1. Assicurarsi di riavviare Visual Studio per rendere effettiva la variabile di ambiente. Una volta abilitati, i log dettagliati vengono scritti nella directory `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Nell'interfaccia della riga di comando è possibile visualizzare altre informazioni durante l'esecuzione del comando usando lo switch `--verbose`. È anche possibile esplorare i log più dettagliati in `%TEMP%\Azure Dev Spaces`. In un computer Mac la directory TEMP è reperibile eseguendo `echo $TMPDIR` da una finestra del terminale. In un computer Linux la directory TEMP è generalmente `/tmp`.

Azure Dev Spaces also works best when debugging a single instance, or pod. The `azds.yaml` file contains a setting, *replicaCount*, that indicates the number of pods that Kubernetes runs for your service. If you change the *replicaCount* to configure your application to run multiple pods for a given service, the debugger attaches to the first pod, when listed alphabetically. Il debugger si collega a un pod diverso quando il pod originale viene riciclato, determinando un comportamento imprevisto.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Common issues when enabling Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Error "Failed to create Azure Dev Spaces controller"

In caso di problemi durante la creazione del controller, è possibile che venga visualizzato questo errore. Se si tratta di un errore temporaneo, per correggerlo, eliminare e ricreare il controller.

You can also try deleting the controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use the Azure Dev Spaces CLI to delete a controller. It’s not possible to delete a controller from Visual Studio. You also can't install the Azure Dev Spaces CLI in the Azure Cloud Shell so you can't delete a controller from the Azure Cloud Shell.

If you don't have the Azure Dev Spaces CLI installed, you can first install it using the following command then delete your controller:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Per ricreare il controller è possibile usare l'interfaccia della riga di comando o Visual Studio. See the [Team development](quickstart-team-development.md) or [Develop with .NET Core](quickstart-netcore-visualstudio.md) quickstarts for examples.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controller create failing because of controller name length

An Azure Dev Spaces controller's name can't be longer than 31 characters. If your controller's name exceeds 31 characters when you enable Dev Spaces on an AKS cluster or create a controller, you'll receive an error. ad esempio:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

To fix this issue, create a controller with an alternate name. ad esempio:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Enabling Dev Spaces failing when Windows node pools are added to an AKS cluster

Currently, Azure Dev Spaces is intended to run on Linux pods and nodes only. When you have an AKS cluster with a Windows node pool, you must ensure that Azure Dev Spaces pods are only scheduled on Linux nodes. If an Azure Dev Spaces pod is scheduled to run on a Windows node, that pod won't start and enabling Dev Spaces will fail.

To fix this issue, [add a taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) to your AKS cluster to ensure Linux pods aren't scheduled to run on a Windows node.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Error "Found no untainted Linux nodes in Ready state on the cluster. There needs to be at least one untainted Linux node in Ready state to deploy pods in 'azds' namespace."

Azure Dev Spaces couldn't create a controller on your AKS cluster because it couldn't find an untainted node in a *Ready* state to schedule pods on. Azure Dev Spaces requires at least one Linux node in a *Ready* state that allows for scheduling pods without specifying tolerations.

To fix this issue, [update your taint configuration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) on your AKS cluster to ensure at least one Linux node allows for scheduling pods without specifying tolerations. Also, ensure that at least one Linux node that allows scheduling pods without specifying tolerations is in the *Ready* state. If your node is taking a long time to reach the *Ready* state, you can try restarting your node.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Error "Azure Dev Spaces CLI not installed properly" when running `az aks use-dev-spaces`

An update to the Azure Dev Spaces CLI changed its installation path. If you're using a version of the Azure CLI earlier than 2.0.63, you may see this error. To display your version of the Azure CLI, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Despite the error message when running `az aks use-dev-spaces` with a version of the Azure CLI before 2.0.63, the installation does succeed. You can continue to use `azds` without any issues.

To fix this issue, update your installation of the [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) to 2.0.63 or later. This update will resolve the error message you receive when running `az aks use-dev-spaces`. Alternatively, you can continue to use your current version of the Azure CLI and the Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Error "Unable to reach kube-apiserver"

You might see this error when Azure Dev Spaces is unable to connect to your AKS cluster's API server. 

If access to your AKS cluster API server is locked down or if you have [API server authorized IP address ranges](../aks/api-server-authorized-ip-ranges.md) enabled for your AKS cluster, you must also [create](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) or [update](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) your cluster to [allow additional ranges based on your region](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Ensure that the API server is available by running kubectl commands. If the API server is unavailable, please contact AKS support and try again when the API server is working.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Common issues when preparing your project for Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Warning "Dockerfile could not be generated due to unsupported language"
Azure Dev Spaces fornisce il supporto nativo per C# e Node.js. When you run `azds prep` in a directory with code written in one of these languages, Azure Dev Spaces automatically creates an appropriate Dockerfile for you.

You can still use Azure Dev Spaces with code written in other languages, but you need to manually create the Dockerfile before running `azds up` for the first time.

If your application is written in a language that Azure Dev Spaces doesn't natively support, you need to provide an appropriate Dockerfile to build a container image running your code. Docker offre un [elenco di procedure consigliate per la scrittura di Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) e [informazioni di riferimento su Dockerfile](https://docs.docker.com/engine/reference/builder/) per la scrittura di un documento Dockerfile in base alle proprie esigenze.

Once you have an appropriate Dockerfile in place, you run `azds up` to run your application in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Common issues when starting or stopping services with Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Error "Config file not found:"

When running `azds up`, you may see this error. Both `azds up` and `azds prep` must be run from the root directory of the project you want to run in your dev space.

Per risolvere il problema:
1. Impostare come directory corrente la cartella radice contenente il codice del servizio. 
1. If you don't have a _azds.yaml_ file in the code folder, run `azds prep` to generate Docker, Kubernetes, and Azure Dev Spaces assets.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout at "Waiting for container image build..." step with AKS virtual nodes

This timeout occurs when you attempt to use Dev Spaces to run a service that is configured to run on an [AKS virtual node](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces doesn't currently support building or debugging services on virtual nodes.

Se si esegue `azds up` con l'opzione `--verbose` o si abilita la registrazione dettagliata in Visual Studio, vengono visualizzati altri dettagli:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

The above command shows that the service's pod was assigned to *virtual-node-aci-linux*, which is a virtual node.

To fix this issue, update the Helm chart for the service to remove any *nodeSelector* or *tolerations* values that allow the service to run on a virtual node. Questi valori sono in genere definiti nel file `values.yaml` del grafico.

You can still use an AKS cluster that has the virtual nodes feature enabled, if the service you wish to build or debug via Dev Spaces runs on a VM node. Running a service with Dev Spaces on a VM node is the default configuration.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Error "could not find a ready tiller pod" when launching Dev Spaces

Questo errore si verifica se il client Helm non può più comunicare con il pod Tiller in esecuzione nel cluster.

To fix this issue, restart the agent nodes in your cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Error "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\>' already exists" or "Pull access denied for \<servicename\>, repository does not exist or may require 'docker login'"

These errors can occur if you mix running direct Helm commands (such as `helm install`, `helm upgrade`, or `helm delete`) with Dev Spaces commands (such as `azds up` and `azds down`) inside the same dev space. They occur because Dev Spaces has its own Tiller instance, which conflicts with your own Tiller instance running in the same dev space.

It's fine to use both Helm commands and Dev Spaces commands against the same AKS cluster, but each Dev Spaces-enabled namespace should use either one or the other.

For example, suppose you use a Helm command to run your entire application in a parent dev space. You can create child dev spaces off that parent, use Dev Spaces to run individual services inside the child dev spaces, and test the services together. When you're ready to check in your changes, use a Helm command to deploy the updated code to the parent dev space. Don't use `azds up` to run the updated service in the parent dev space, because it will conflict with the service initially run using Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Existing Dockerfile not used to build a container

Azure Dev Spaces può essere configurato per puntare a un _Dockerfile_ specifico all'interno del progetto. Se sembra che Azure Dev Spaces non usi il documento _Dockerfile_ previsto per compilare i contenitori, potrebbe essere necessario indicare in modo esplicito ad Azure Dev Spaces quale Dockerfile usare. 

To fix this issue, open the _azds.yaml_ file that Azure Dev Spaces generated in your project. Update *configurations: develop: build: dockerfile* to point to the Dockerfile you want to use. ad esempio:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Error "unauthorized: authentication required" when trying to use a Docker image from a private registry

You're using a Docker image from a private registry that requires authentication.

To fix this issue, you can allow Dev Spaces to authenticate and pull images from this private registry using [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). To use imagePullSecrets, [create a Kubernetes secret](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in the namespace where you're using the image. Then provide the secret as an imagePullSecret in `azds.yaml`.

Below is an example of a specifying imagePullSecrets in `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Setting imagePullSecrets in `azds.yaml` will override imagePullSecrets specified in the `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Error "Service cannot be started."

Si potrebbe verificare questo errore quando il codice del servizio non viene avviato. La causa è spesso nel codice utente. To get more diagnostic information, enable more detailed logging when starting your service.

From the command line, use the `--verbose` to enable more detailed logging. You can also specify an output format using `--output`. ad esempio:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Aprire **Strumenti > Opzioni** e in **Projects and Solutions** (Progetti e soluzioni), scegliere **Build and Run** (Compila ed esegui).
2. Modificare le impostazioni per **MSBuild project build output verbosity** (Livello di dettaglio output di compilazione progetto MSBuild) in **Detailed** (Dettagliato) oppure in **Diagnostic** (Diagnostica).

    ![Screenshot della finestra di dialogo Strumenti > Opzioni](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Riesecuzione di un servizio dopo la ripetizione della creazione del controller

Viene visualizzato l'errore *Service cannot be started* quando si prova a rieseguire un servizio dopo la rimozione e quindi la ricreazione del controller Azure Dev Spaces associato al cluster. In questo caso, l'output dettagliato contiene il testo seguente:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

This error occurs because removing the Dev Spaces controller doesn't remove services previously installed by that controller. Non è possibile ricreare il controller e quindi tentare di eseguire i servizi usando il nuovo controller perché i servizi precedenti sono ancora attivi.

Per risolvere questo problema, usare il comando `kubectl delete` per rimuovere manualmente i servizi precedenti dal cluster, quindi eseguire di nuovo Dev Spaces per installare i nuovi servizi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Error "Service cannot be started." when using multi-stage Dockerfiles

Viene visualizzato l'errore *Service cannot be started* quando si usa un documento Dockerfile a più fasi. In questo caso, l'output dettagliato contiene il testo seguente:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

This error occurs because AKS nodes run an older version of Docker that doesn't support multi-stage builds. Per evitare compilazioni in più fasi, riscrivere il documento Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Network traffic is not forwarded to your AKS cluster when connecting your development machine

When using [Azure Dev Spaces to connect your AKS cluster to your development machine](how-to/connect.md), you may encounter an issue where network traffic is not forwarded between your development machine and your AKS cluster.

When connecting your development machine to your AKS cluster, Azure Dev Spaces forwards network traffic between your AKS cluster and your development machine by modifying your development machine's `hosts` file. Azure Dev Spaces creates an entry in the `hosts` with the address of the Kubernetes service you are replacing as a host name. This entry is used with port forwarding to direct network traffic between your development machine and the AKS cluster. If a service on your development machine conflicts with the port of the Kubernetes service you are replacing, Azure Dev Spaces cannot forward network traffic for the Kubernetes service. For example, the *Windows BranchCache* service is usually bound to *0.0.0.0:80*, which conflicts will cause a conflict for port 80 on all local IPs.

To fix this issue, you need to stop any services or processes that conflict with port of the Kubernetes service you are trying to replace. You can use tools, such as *netstat*, to inspect what services or processes on your development machine are in conflict.

For example, to stop and disable the *Windows BranchCache* service:
* Run `services.msc` from the command prompt.
* Right click on *BranchCache* and select *Properties*.
* Click *Stop*.
* Optionally, you can disable it by setting *Startup type* to *Disabled*.
* Fare clic su *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Common issues using Visual Studio and Visual Studio Code with Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Error "Required tools and configurations are missing"

Questo errore può verificarsi quando si avvia VS Code: "[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing". ([Azure Dev Spaces] Gli strumenti e le configurazioni richiesti per la compilazione di '[nome progetto]' sono mancanti).
Questo errore indica che azds.exe non è presente nella variabile di ambiente PATH, come illustrato in VS Code.

Try launching VS Code from a command prompt where the PATH environment variable is set properly.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Errore "Required tools to build and debug 'projectname' are out of date" (Gli strumenti necessari per la compilazione e il debug di 'nome progetto' non sono aggiornati)

Questo errore viene visualizzato in Visual Studio Code se sono presenti una versione dell'estensione VS Code per Azure Dev Spaces più recente e una versione dell'interfaccia della riga di comando di Azure Dev Spaces meno recente.

Try downloading and installing the latest version of the Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Error: "Failed to find debugger extension for type:coreclr"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for C# installed on your development machine. The C# extension includes debugging support for .NET Core (CoreCLR).

To fix this issue, install the [VS Code extension for C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Error "Configured debug type 'coreclr' is not supported"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for Azure Dev Spaces installed on your development machine.

To fix this issue, install the [VS Code extension for Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Error "Invalid 'cwd' value '/src'. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato) o "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[percorso file binario di progetto]' non esiste)

You may see this error when running the Visual Studio Code debugger. Per impostazione predefinita, l'estensione VS Code usa `src` come directory di lavoro per il progetto nel contenitore. Se è stato aggiornato il `Dockerfile` per specificare una directory di lavoro diversa, è possibile che venga visualizzato questo errore.

To fix this issue, update the `launch.json` file under the `.vscode` subdirectory of your project folder. Modificare la direttiva `configurations->cwd` in modo da puntare alla stessa directory `WORKDIR` definita nel `Dockerfile` del progetto. Potrebbe inoltre essere necessario aggiornare anche la direttiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Error "The pipe program 'azds' exited unexpectedly with code 126."

You may see this error when running the Visual Studio Code debugger.

To fix this issue, close and reopen Visual Studio Code. Restart the debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Error "Internal watch failed: watch ENOSPC" when attaching debugging to a Node.js application

This error occurs when the node running the pod with the Node.js application you're trying to attach to with a debugger has exceeded the *fs.inotify.max_user_watches* value. In some cases, [the default value of *fs.inotify.max_user_watches* may be too small to handle attaching a debugger directly to a pod](https://github.com/Azure/AKS/issues/772).

A temporary workaround for this issue is to increase the value of *fs.inotify.max_user_watches* on each node in the cluster and restart that node for the changes to take effect.

## <a name="other-common-issues"></a>Other common issues

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Error "azds" is not recognized as an internal or external command, operable program, or batch file

This error can happen if `azds.exe` is not installed or configured correctly.

Per risolvere il problema:

1. Check the location %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for `azds.exe`. Se è presente, aggiungere tale percorso alla variabile di ambiente PATH.
2. If `azds.exe` isn't installed, run the following command:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Authorization error "Microsoft.DevSpaces/register/action"

Per gestire Azure Dev Spaces, è necessario l'accesso *Proprietario* o *Collaboratore* nella sottoscrizione di Azure. If you're trying to manage Dev Spaces and you don't have *Owner* or *Contributor* access to the associated Azure subscription, you may see an authorization error. ad esempio:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

To fix this issue, using an account with *Owner* or *Contributor* access to the Azure subscription, manually register the `Microsoft.DevSpaces` namespace:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>New pods aren't starting

The Kubernetes initializer can't apply the PodSpec for new pods due to RBAC permission changes to the *cluster-admin* role in the cluster. The new pod may also have an invalid PodSpec, for example the service account associated with the pod no longer exists. To see the pods that are in a *Pending* state due to the initializer issue, use the `kubectl get pods` command:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

This issue can impact pods in *all namespaces* in the cluster including namespaces where Azure Dev Spaces is not enabled.

To fix this issue, [update the Dev Spaces CLI to the latest version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) and then deleting the *azds InitializerConfiguration* from the Azure Dev Spaces controller:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Once you have removed the *azds InitializerConfiguration* from the Azure Dev Spaces controller, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, redeploy your pods.

If new pods are still stuck in a *Pending* state after a redeployment, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, delete the controller from the cluster and reinstall it:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

After your controller is reinstalled, redeploy your pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Incorrect RBAC permissions for calling Dev Spaces controller and APIs

The user accessing the Azure Dev Spaces controller must have access to read the admin *kubeconfig* on the AKS cluster. For example, this permission is available in the [built-in Azure Kubernetes Service Cluster Admin Role](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). The user accessing the Azure Dev Spaces controller must also have the *Contributor* or *Owner* RBAC role for the controller. More details on updating a user's permissions for an AKS cluster are available [here](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

To update the user's RBAC role for the controller:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Navigate to the Resource Group containing the controller, which is usually the same as your AKS cluster.
1. Enable the *Show hidden types* checkbox.
1. Click on the controller.
1. Open the *Access Control (IAM)* pane.
1. Click on the *Role Assignments* tab.
1. Click *Add* then *Add role assignment*.
    * For *Role*, select either *Contributor* or *Owner*.
    * In *Assegna accesso a* selezionare *Utente, gruppo o entità servizio di Azure AD*.
    * For *Select*, search for the user you want to give permissions.
1. Fare clic su *Salva*

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Risoluzione dei nomi DNS non completa l'operazione per un URL pubblico associato al servizio Dev Spaces

È possibile configurare un endpoint URL pubblico per il servizio specificando l'opzione `--public` per il comando `azds prep` o selezionando la casella di controllo `Publicly Accessible` in Visual Studio. Il nome DNS pubblico viene registrato automaticamente quando si esegue il servizio in Dev Spaces. Se questo nome DNS non viene registrato, viene visualizzato un errore simile a *Non è possibile visualizzare la pagina* o *Non è possibile raggiungere il sito* nel Web browser quando ci si connette all'URL pubblico.

Per risolvere il problema:

* Check the status of all URLs associated with your Dev Spaces services:

  ```console
  azds list-uris
  ```

* If a URL is in the *Pending* state, Dev Spaces is still waiting for DNS registration to complete. In alcuni casi sono necessari alcuni minuti per completare la registrazione. Dev Spaces offre anche un tunnel localhost per ogni servizio, che è possibile usare durante l'attesa della registrazione DNS.
* Se un URL rimane nello stato *In sospeso* per più di 5 minuti, ciò potrebbe indicare un problema con il pod DNS esterno che crea l'endpoint pubblico o con il pod del controller in ingresso nginx che è responsabile dell'acquisizione dell'endpoint pubblico. Use the following commands to delete these pods and allow AKS to automatically recreate them:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Error "upstream connect error or disconnect/reset before headers"

Questo errore potrebbe essere visualizzato quando si tenta di accedere al servizio. Ad esempio, quando si passa all'URL del servizio in un browser. This error means the container port isn't available. This can for the follow reasons:

* Il contenitore è ancora in fase di compilazione e distribuzione. Il problema si può verificare se si esegue `azds up` o si avvia il debugger e quindi si tenta di accedere al contenitore prima che sia stato distribuito correttamente.
* La configurazione della porta non è coerente tra il _Dockerfile_, il grafico Helm e il codice server che consente di aprire una porta.

Per risolvere il problema:

1. Se il contenitore è in fase di compilazione o distribuzione, attendere 2-3 secondi e tentare di accedere nuovamente al servizio. 
1. Verificare la configurazione della porta. I numeri di porta specificati devono essere **identici** in tutti gli asset seguenti:
    * **Dockerfile:** specificato dall'istruzione `EXPOSE`.
    * **[Grafico Helm](https://docs.helm.sh):** specificato dai valori `externalPort` e `internalPort` per un servizio (spesso si trova in un file `values.yml`),
    * Qualsiasi porta da aprire nel codice applicazione, ad esempio in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>The type or namespace name "MyLibrary" couldn't be found

A library project you're using can't be found. With Dev Spaces, the build context is at the project/service level by default.  

Per risolvere il problema:

1. Modify the `azds.yaml` file to set the build context to the solution level.
2. Modify the `Dockerfile` and `Dockerfile.develop` files to refer to the project files, for example `.csproj`, correctly relative to the new build context.
3. Add a `.dockerignore` in the same directory as the `.sln` file.
4. Update the `.dockerignore` with additional entries as needed.

You can find an example at [here](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizontal pod autoscaling not working in a dev space

When you run a service in a dev space, that service's pod is [injected with additional containers for instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster) and all the containers in a pod need to have resource limits and requests set for Horizontal Pod Autoscaling.

To fix this issue, apply a resource request and limit to the injected Dev Spaces containers. Resource requests and limits can be applied for the injected container (devspaces-proxy) by adding the `azds.io/proxy-resources` annotation to your pod spec. The value should be set to a JSON object representing the resources section of the container spec for the proxy.

Below is an example of a proxy-resources annotation that is to be applied to your pod spec.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Enable Azure Dev Spaces on an existing namespace with running pods

You may have an existing AKS cluster and namespace with running pods where you want to enable Azure Dev Spaces.

To enable Azure Dev Spaces on an existing namespace in an AKS cluster, run `use-dev-spaces` and use `kubectl` to restart all pods in that namespace.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

After your pods have restarted, you can begin using your existing namespace with Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Enable Azure Dev Spaces on AKS cluster with restricted egress traffic for cluster nodes

To enable Azure Dev Spaces on an AKS cluster for which the egress traffic from cluster nodes is restricted, you will have to allow following FQDNs:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | To pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 | To pull helm/tiller images|
| storage.googleapis.com | HTTP:443 | To pull helm/tiller images|
| azds-<guid>.<location>.azds.io | HTTPS:443 | To communicate with Azure Dev Spaces backend services for your controller. The exact FQDN can be found in the "dataplaneFqdn" in %USERPROFILE%\.azds\settings.json|