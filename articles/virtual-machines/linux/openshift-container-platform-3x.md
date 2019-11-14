---
title: Distribuire OpenShift container Platform 3,11 in Azure
description: Distribuire OpenShift container Platform 3,11 in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 56607de57939be769b1951f0eee9078c46d610c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035455"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Distribuire OpenShift container Platform 3,11 in Azure

È possibile usare uno dei diversi metodi per distribuire OpenShift container Platform 3,11 in Azure:

- È possibile distribuire manualmente i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione di OpenShift Container Platform](https://docs.openshift.com/container-platform).
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-container-platform/) esistente che semplifica la distribuzione del cluster OpenShift Container Platform.
- Un'altra opzione consiste nell'usare l'[offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Per tutte le opzioni è necessaria una sottoscrizione di Red Hat. Durante la distribuzione, l'istanza Red Hat Enterprise Linux viene registrata nella sottoscrizione di Red Hat e allegata all'ID del pool che contiene i diritti per OpenShift Container Platform.
Verificare di avere un nome utente, una password e un ID pool validi per Red Hat Subscription Manager (RHSM). È possibile usare una chiave di attivazione, l'ID organizzazione e l'ID pool. Per verificare le informazioni, eseguire l'accesso a https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Eseguire la distribuzione usando il modello OpenShift container Platform Gestione risorse 3,11

### <a name="private-clusters"></a>Cluster privati

Per la distribuzione di cluster OpenShift privati, non è necessario disporre di un indirizzo IP pubblico associato al servizio di bilanciamento del carico Master (console Web) o al servizio di bilanciamento del carico infra (router).  Un cluster privato USA in genere un server DNS personalizzato (non il DNS di Azure predefinito), un nome di dominio personalizzato (ad esempio contoso.com) e una o più reti virtuali predefinite.  Per i cluster privati, è necessario configurare in anticipo la rete virtuale con tutte le subnet appropriate e le impostazioni del server DNS.  Usare quindi **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**e **existingNodeSubnetReference** per specificare la subnet esistente per l'uso da parte del cluster.

Se è selezionata l'opzione Master privato (**masterClusterType**= private), è necessario specificare un indirizzo IP statico privato per **masterPrivateClusterIp**.  Questo indirizzo IP verrà assegnato al front-end del servizio di bilanciamento del carico principale.  L'indirizzo IP deve essere incluso nel CIDR per la subnet master e non in uso.  **masterClusterDnsType** deve essere impostato su "Custom" ed è necessario fornire il nome DNS master per **masterClusterDns**.  Il nome DNS deve essere mappato all'indirizzo IP privato statico e verrà usato per accedere alla console nei nodi master.

Se è selezionato un router privato (**routerClusterType**= private), è necessario specificare un indirizzo IP statico privato per **routerPrivateClusterIp**.  Questo indirizzo IP verrà assegnato al front-end del servizio di bilanciamento del carico infra.  L'indirizzo IP deve essere incluso nel CIDR per la subnet infra e non in uso.  **routingSubDomainType** deve essere impostato su "Custom" ed è necessario fornire il nome DNS con caratteri jolly per il routing per **routingSubDomain**.  

Se sono selezionati Master privati e router privato, è necessario immettere anche il nome di dominio personalizzato per **NomeDominio**

Al termine della distribuzione, il nodo Bastion è l'unico nodo con un indirizzo IP pubblico in cui è possibile connettersi tramite SSH.  Anche se i nodi master sono configurati per l'accesso pubblico, non sono esposti per l'accesso SSH.

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

### <a name="azuredeployparametersjson-file-explained"></a>file azuredeploy. Descrizione del file Parameters. JSON

