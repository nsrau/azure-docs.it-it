---
title: Creare un controller di ingresso con un gateway applicazione esistente
description: Questo articolo fornisce informazioni su come distribuire un controller di ingresso del gateway applicazione con un gateway applicazione esistente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: dec43a4d7eb5a9546fcd77cce972b93542ea3b10
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795947"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Installare un controller di ingresso del gateway applicazione (AGIC) usando un gateway applicazione esistente

Il controller di ingresso del gateway applicazione (AGIC) è un pod nel cluster Kubernetes.
AGIC monitora le risorse di [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/) Kubernetes e crea e applica la configurazione del gateway applicazione in base allo stato del cluster Kubernetes.

## <a name="outline"></a>Struttura
- [Prerequisiti](#prerequisites)
- [Autenticazione Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Opzione 1: [configurare AAD-Pod-Identity](#set-up-aad-pod-identity) e creare un'identità di Azure per le armi
    - Opzione 2: [uso di un'entità servizio](#using-a-service-principal)
- [Installare il controller di ingresso con Helm](#install-ingress-controller-as-a-helm-chart)
- [Gateway applicazione multicluster/condiviso](#multi-cluster--shared-application-gateway): installare AGIC in un ambiente in cui il gateway applicazione è condiviso tra uno o più cluster AKS e/o altri componenti di Azure.

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone che siano già installati gli strumenti e l'infrastruttura seguenti:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) con [rete avanzata](https://docs.microsoft.com/azure/aks/configure-azure-cni) abilitata
- [Gateway applicazione V2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) nella stessa rete virtuale di AKS
- [Identità Pod AAD](https://github.com/Azure/aad-pod-identity) installata nel cluster AKS
- [Cloud Shell](https://shell.azure.com/) è l'ambiente di Azure Shell, che include `az` CLI, `kubectl`e `helm` installati. Questi strumenti sono necessari per i comandi seguenti.

__Eseguire il backup della configurazione del gateway applicazione prima di__ installare AGIC:
  1. uso di [portale di Azure](https://portal.azure.com/) passare all'istanza di `Application Gateway`
  2. da `Export template` fare clic su `Download`

Il file zip scaricato avrà i modelli JSON, bash e gli script di PowerShell che è possibile usare per ripristinare il gateway app che diventa necessario

## <a name="install-helm"></a>Installare Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) è una gestione pacchetti per Kubernetes. Verrà usato per installare il pacchetto di `application-gateway-kubernetes-ingress`.
Usare [cloud Shell](https://shell.azure.com/) per installare Helm:

1. Installare [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) ed eseguire il comando seguente per aggiungere `application-gateway-kubernetes-ingress` pacchetto Helm:

    - *RBAC abilitato* Cluster AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC disabilitato* Cluster AKS

    ```bash
    helm init
    ```

1. Aggiungere il repository Helm di AGIC:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Autenticazione Azure Resource Manager

AGIC comunica con il server API Kubernetes e il Azure Resource Manager. Per accedere a queste API, è necessaria un'identità.

## <a name="set-up-aad-pod-identity"></a>Configurare l'identità di AAD Pod

L' [identità del Pod AAD](https://github.com/Azure/aad-pod-identity) è un controller, simile a AGIC, che viene eseguito anche nel servizio contenitore di Azure. Associa Azure Active Directory identità ai pod Kubernetes. L'identità è necessaria per consentire a un'applicazione in un pod Kubernetes di comunicare con altri componenti di Azure. Nel caso specifico, è necessario disporre dell'autorizzazione per il Pod AGIC per eseguire richieste HTTP a [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Seguire le [istruzioni di installazione di AAD Pod Identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) per aggiungere questo componente al AKS.

A questo punto è necessario creare un'identità di Azure e assegnarle le autorizzazioni ARM.
Usare [cloud Shell](https://shell.azure.com/) per eseguire tutti i comandi seguenti e creare un'identità:

1. Creare un'identità **di Azure nello stesso gruppo di risorse dei nodi AKS**. La selezione del gruppo di risorse corretto è importante. Il gruppo di risorse richiesto nel comando seguente *non* è quello a cui si fa riferimento nel riquadro del portale AKS. Questo è il gruppo di risorse delle macchine virtuali `aks-agentpool`. In genere, il gruppo di risorse inizia con `MC_` e contiene il nome dell'AKS. Ad esempio: `MC_resourceGroup_aksABCD_westus`

    ```bash
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Per i comandi di assegnazione di ruolo seguenti è necessario ottenere `principalId` per l'identità appena creata:

    ```bash
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Fornire l'identità `Contributor` l'accesso al gateway applicazione. A tale proposito, è necessario l'ID del gateway applicazione, che sarà simile al seguente: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Ottenere l'elenco degli ID del gateway applicazione nella sottoscrizione con: `az network application-gateway list --query '[].id'`

    ```bash
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Assegnare all'identità `Reader` l'accesso al gruppo di risorse del gateway applicazione. L'ID del gruppo di risorse sarà simile al seguente: `/subscriptions/A/resourceGroups/B`. È possibile ottenere tutti i gruppi di risorse con: `az group list --query '[].id'`

    ```bash
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Uso di un'entità servizio
È anche possibile fornire l'accesso AGIC a ARM tramite un segreto Kubernetes.

1. Creare un'entità servizio Active Directory e codificarla con Base64. Per salvare il BLOB JSON in Kubernetes, è necessaria la codifica Base64.

```bash
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Aggiungere il BLOB JSON con codifica base64 al file di `helm-config.yaml`. Altre informazioni su `helm-config.yaml` sono riportate nella sezione successiva.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Installare il controller di ingresso come grafico Helm
Nei primi passaggi viene installata la barra di comando di Helm nel cluster Kubernetes. Usare [cloud Shell](https://shell.azure.com/) per installare il pacchetto AGIC Helm:

1. Aggiungere il repository Helm `application-gateway-kubernetes-ingress` ed eseguire un aggiornamento Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. Scaricare Helm-config. YAML, che configurerà AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    In alternativa, copiare il file YAML seguente: 
    
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

1. Modificare Helm-config. YAML e inserire i valori per `appgw` e `armAuth`.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > Le `<identity-resource-id>` e `<identity-client-id>` sono le proprietà dell'identità Azure AD che è stata impostata nella sezione precedente. È possibile recuperare queste informazioni eseguendo il comando seguente: `az identity show -g <resourcegroup> -n <identity-name>`, dove `<resourcegroup>` è il gruppo di risorse in cui vengono distribuiti l'oggetto cluster AKS di primo livello, il gateway applicazione e l'identificazione gestita.

1. Installare Helm Chart `application-gateway-kubernetes-ingress` con la configurazione `helm-config.yaml` dal passaggio precedente

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    In alternativa, è possibile combinare il `helm-config.yaml` e il comando Helm in un unico passaggio:
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
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Controllare il log del Pod appena creato per verificare se è stato avviato correttamente

Per informazioni su come esporre un servizio AKS su HTTP o HTTPS a Internet usando un gateway applicazione Azure, fare riferimento a [questa guida](ingress-controller-expose-service-over-http-https.md) .



## <a name="multi-cluster--shared-application-gateway"></a>Gateway applicazione multicluster/condiviso
Per impostazione predefinita, AGIC presuppone la proprietà completa del gateway applicazione a cui è collegato. AGIC versione 0.8.0 e versioni successive possono condividere un singolo gateway applicazione con altri componenti di Azure. Ad esempio, è possibile usare lo stesso gateway applicazione per un'app ospitata in un set di scalabilità di macchine virtuali e un cluster AKS.

Per abilitare questa impostazione, __eseguire il backup della configurazione del gateway applicazione__ :
  1. uso di [portale di Azure](https://portal.azure.com/) passare all'istanza di `Application Gateway`
  2. da `Export template` fare clic su `Download`

Il file zip scaricato avrà modelli JSON, bash e script PowerShell che è possibile usare per ripristinare il gateway applicazione

### <a name="example-scenario"></a>Scenario di esempio
Viene ora esaminato un gateway applicazione immaginaria, che gestisce il traffico per due siti Web:
  - `dev.contoso.com` ospitato su un nuovo AKS, usando il gateway applicazione e AGIC
  - `prod.contoso.com` ospitato in un [set di scalabilità di macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Con le impostazioni predefinite, AGIC presuppone il 100% della proprietà del gateway applicazione a cui fa riferimento. AGIC sovrascrive tutta la configurazione del gateway applicazione. Se dovessimo creare manualmente un listener per `prod.contoso.com` (sul gateway applicazione), senza definirlo nell'ingresso Kubernetes, AGIC eliminerà la configurazione `prod.contoso.com` in pochi secondi.

Per installare AGIC e gestire `prod.contoso.com` dai computer del set di scalabilità di macchine virtuali, è necessario vincolare AGIC solo alla configurazione di `dev.contoso.com`. Questa operazione è facilitata dalla creazione di un'istanza del [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)seguente:

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

Il comando precedente crea un oggetto `AzureIngressProhibitedTarget`. In questo modo, AGIC (Version 0.8.0 e versioni successive) è in grado di riconoscere l'esistenza della configurazione del gateway applicazione per `prod.contoso.com` e indica in modo esplicito di evitare la modifica di qualsiasi configurazione relativa a tale nome host.


### <a name="enable-with-new-agic-installation"></a>Abilita con nuova installazione di AGIC
Per limitare AGIC (Version 0.8.0 e versioni successive) a un subset della configurazione del gateway applicazione, modificare il modello di `helm-config.yaml`.
Nella sezione `appgw:` aggiungere `shared` chiave e impostarla su `true`.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Applicare le modifiche Helm:
  1. Verificare che il `AzureIngressProhibitedTarget` CRD sia installato con:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Aggiornamento Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Di conseguenza, il servizio AKS avrà una nuova istanza di `AzureIngressProhibitedTarget` denominata `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

L'oggetto `prohibit-all-targets`, come suggerisce il nome, impedisce a AGIC di modificare la configurazione per *qualsiasi* host e percorso.
Helm install with `appgw.shared=true` distribuirà AGIC, ma non apporterà alcuna modifica al gateway applicazione.


### <a name="broaden-permissions"></a>Ampliare le autorizzazioni
Poiché Helm con `appgw.shared=true` e il `prohibit-all-targets` predefinito impedisce a AGIC di applicare qualsiasi configurazione.

Ampliare le autorizzazioni di AGIC con:
1. Creare una nuova `AzureIngressProhibitedTarget` con la configurazione specifica:
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

2. Solo dopo aver creato un divieto personalizzato, è possibile eliminare quello predefinito, troppo ampio:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Abilita per un'installazione di AGIC esistente
Si supponga di avere già un AKS funzionante, un gateway applicazione e un AGIC configurato nel cluster. Si dispone di un ingresso per la `prod.contosor.com` e il traffico viene eseguito correttamente da AKS. Si vuole aggiungere `staging.contoso.com` al gateway applicazione esistente, ma è necessario ospitarlo in una [macchina virtuale](https://azure.microsoft.com/services/virtual-machines/). Verrà riutilizzato il gateway applicazione esistente e verranno configurati manualmente un listener e i pool back-end per `staging.contoso.com`. Tuttavia, la modifica manuale della configurazione del gateway applicazione (tramite il [portale](https://portal.azure.com), le [API ARM](https://docs.microsoft.com/rest/api/resources/) o la [bonifica](https://www.terraform.io/)) potrebbe essere in conflitto con i presupposti di AGIC di proprietà completa. Subito dopo l'applicazione delle modifiche, AGIC li sovrascriverà o li eliminerà.

È possibile impedire a AGIC di apportare modifiche a un subset di configurazione.

1. Creare un oggetto `AzureIngressProhibitedTarget`:
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

3. Modificare la configurazione del gateway applicazione tramite il portale: aggiungere listener, regole di routing, backend e così via. Il nuovo oggetto creato (`manually-configured-staging-environment`) impedirà a AGIC di sovrascrivere la configurazione del gateway applicazione correlata al `staging.contoso.com`.
