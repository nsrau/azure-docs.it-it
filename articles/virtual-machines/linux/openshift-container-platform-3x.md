---
title: Distribuire OpenShift Container Platform 3.11 in AzureDeploy OpenShift Container Platform 3.11 in Azure
description: Distribuire OpenShift Container Platform 3.11 in Azure.Deploy OpenShift Container Platform 3.11 in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757890"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Distribuire OpenShift Container Platform 3.11 in AzureDeploy OpenShift Container Platform 3.11 in Azure

È possibile usare uno dei diversi metodi per distribuire OpenShift Container Platform 3.11 in Azure:You can use one of several methods to deploy OpenShift Container Platform 3.11 in Azure:

- È possibile distribuire manualmente i componenti dell'infrastruttura di Azure necessari e quindi seguire la documentazione di [OpenShift Container Platform](https://docs.openshift.com/container-platform).
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-container-platform/) esistente che semplifica la distribuzione del cluster OpenShift Container Platform.
- Un'altra opzione consiste nell'usare [l'offerta azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Per tutte le opzioni è necessaria una sottoscrizione di Red Hat. Durante la distribuzione, l'istanza Red Hat Enterprise Linux viene registrata nella sottoscrizione di Red Hat e allegata all'ID del pool che contiene i diritti per OpenShift Container Platform.
Verificare di avere un nome utente, una password e un ID pool validi per Red Hat Subscription Manager (RHSM). È possibile usare una chiave di attivazione, l'ID organizzazione e l'ID pool. Per verificare le informazioni, eseguire l'accesso a https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Eseguire la distribuzione usando il modello OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Cluster privati

La distribuzione di cluster OpenShift privati richiede molto di più che non avere semplicemente un indirizzo IP pubblico associato al servizio di bilanciamento del carico master (console Web) o al servizio di bilanciamento del carico infra (router).  Un cluster privato usa in genere un server DNS personalizzato (non il DNS di Azure predefinito), un nome di dominio personalizzato (ad esempio contoso.com) e una o più reti virtuali predefinite.  Per i cluster privati, è necessario configurare la rete virtuale con tutte le subnet e le impostazioni del server DNS appropriate in anticipo.  Utilizzare **quindi existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**e **existingNodeSubnetReference** per specificare la subnet esistente per l'utilizzo da parte del cluster.

Se si seleziona master privato (**masterClusterType**- privato), è necessario specificare un indirizzo IP privato statico per **masterPrivateClusterIp**.  Questo IP verrà assegnato al front-end del servizio di bilanciamento del carico principale.  L'IP deve essere all'interno del CIDR per la subnet master e non in uso.  **masterClusterDnsType** deve essere impostato su "custom" e il nome DNS master deve essere fornito per **masterClusterDns**.  Il nome DNS deve essere mappato all'IP privato statico e verrà utilizzato per accedere alla console nei nodi master.

Se si seleziona router privato (**routerClusterType**- privato), è necessario specificare un ip privato statico per **routerPrivateClusterIp**.  Questo IP verrà assegnato al front-end del servizio di bilanciamento del carico.  L'IP deve essere all'interno del CIDR per la subnet a infrarossi e non in uso.  **routingSubDomainType** deve essere impostato su "custom" e il nome DNS con caratteri jolly per il routing deve essere fornito per **routingSubDomain**.  

Se sono selezionati master privati e router privati, è necessario immettere anche il nome di dominio personalizzato per **domainName**

Dopo la corretta distribuzione, il nodo Bastion è l'unico nodo con un IP pubblico in cui è possibile eseguire lo ssh.  Anche se i nodi master sono configurati per l'accesso pubblico, non sono esposti per l'accesso ssh.

Per distribuire usando il modello di Resource Manager, viene usato un file di parametri per specificare tutti i parametri di input. Per personalizzare ulteriormente la distribuzione, creare una copia tramite fork dell'archivio GitHub e modificare gli elementi appropriati.

Tra le opzioni di personalizzazione comuni sono incluse, ad esempio:

- Dimensioni macchina virtuale bastion (variabile in azuredeploy.json)
- Convenzioni di denominazione (variabile in azuredeploy.json)
- Specifiche del cluster OpenShift - modificate usando il file hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurare il file dei parametri

Il [modello OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) dispone di più rami disponibili per diverse versioni di OpenShift Container Platform.  In base alle esigenze, è possibile distribuire direttamente dal repository oppure è possibile dividere il repository e apportare modifiche personalizzate ai modelli o agli script prima della distribuzione.

Usare il valore `appId` dall'entità servizio creata in precedenza per il parametro `aadClientId`.

L'esempio seguente mostra un file di parametri denominato azuredeploy.parameters.json con tutti gli input necessari.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Sostituire i parametri con le informazioni specifiche.

Versioni diverse possono avere parametri diversi, pertanto occorre verificare i parametri necessari per il ramo utilizzato.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Spiegazione del file Parameters.json

| Proprietà | Descrizione | Opzioni valide | Default Value |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL per gli elementi (json, script e così via) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Area di Azure in cui distribuire le risorseAzure region to deploy resources to |  |  |
| `masterVmSize` | Dimensioni della macchina virtuale master. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy.jsonSelect from one of the allowed VM sizes listed in the azuredeploy.json file |  | Standard_E2s_v3 |
| `infraVmSize` | Dimensioni della macchina virtuale Infra. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy.jsonSelect from one of the allowed VM sizes listed in the azuredeploy.json file |  | Standard_D4s_v3 |
| `nodeVmSize` | Dimensioni della macchina virtuale del nodo dell'app. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy.jsonSelect from one of the allowed VM sizes listed in the azuredeploy.json file |  | Standard_D4s_v3 |
| `cnsVmSize` | Dimensioni della macchina virtuale del nodo Container Native Storage (CNS). Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy.jsonSelect from one of the allowed VM sizes listed in the azuredeploy.json file |  | Standard_E4s_v3 |
| `osImageType` | Immagine RHEL da utilizzare. defaultgallery: On-Demand; marketplace: immagine di terze parti | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` è marketplace, immettere i valori appropriati per 'publisher', 'offer', 'sku', 'version' dell'offerta del marketplace. Questo parametro è un tipo di oggetto |  |  |
| `storageKind` | Il tipo di storage da utilizzare  | gestito<br> unmanaged | gestito |
| `openshiftClusterPrefix` | Prefisso cluster utilizzato per configurare i nomi host per tutti i nodi.  Tra 1 e 20 caratteri |  | mycluster |
| `minoVersion` | La versione secondaria di OpenShift Container Platform 3.11 da distribuire |  | 69 |
| `masterInstanceCount` | Numero di nodi Master da distribuire | 1, 3, 5 | 3 |
| `infraInstanceCount` | Numero di nodi con traframe da distribuire | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Numero di nodi da distribuire | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Numero di nodi CNS da distribuire | 3, 4 | 3 |
| `osDiskSize` | Dimensioni del disco del sistema operativo per la macchina virtuale (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Dimensioni del disco dati da collegare ai nodi per il volume Docker (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Dimensioni del disco dati da collegare ai nodi CNS per l'utilizzo da parte di glusterfs (in GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome utente amministratore sia per l'accesso al sistema operativo (VM) che per l'utente OpenShift iniziale |  | ocpadmin |
| `enableMetrics` | Abilitare le metriche. Le metriche richiedono più risorse, quindi selezionare le dimensioni corrette per Infra VMMetrics require more resources so select proper size for Infra VM | true <br> false | false |
| `enableLogging` | Abilitare la registrazione. il pod elasticsearch richiede 8 GB di RAM per selezionare le dimensioni corrette per Infra VM | true <br> false | false |
| `enableCNS` | Abilitare l'archiviazione nativa contenitoreEnable Container Native Storage | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat Subscription Manager Nome utente o ID organizzazione |  |  |
| `rhsmPoolId` | ID del pool di Red Hat Subscription Manager che contiene i permessi OpenShift per i nodi di calcolo |  |  |
| `rhsmBrokerPoolId` | ID pool di Red Hat Subscription Manager che contiene i diritti OpenShift per i master e i nodi infrarossi. Se non si dispone di ID pool diversi, immettere lo stesso ID pool di 'rhsmPoolId' |  |
| `sshPublicKey` | Copia la chiave pubblica SSH qui |  |  |
| `keyVaultSubscriptionId` | ID sottoscrizione della sottoscrizione che contiene l'insieme di credenziali delle chiavi |  |  |
| `keyVaultResourceGroup` | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi |  |  |
| `keyVaultName` | Il nome dell'insieme di credenziali delle chiavi creato |  |  |
| `enableAzure` | Abilitare il provider cloud di AzureEnable Azure Cloud Provider | true <br> false | true |
| `aadClientId` | Azure Active Directory Client ID also known as Application ID for Service Principal |  |  |
| `domainName` | Nome del nome di dominio personalizzato da utilizzare (se applicabile). Impostare su "none" se non si distribuisce un cluster completamente privato |  | none |
| `masterClusterDnsType` | Tipo di dominio per la console Web OpenShift. 'default' utilizzerà l'etichetta DNS dell'IP pubblico master infra. 'personalizzato' consente di definire il proprio nome | default <br> personalizzati | default |
| `masterClusterDns` | Il nome DNS personalizzato da utilizzare per accedere alla console Web OpenShift se è stata selezionata l'opzione "personalizzato" per`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se impostato su 'nipio', `routingSubDomain` userà nip.io.  Utilizzare 'custom' se si dispone di un proprio dominio che si desidera utilizzare per il routing | nipio <br> personalizzati | nipio |
| `routingSubDomain` | Il nome DNS con caratteri jolly che si desidera utilizzare per il routing se è stata selezionata l'opzione "personalizzato" per`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Scegliere se utilizzare una rete virtuale esistente o creare una nuova rete virtuale | esistenti <br> Nuovo | Nuovo |
| `virtualNetworkResourceGroupName` | Nome del gruppo di risorse per la nuova rete virtuale se è stato selezionato 'nuovo' per`virtualNetworkNewOrExisting` |  | resourceGroup().nome |
| `virtualNetworkName` | Il nome della nuova rete virtuale da creare se è stato selezionato "nuovo" per`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefisso indirizzo della nuova rete virtuale |  | 10.0.0.0/14 |
| `masterSubnetName` | Nome della subnet master |  | mastersubnet |
| `masterSubnetPrefix` | CIDR utilizzato per la subnet master - deve essere un sottoinsieme del addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Il nome della subnet infra |  | infrasubnet |
| `infraSubnetPrefix` | CIDR utilizzato per la subnet infra - deve essere un sottoinsieme del addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Nome della subnet del nodo |  | nodosubnet |
| `nodeSubnetPrefix` | CIDR utilizzato per la subnet del nodo - deve essere un sottoinsieme di addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Riferimento completo alla subnet esistente per i nodi master. Non necessario se si crea una nuova rete virtuale / subnet |  |  |
| `existingInfraSubnetReference` | Riferimento completo alla subnet esistente per i nodi infrano. Non necessario se si crea una nuova rete virtuale / subnet |  |  |
| `existingCnsSubnetReference` | Riferimento completo alla subnet esistente per i nodi CNS. Non necessario se si crea una nuova rete virtuale / subnet |  |  |
| `existingNodeSubnetReference` | Riferimento completo alla subnet esistente per i nodi di calcolo. Non necessario se si crea una nuova rete virtuale / subnet |  |  |
| `masterClusterType` | Specificare se il cluster utilizza nodi master privati o pubblici. Se si seleziona private, i nodi master non saranno esposti a Internet tramite un indirizzo IP pubblico. Al contrario, utilizzerà l'IP privato specificato nel`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Se vengono selezionati nodi master privati, è necessario specificare un indirizzo IP privato per l'utilizzo da parte del servizio di bilanciamento del carico interno per i nodi master. Questo IP statico deve trovarsi all'interno del blocco CIDR per la subnet master e non è già in uso. Se sono selezionati nodi master pubblici, questo valore non verrà utilizzato ma deve comunque essere specificato |  | 10.1.0.200 |
| `routerClusterType` | Specificare se il cluster utilizza nodi infradiprivati privati o pubblici. Se si seleziona private, i nodi infra non saranno esposti a Internet tramite un indirizzo IP pubblico. Al contrario, utilizzerà l'IP privato specificato nel`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | Se vengono selezionati infradi privati, è necessario specificare un indirizzo IP privato per l'utilizzo da parte del servizio di bilanciamento del carico interno per i nodi infra.If private infra nodes are selected, then a private IP address must be specified for use by the internal load balancer for infra nodes. Questo IP statico deve trovarsi all'interno del blocco CIDR per la subnet a infrarossi e non è già in uso. Se si selezionano infradi pubblici, questo valore non verrà utilizzato ma deve comunque essere specificato |  | 10.2.0.200 |
| `routingCertType` | Usare il certificato personalizzato per il routing del dominio o il certificato autofirmato predefinito- seguire le istruzioni nella sezione **Certificati personalizzatiUse custom certificate** for routing domain or the default self-signed certificate - follow instructions in Custom Certificates section | autofirmato <br> personalizzati | autofirmato |
| `masterCertType` | Usare il certificato personalizzato per il dominio master o il certificato autofirmato predefinito - seguire le istruzioni nella sezione **Certificati personalizzatiUse custom certificate** for master domain or the default self-signed certificate - follow instructions in Custom Certificates section | autofirmato <br> personalizzati | autofirmato |

<br>

### <a name="deploy-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

> [!NOTE] 
> Il comando seguente richiede l'interfaccia della riga di comando di Azure 2.0.8 o versione successiva. È possibile verificare la versione dell'interfaccia della riga di comando di Azure con il comando `az --version`. Per aggiornare la versione della CLI, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

L'esempio seguente consente di distribuire il cluster OpenShift e tutte le risorse correlate in un gruppo di risorse denominato openshiftrg, con nome di distribuzione myOpenShiftCluster. Viene fatto riferimento al modello direttamente dal repository GitHub e viene usato un file di parametri locale denominato azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Il completamento della distribuzione richiede almeno 60 minuti, in base al numero totale di nodi distribuiti e alle opzioni configurate. Al termine della distribuzione, il nome di dominio completo del DNS Bastion e l'URL della console di OpenShift vengono visualizzati sul terminale.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se non si vuole bloccare la riga di comando in attesa del completamento della distribuzione, aggiungere `--no-wait` come una delle opzioni per la distribuzione del gruppo. L'output dalla distribuzione può essere recuperato dal portale di Azure nella sezione distribuzione del gruppo di risorse.

## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console OpenShift con il browser utilizzando **l'URL della console OpenShift**. È inoltre possibile sSH all'host Bastion. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-container-platform-3x-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com) (Introduzione a OpenShift Container Platform)