| Proprietà | DESCRIZIONE | Opzioni valide | Valore predefinito |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL per gli artefatti (JSON, script e così via) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Area di Azure in cui distribuire le risorse |  |  |
| `masterVmSize` | Dimensioni della macchina virtuale master. Selezionare una delle dimensioni di macchina virtuale consentite elencate nel file file azuredeploy. JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Dimensioni della VM infra. Selezionare una delle dimensioni di macchina virtuale consentite elencate nel file file azuredeploy. JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Dimensioni della VM del nodo dell'app. Selezionare una delle dimensioni di macchina virtuale consentite elencate nel file file azuredeploy. JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Dimensioni della VM del nodo dello spazio di archiviazione nativo (CNS) del contenitore. Selezionare una delle dimensioni di macchina virtuale consentite elencate nel file file azuredeploy. JSON |  | Standard_E4s_v3 |
| `osImageType` | Immagine RHEL da usare. defaultgallery: su richiesta; Marketplace: immagine di terze parti | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` è Marketplace, immettere i valori appropriati per ' Publisher ',' offer ',' SKU ',' version ' dell'offerta Marketplace. Questo parametro è un tipo di oggetto |  |  |
| `storageKind` | Tipo di archiviazione da usare  | gestito<br> gestito | gestito |
| `openshiftClusterPrefix` | Prefisso del cluster usato per configurare i nomi host per tutti i nodi.  Da 1 a 20 caratteri |  | miocluster |
| `minoVersion` | Versione secondaria di OpenShift container Platform 3,11 da distribuire |  | 69 |
| `masterInstanceCount` | Numero di nodi master da distribuire | 1, 3, 5 | 3 |
| `infraInstanceCount` | Numero di nodi infra da distribuire | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Numero di nodi da distribuire | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Numero di nodi del SNC da distribuire | 3, 4 | 3 |
| `osDiskSize` | Dimensioni del disco del sistema operativo per la macchina virtuale (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Dimensioni del disco dati da collegare ai nodi per il volume Docker (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Dimensioni del disco dati per il collegamento ai nodi dello SNC per l'uso da GlusterFS (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome utente amministratore per l'accesso sia al sistema operativo (VM) che all'utente OpenShift iniziale |  | ocpadmin |
| `enableMetrics` | Abilitare le metriche. Le metriche richiedono più risorse, quindi selezionare le dimensioni appropriate per la macchina virtuale infra | true <br> false | false |
| `enableLogging` | Abilitare la registrazione. il Pod elasticsearch richiede 8 GB di RAM, quindi selezionare le dimensioni appropriate per la macchina virtuale infra | true <br> false | false |
| `enableCNS` | Abilitare l'archiviazione nativa del contenitore | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nome utente o ID organizzazione di Red Hat Subscription Manager |  |  |
| `rhsmPoolId` | ID del pool di gestione delle sottoscrizioni di Red Hat che contiene i diritti OpenShift per i nodi di calcolo |  |  |
| `rhsmBrokerPoolId` | ID del pool di gestione delle sottoscrizioni di Red Hat che contiene i diritti OpenShift per i nodi master e infra. Se non si hanno ID pool diversi, immettere lo stesso ID pool di ' rhsmPoolId ' |  |
| `sshPublicKey` | Copiare qui la chiave pubblica SSH |  |  |
| `keyVaultSubscriptionId` | ID sottoscrizione della sottoscrizione che contiene il Key Vault |  |  |
| `keyVaultResourceGroup` | Nome del gruppo di risorse che contiene il Key Vault |  |  |
| `keyVaultName` | Nome del Key Vault creato |  |  |
| `enableAzure` | Abilita provider di servizi cloud di Azure | true <br> false | true |
| `aadClientId` | ID client di Azure Active Directory noto anche come ID applicazione per l'entità servizio |  |  |
| `domainName` | Nome del nome di dominio personalizzato da usare, se applicabile. Impostato su "None" se non si distribuisce un cluster completamente privato |  | nessuno |
| `masterClusterDnsType` | Tipo di dominio per la console Web OpenShift. con ' default ' viene utilizzata l'etichetta DNS dell'indirizzo IP pubblico del Master infra. ' Custom ' consente di definire un nome personalizzato | default <br> personalizzato | default |
| `masterClusterDns` | Nome DNS personalizzato da usare per accedere alla console Web di OpenShift se è stata selezionata l'opzione ' Custom ' per `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se impostato su' nipio ', `routingSubDomain` utilizzerà nip.io.  Usare "Custom" Se si dispone di un dominio personalizzato che si vuole usare per il routing | nipio <br> personalizzato | nipio |
| `routingSubDomain` | Nome DNS con caratteri jolly che si vuole usare per il routing se è stato selezionato "Custom" per `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Scegliere se usare una rete virtuale esistente o creare una nuova rete virtuale | esistente <br> nuovo | nuovo |
| `virtualNetworkResourceGroupName` | Nome del gruppo di risorse per la nuova rete virtuale se è stata selezionata l'opzione ' nuovo ' per `virtualNetworkNewOrExisting` |  | resourceGroup (). nome |
| `virtualNetworkName` | Nome della nuova rete virtuale da creare se è stato selezionato "nuovo" per `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefisso dell'indirizzo della nuova rete virtuale |  | 10.0.0.0/14 |
| `masterSubnetName` | Nome della subnet master |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usato per la subnet master. deve essere un subset del addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Nome della subnet infra |  | infrasubnet |
| `infraSubnetPrefix` | CIDR usato per la subnet infra. deve essere un subset del addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Nome della subnet del nodo |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR usato per la subnet del nodo. deve essere un subset del addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Riferimento completo alla subnet esistente per i nodi master. Non necessario se si crea una nuova vNet/subnet |  |  |
| `existingInfraSubnetReference` | Riferimento completo alla subnet esistente per i nodi infra. Non necessario se si crea una nuova vNet/subnet |  |  |
| `existingCnsSubnetReference` | Riferimento completo alla subnet esistente per i nodi del sistema nervoso. Non necessario se si crea una nuova vNet/subnet |  |  |
| `existingNodeSubnetReference` | Riferimento completo alla subnet esistente per i nodi di calcolo. Non necessario se si crea una nuova vNet/subnet |  |  |
| `masterClusterType` | Specificare se il cluster usa nodi master privati o pubblici. Se si sceglie private, i nodi master non verranno esposti a Internet tramite un indirizzo IP pubblico. Utilizzerà invece l'indirizzo IP privato specificato nella `masterPrivateClusterIp` | public <br> Privato | public |
| `masterPrivateClusterIp` | Se si selezionano i nodi master privati, è necessario specificare un indirizzo IP privato per l'uso da parte del servizio di bilanciamento del carico interno per i nodi master. Questo indirizzo IP statico deve trovarsi all'interno del blocco CIDR per la subnet master e non è già in uso. Se si selezionano i nodi Master pubblici, questo valore non verrà utilizzato, ma sarà comunque necessario specificarlo |  | 10.1.0.200 |
| `routerClusterType` | Specificare se il cluster usa i nodi infra privati o pubblici. Se si sceglie private, i nodi infra non verranno esposti a Internet tramite un indirizzo IP pubblico. Utilizzerà invece l'indirizzo IP privato specificato nella `routerPrivateClusterIp` | public <br> Privato | public |
| `routerPrivateClusterIp` | Se si selezionano i nodi infra privati, è necessario specificare un indirizzo IP privato per l'uso da parte del servizio di bilanciamento del carico interno per i nodi infra. Questo indirizzo IP statico deve trovarsi all'interno del blocco CIDR per la subnet master e non è già in uso. Se si selezionano i nodi infra pubblici, questo valore non verrà utilizzato, ma sarà comunque necessario specificarlo |  | 10.2.0.200 |
| `routingCertType` | Usa certificato personalizzato per il dominio di routing o il certificato autofirmato predefinito-seguire le istruzioni nella sezione **certificati personalizzati** | selfsigned <br> personalizzato | selfsigned |
| `masterCertType` | Usare un certificato personalizzato per il dominio master o il certificato autofirmato predefinito-seguire le istruzioni nella sezione **certificati personalizzati** | selfsigned <br> personalizzato | selfsigned |

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

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console di OpenShift con il browser usando l' **URL della console di OpenShift**. è anche possibile usare SSH per l'host Bastion. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
