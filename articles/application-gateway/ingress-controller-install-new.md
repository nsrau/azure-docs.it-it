---
title: Creazione di un controller in ingresso con un nuovo gateway applicazioneCreating an ingress controller with a new Application Gateway
description: In questo articolo vengono fornite informazioni su come distribuire un controller di ingresso del gateway applicazione con un nuovo gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239470"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Come installare un controller di ingresso del gateway applicazione (AGIC) utilizzando un nuovo gateway applicazione

Le istruzioni riportate di seguito presuppongono che il controller AGIC (Application Gateway Ingress Controller) verrà installato in un ambiente senza componenti preesistenti.

## <a name="required-command-line-tools"></a>Strumenti della riga di comando necessari

È consigliabile usare [Azure Cloud Shell](https://shell.azure.com/) per tutte le operazioni della riga di comando di seguito. Avviare la shell da shell.azure.com o facendo clic sul collegamento:

[![Lancio dell'incorporamento](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)

In alternativa, avviare Cloud Shell dal portale di Azure usando l'icona seguente:Alternatively, launch Cloud Shell from Azure portal using the following icon:

![Avvio del portale](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure Cloud Shell](https://shell.azure.com/) dispone già di tutti gli strumenti necessari. Se si sceglie di utilizzare un altro ambiente, assicurarsi che siano installati i seguenti strumenti della riga di comando:

* `az`- Interfaccia della riga di comando di Azure: [istruzioni di installazione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Strumento da riga di comando Kubernetes: [istruzioni di installazione](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Gestore pacchetti Kubernetes: [istruzioni di installazione](https://github.com/helm/helm/releases/latest)
* `jq`- processore JSON da riga di comando: [istruzioni di installazione-](https://stedolan.github.io/jq/download/) command-line JSON processor: installation instructions


## <a name="create-an-identity"></a>Creare un'identitàCreate an Identity

Seguire i passaggi seguenti per creare un [oggetto entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)di Azure Active Directory (AAD). Si prega `appId` `password`di `objectId` registrare i valori , e - questi verranno utilizzati nei passaggi seguenti.

1. Creare un'entità servizio ACTIVE[(ulteriori informazioni sul controllo degli accessi in base al](https://docs.microsoft.com/azure/role-based-access-control/overview)ruolo):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    I `appId` `password` valori e dell'output JSON verranno usati nei passaggi seguentiThe e values from the JSON output will be used in the following steps


1. Usare `appId` l'output del comando precedente `objectId` per ottenere l'oggetto della nuova entità servizio:Use the from the previous command's output to get the of the new service principal:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    L'output di `objectId`questo comando è , che verrà usato nel modello di Azure Resource Manager riportato di seguito

1. Creare il file dei parametri che verrà usato nella distribuzione del modello di Azure Resource Manager in un secondo momento.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Per distribuire un cluster abilitato `aksEnabledRBAC` al controllo degli **accessi** in base al ruolo, impostare il campo su`true`

## <a name="deploy-components"></a>Distribuire i componentiDeploy Components
Questo passaggio aggiungerà i componenti seguenti alla sottoscrizione:This step will add the following components to your subscription:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) con 2 subnetVirtual Network with 2 [subnets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), che verrà utilizzato [dall'identità del pod AAD](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Scaricare il modello di Azure Resource Manager e modificare il modello in base alle esigenze.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Distribuire il modello di `az cli`Azure Resource Manager usando . L'operazione potrebbe richiedere fino a 5 minuti.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Al termine della distribuzione, scaricare l'output della distribuzione in un file denominato `deployment-outputs.json`.
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configurare il controller di ingresso del gateway applicazioneSet up Application Gateway Ingress Controller

Con le istruzioni nella sezione precedente, abbiamo creato e configurato un nuovo cluster AKS e un gateway applicazione. Ora siamo pronti per distribuire un'app di esempio e un controller in ingresso nella nostra nuova infrastruttura Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Credenziali di configurazione Kubernetes
Per i passaggi seguenti, è necessario il comando di installazione [kubectl,](https://kubectl.docs.kubernetes.io/) che verrà utilizzato per connettersi al nuovo cluster Kubernetes. [Cloud](https://shell.azure.com/) Shell `kubectl` è già stato installato. Utilizzeremo `az` CLI per ottenere le credenziali per Kubernetes.

Ottenere le credenziali per aKS appena distribuito[(ulteriori informazioni):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Installare AAD Pod Identity
  Azure Active Directory Pod Identity fornisce l'accesso basato su token ad [Azure Resource Manager (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) aggiungerà i seguenti componenti al cluster Kubernetes:
   * Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`: `AzureAssignedIdentity`,`AzureIdentityBinding`
   * Componente [Controller identità gestita](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Componente [Identità gestita del nodo](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Per installare AAD Pod Identity nel cluster:

   - *Controllo degli accessi in base al ruolo* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *Controllo degli accessi in base al ruolo* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Installare Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) è un package manager per Kubernetes. Lo sfrutteremo `application-gateway-kubernetes-ingress` per installare il pacchetto:

1. Installare [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) ed eseguire `application-gateway-kubernetes-ingress` quanto segue per aggiungere il pacchetto helm:

    - *Controllo degli accessi in base al ruolo* Cluster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *Controllo degli accessi in base al ruolo* Cluster AKS

        ```bash
        helm init
        ```

1. Aggiungere il repository Helm AGIC:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Installare il grafico Helm del controller di ingresso

1. Utilizzare `deployment-outputs.json` il file creato in precedenza e creare le seguenti variabili.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Scaricare helm-config.yaml, che configurerà AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    In alternativa, copiare il file YAML riportato di seguito: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Modificare il file helm-config.yaml appena `appgw` scaricato `armAuth`e compilare le sezioni e .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Valori:
     - `verbosityLevel`: imposta il livello di dettaglio dell'infrastruttura di registrazione AGIC. Per i valori possibili, vedere [Livelli di registrazione](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
     - `appgw.subscriptionId`: ID sottoscrizione di Azure in cui si trova il gateway applicazione. Esempio: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: nome del gruppo di risorse di Azure in cui è stato creato il gateway applicazione. Esempio: `app-gw-resource-group`
     - `appgw.name`: nome del gateway applicazione. Esempio: `applicationgatewayd0f0`
     - `appgw.shared`: il valore predefinito di `false`questo flag booleano deve essere . Impostare `true` su se è necessario un [gateway applicazione condiviso](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: specificare lo spazio dei nomi che AGIC deve controllare. Può trattarsi di un singolo valore stringa o di un elenco separato da virgole di spazi dei nomi.
    - `armAuth.type`: potrebbe `aadPodIdentity` essere o`servicePrincipal`
    - `armAuth.identityResourceID`: ID risorsa dell'identità gestita di Azure
    - `armAuth.identityClientId`: l'ID client dell'identità. Vedi sotto per maggiori informazioni sull'identità
    - `armAuth.secretJSON`: necessario solo quando viene scelto `armAuth.type` il tipo `servicePrincipal`di segreto dell'entità servizio (quando è stato impostato su ) 


   > [!NOTE]
   > I `identityResourceID` `identityClientID` valori e sono stati creati durante i passaggi [Create an Identity](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) e possono essere ottenuti nuovamente utilizzando il comando seguente:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`nel comando precedente è il gruppo di risorse del gateway applicazione. `<identity-name>`è il nome dell'identità creata. Tutte le identità per una determinata sottoscrizione possono essere elencate tramite:All identities for a given subscription can be listed using:`az identity list`


1. Installare il pacchetto del controller di ingresso del gateway applicazione:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Installare un'app di esempioInstall a Sample App
Ora che abbiamo installato Gateway applicazione, AKS e AGIC possiamo installare un'app di esempio tramite [Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

In alternativa è possibile:

* Scaricare il file YAML sopra:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Applicare il file YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Altri esempi
Questa [guida alle procedure](ingress-controller-expose-service-over-http-https.md) contiene altri esempi su come esporre un servizio AKS tramite HTTP o HTTPS a Internet con il gateway applicazione.
