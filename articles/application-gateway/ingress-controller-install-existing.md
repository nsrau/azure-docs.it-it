---
title: Creare un controller in ingresso con un gateway applicazione esistenteCreate an ingress controller with an existing Application Gateway
description: In questo articolo vengono fornite informazioni su come distribuire un controller di ingresso del gateway applicazione con un gateway applicazione esistente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869890"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Installare un controller AGIC (Application Gateway Ingress Controller) usando un gateway applicazione esistenteInstall an Application Gateway Ingress Controller (AGIC) using an existing Application Gateway

Il controller di ingresso del gateway applicazione (AGIC, Application Gateway Ingress Controller) è un pod all'interno del cluster Kubernetes.
AGIC monitora le risorse Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) e crea e applica la configurazione del gateway applicazione in base allo stato del cluster Kubernetes.

## <a name="outline"></a>Contorno:
- [Prerequisiti](#prerequisites)
- [Autenticazione di Azure Resource Manager (ARM)Azure Resource Manager Authentication (ARM)](#azure-resource-manager-authentication)
    - Opzione 1: [Configurare aad-pod-identity](#set-up-aad-pod-identity) e creare l'identità di Azure negli ARMOption 1: Set up aad-pod-identity and create Azure Identity on AMs
    - Opzione 2: [Utilizzo di un'entità servizioOption](#using-a-service-principal) 2: Using a Service Principal
- [Installare Ingress Controller con Helm](#install-ingress-controller-as-a-helm-chart)
- [Multi-cluster/Shared Application Gateway](#multi-cluster--shared-application-gateway): Install AGIC in un ambiente, in cui Il gateway applicazione è condiviso tra uno o più cluster AKS e/o altri componenti di Azure.Multi-cluster/ Shared Application Gateway : Install AGIC in an environment, where Application Gateway is shared between one or more AKS clusters and/or other Azure components.

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone che siano già installati i seguenti strumenti e infrastrutture:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) con [la rete avanzata](https://docs.microsoft.com/azure/aks/configure-azure-cni) abilitata
- [Gateway applicazione v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) nella stessa rete virtuale di AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) installato nel cluster AKS
- [Cloud Shell](https://shell.azure.com/) è l'ambiente `az` shell `kubectl`di `helm` Azure che dispone di CLI, e installato. Questi strumenti sono necessari per i comandi riportati di seguito.

Eseguire __il backup della configurazione del gateway applicazione__ prima di installare AGIC:
  1. uso del [portale](https://portal.azure.com/) `Application Gateway` di Azure passare all'istanza
  2. dal `Export template` clic`Download`

Il file zip scaricato avrà modelli JSON, bash e script di PowerShell che è possibile usare per ripristinare App Gateway se necessario

## <a name="install-helm"></a>Installare Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) è un package manager per Kubernetes. Lo sfrutteremo `application-gateway-kubernetes-ingress` per installare il pacchetto.
Utilizzare [Cloud Shell](https://shell.azure.com/) per installare Helm:

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

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager Authentication

AGIC comunica con il server API Kubernetes e Azure Resource Manager. Richiede un'identità per accedere a queste API.

## <a name="set-up-aad-pod-identity"></a>Configurare l'identità del pod AAD

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) è un controller, simile ad AGIC, che viene eseguito anche sul controller AKS. Associa le identità di Azure Active Directory ai pod Kubernetes.It binds Azure Active Directory identities to your Kubernetes pods. L'identità è necessaria affinché un'applicazione in un pod Kubernetes sia in grado di comunicare con altri componenti di Azure.Identity is required for an application in a Kubernetes pod to be able to communicate with other Azure components. Nel caso particolare, in questo caso, è necessaria l'autorizzazione per il pod AGIC per effettuare richieste HTTP a [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Seguire le istruzioni di installazione di [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) per aggiungere questo componente ad AKS.

Successivamente è necessario creare un'identità di Azure e assegnare le autorizzazioni ARM.
Utilizzare [Cloud Shell](https://shell.azure.com/) per eseguire tutti i comandi seguenti e creare un'identità:

1. Creare un'identità di Azure nello stesso gruppo di **risorse dei nodi AKS**. È importante scegliere il gruppo di risorse corretto. Il gruppo di risorse richiesto nel comando seguente *non* è quello a cui viene fatto riferimento nel riquadro del portale AKS. Questo è il gruppo `aks-agentpool` di risorse delle macchine virtuali. In genere tale `MC_` gruppo di risorse inizia con e contiene il nome dell'AKS. Ad esempio:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Per i comandi di assegnazione `principalId` dei ruoli riportati di seguito è necessario ottenere per l'identità appena creata:For the role assignment commands below we need to obtain for the newly created identity:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Concedere all'identità `Contributor` l'accesso al gateway applicazione. Per questo è necessario l'ID del gateway applicazione, che sarà simile al seguente:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Ottenere l'elenco degli ID del gateway applicazione nell'abbonamento con:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Concedere all'identità `Reader` l'accesso al gruppo di risorse Gateway applicazione. L'ID del gruppo `/subscriptions/A/resourceGroups/B`di risorse sarà simile al seguente: . È possibile ottenere tutti i gruppi di risorse con:You can get all resource groups with:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Utilizzo di un'entità servizioUsing a Service Principal
È anche possibile fornire a AGIC l'accesso ad ARM tramite un segreto Kubernetes.

1. Creare un'entità servizio Active Directory e codificare con base64. The base64 encoding is required for the JSON blob to be saved to Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Aggiungere il BLOB JSON codificato `helm-config.yaml` in base64 al file. Ulteriori informazioni `helm-config.yaml` sono disponibili nella sezione successiva.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Installare Il controller in uscita come grafico del grile
Nei primi passi, installiamo Helm's Tiller sul tuo cluster Kubernetes. Utilizzare [Cloud Shell](https://shell.azure.com/) per installare il pacchetto AGIC Helm:

1. Aggiungere `application-gateway-kubernetes-ingress` il repository del timone ed eseguire un aggiornamento del timone

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Modificare helm-config.yaml e inserire `appgw` i `armAuth`valori per e .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > Le `<identity-resource-id>` `<identity-client-id>` e sono le proprietà dell'identità di Azure AD configurata nella sezione precedente. È possibile recuperare queste informazioni eseguendo `az identity show -g <resourcegroup> -n <identity-name>`il `<resourcegroup>` comando seguente: , dove è il gruppo di risorse in cui vengono distribuiti l'oggetto cluster AKS di primo livello, il gateway applicazione e l'identificazione gestita.

1. Installare Helm `application-gateway-kubernetes-ingress` chart `helm-config.yaml` con la configurazione del passaggio precedente

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    In alternativa è `helm-config.yaml` possibile combinare il comando e L'elmo in un unico passaggio:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Controllare il registro del pod appena creato per verificare se è stato avviato correttamente

Fare riferimento a questa guida alle procedure per comprendere come esporre un servizio AKS su HTTP o HTTPS a Internet usando un gateway applicazione di Azure.Refer to this [how to guide](ingress-controller-expose-service-over-http-https.md) to understand how you can expose an AKS service over HTTP or HTTPS, to the internet, using an Azure Application Gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Gateway applicazione multi-cluster/condiviso
Per impostazione predefinita, AGIC presuppone la piena proprietà del gateway applicazione a cui è collegato. AGIC versione 0.8.0 e successive possono condividere un singolo gateway applicazione con altri componenti di Azure.AGIC version 0.8.0 and later can share a single Application Gateway with other Azure components. Ad esempio, è possibile usare lo stesso gateway applicazione per un'app ospitata in Virtual Machine Scale Set e un cluster AKS.

Eseguire __il backup della configurazione del gateway applicazione__ prima di abilitare questa impostazione:
  1. uso del [portale](https://portal.azure.com/) `Application Gateway` di Azure passare all'istanza
  2. dal `Export template` clic`Download`

Il file zip scaricato conterrà modelli JSON, bash e script di PowerShell che è possibile usare per ripristinare il gateway applicazione

### <a name="example-scenario"></a>Scenario di esempio
Diamo un'occhiata a un immaginario Application Gateway, che gestisce il traffico per due siti web:
  - `dev.contoso.com`- ospitato su un nuovo AKS, utilizzando Application Gateway e AGIC
  - `prod.contoso.com`- ospitato in un set di [scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/) di Azure

Con le impostazioni predefinite, AGIC assume il 100% della proprietà del gateway applicazione a cui fa riferimento. AGIC sovrascrive tutta la configurazione di App Gateway. Se dovessimo creare manualmente `prod.contoso.com` un listener per (nel gateway applicazione), senza definirlo in Kubernetes Ingress, AGIC eliminerà la `prod.contoso.com` configurazione in pochi secondi.

Per installare AGIC `prod.contoso.com` e servire anche dalle nostre macchine Virtual Machine Scale `dev.contoso.com` Set, dobbiamo vincolare AGIC solo alla configurazione. Ciò è facilitato dalla creazione di un'istanza del seguente [CRD:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Il comando precedente `AzureIngressProhibitedTarget` crea un oggetto. In questo modo AGIC (versione 0.8.0 e versioni successive) è a conoscenza dell'esistenza della configurazione del gateway applicazione per `prod.contoso.com` e lo indica in modo esplicito di evitare di modificare qualsiasi configurazione correlata a tale nome host.


### <a name="enable-with-new-agic-installation"></a>Abilita con nuova installazione AGIC
Per limitare AGIC (versione 0.8.0 e successive) a `helm-config.yaml` un sottoinsieme della configurazione del gateway applicazione modificare il modello.
Nella `appgw:` sezione, `shared` aggiungere chiave e `true`impostarla su .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Applicare le modifiche del timone:
  1. Assicurarsi `AzureIngressProhibitedTarget` che il CRD sia installato con:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Aggiorna Elmo:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Di conseguenza, l'AKS dirà una nuova istanza di `AzureIngressProhibitedTarget` called `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

L'oggetto `prohibit-all-targets`, come implica il nome, impedisce ad AGIC di modificare la configurazione per *qualsiasi* host e percorso.
L'installazione `appgw.shared=true` di Helm con distribuirà AGIC, ma non apporterà modifiche al gateway applicazione.


### <a name="broaden-permissions"></a>Ampie autorizzazioni
Poiché `appgw.shared=true` Helm con `prohibit-all-targets` e il valore predefinito impedisce ad AGIC di applicare qualsiasi configurazione.

Ampliare le autorizzazioni AGIC con:
1. Crea una `AzureIngressProhibitedTarget` nuova con la tua configurazione specifica:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Solo dopo aver creato il tuo divieto personalizzato, puoi eliminare quello predefinito, che è troppo ampio:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Abilitare l'opzione per un'installazione AGIC esistenteEnable for an existing AGIC installation
Si supponga di disporre già di un AKS funzionante, gateway applicazione e AGIC configurato nel cluster. Abbiamo un Ingress `prod.contosor.com` per e stiamo servendo con successo il traffico per esso da AKS. Si desidera `staging.contoso.com` aggiungere al gateway applicazione esistente, ma è necessario ospitarlo in una [macchina virtuale.](https://azure.microsoft.com/services/virtual-machines/) Verrà riutilizzato il gateway applicazione esistente e verrà configurato manualmente `staging.contoso.com`un listener e pool back-end per . Ma modificare manualmente la configurazione del gateway applicazione (tramite [portale,](https://portal.azure.com) [LE API ARM](https://docs.microsoft.com/rest/api/resources/) o [Terraform](https://www.terraform.io/)) sarebbe in conflitto con i presupposti di AGIC sulla piena proprietà. Poco dopo aver applicato le modifiche, AGIC le sovrascriverà o le eliminerà.

Possiamo impedire ad AGIC di apportare modifiche a un sottoinsieme di configurazione.

1. Creare `AzureIngressProhibitedTarget` un oggetto:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Visualizzare l'oggetto appena creato:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modificare la configurazione del gateway applicazione tramite il portale: aggiungere listener, regole di routing, back-end e così via. Il nuovo oggetto`manually-configured-staging-environment`creato ( ) impedirà ad AGIC `staging.contoso.com`di sovrascrivere la configurazione del gateway applicazione correlata a .
