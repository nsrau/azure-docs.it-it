---
title: Distribuire OpenShift Container Platform in Azure | Microsoft Docs
description: Distribuire OpenShift Container Platform in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 664099322bef3ac85d980fbe5e43dcc49cba862b
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411569"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuire OpenShift Container Platform in Azure

Esistono diversi modi di distribuire OpenShift Container Platform in Azure:

- È possibile distribuire manualmente i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione di OpenShift Container Platform](https://docs.openshift.com/container-platform).
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-container-platform/) esistente che semplifica la distribuzione del cluster OpenShift Container Platform.
- Un'altra opzione consiste nell'usare l'[offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Per tutte le opzioni è necessaria una sottoscrizione di Red Hat. Durante la distribuzione, l'istanza Red Hat Enterprise Linux viene registrata nella sottoscrizione di Red Hat e allegata all'ID del pool che contiene i diritti per OpenShift Container Platform.
Verificare di avere un nome utente, una password e un ID pool validi per Red Hat Subscription Manager (RHSM). È possibile usare una chiave di attivazione, l'ID organizzazione e l'ID pool. Per verificare le informazioni, eseguire l'accesso a https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Distribuire usando il modello di Resource Manager di OpenShift Container Platform

### <a name="private-clusters"></a>Cluster privato

La distribuzione privata dei cluster OpenShift è necessario disporre di più di ma non un IP pubblico associato al bilanciamento del carico master (console web) o alle infra bilanciamento del carico (router).  Un cluster privato Usa in genere un server DNS personalizzato (non predefinita DNS di Azure), un nome di dominio personalizzato (ad esempio, contoso.com) e le reti virtuali predefinite.  Per i cluster privati, è necessario configurare in anticipo la rete virtuale con tutte le subnet appropriate e impostazioni del server DNS.  Quindi usare **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, e  **existingNodeSubnetReference** per specificare la subnet esistente per l'uso da parte del cluster.

Se è selezionata l'opzione master privato (**masterClusterType**= privato), deve essere specificata per un indirizzo IP privato statico **masterPrivateClusterIp**.  Questo indirizzo IP verrà assegnato per il front-end del bilanciamento del carico master.  L'indirizzo IP deve essere compreso il CIDR per la subnet del master e non è in uso.  **masterClusterDnsType** deve essere impostata su "custom" e il master nome DNS deve essere prevista **masterClusterDns**.  Il nome DNS deve eseguire il mapping per l'IP privato statico e verrà usato per accedere alla console in nodi master.

Se è selezionata private router (**routerClusterType**= privato), deve essere specificata per un indirizzo IP privato statico **routerPrivateClusterIp**.  Questo indirizzo IP verrà assegnato per il front-end del bilanciamento del carico di infrastruttura.  L'indirizzo IP deve essere all'interno del CIDR per le infrastrutture-subnet e non in uso.  **routingSubDomainType** deve essere impostata su "custom" e il nome DNS con caratteri jolly per il routing deve essere disponibile **routingSubDomain**.  

Se master privato e private router sono selezionate, il nome di dominio personalizzati deve essere inserito anche per **domainName**

Al termine della distribuzione, il nodo Bastion è l'unico nodo con un IP pubblico che è possibile usare ssh in.  Anche se i nodi master sono configurati per l'accesso pubblico, non sono esposti per ssh accesso.

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

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Descrizione del file Parameters. JSON

| Proprietà | Descrizione | Opzioni valide | Default Value |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL per elementi (json, script e così via) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Area di Azure per distribuire le risorse di |  |  |
| `masterVmSize` | Dimensioni della macchina virtuale Master. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy. JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Dimensioni dell'infrastruttura della macchina virtuale. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy. JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Dimensioni della macchina virtuale del nodo App. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy. JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Dimensioni del nodo contenitore per l'archiviazione nativa (CNS) della macchina virtuale. Selezionare una delle dimensioni di VM consentite elencate nel file azuredeploy. JSON |  | Standard_E4s_v3 |
| `osImageType` | L'immagine RHEL da usare. defaultgallery: On Demand; Marketplace: immagine di terze parti | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` è marketplace, quindi immettere i valori appropriati per 'publisher', 'offrono', 'sku', 'version' dell'offerta del marketplace. Questo parametro è un tipo di oggetto |  |  |
| `storageKind` | Il tipo di archiviazione da usare  | gestito<br> non gestito | gestito |
| `openshiftClusterPrefix` | Prefisso usato per configurare i nomi host per tutti i nodi del cluster.  Tra 1 e 20 caratteri |  | MyCluster |
| `minoVersion` | La versione secondaria della 3.11 di piattaforma contenitore OpenShift per la distribuzione |  | 69 |
| `masterInstanceCount` | Numero di nodi di schemi da distribuire | 1, 3, 5 | 3 |
| `infraInstanceCount` | Numero di infrastrutture-nodi da distribuire | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Numero di nodi da distribuire | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Numero di nodi di nomi comuni per la distribuzione | 3, 4 | 3 |
| `osDiskSize` | Dimensioni del disco del sistema operativo della macchina virtuale (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Dimensioni del disco dati da collegare ai nodi per il volume di Docker (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Dimensione del disco dati da associare ai nodi di nomi comuni per l'uso mediante glusterfs (in GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome utente amministratore per account di accesso del sistema operativo (VM) e l'utente iniziale di OpenShift |  | ocpadmin |
| `enableMetrics` | Abilitare le metriche. Le metriche richiedono che più risorse, quindi, scegliere dimensioni appropriate per infrastrutture-macchina virtuale | true <br> false | false |
| `enableLogging` | Abilitare la registrazione. elasticsearch pod richiede 8 GB di RAM se si desidera selezionare dimensioni appropriate per la macchina virtuale all'infrastruttura | true <br> false | false |
| `enableCNS` | Abilitare l'archiviazione nativa contenitore | true <br> false | false |
| `rhsmUsernameOrOrgId` | ID Red Hat Subscription Manager Username o dell'organizzazione |  |  |
| `rhsmPoolId` | il Pool ID di Red Hat Subscription Manager che contiene i diritti di OpenShift per i nodi di calcolo |  |  |
| `rhsmBrokerPoolId` | Red Hat Subscription Manager Pool di ID contiene i diritti di OpenShift per gli schemi e le infra nodi. Se non si dispone di diversi pool di ID, immettere lo stesso ID del pool 'rhsmPoolId' |  |
| `sshPublicKey` | Copiare la chiave pubblica SSH qui |  |  |
| `keyVaultSubscriptionId` | L'ID sottoscrizione della sottoscrizione che contiene l'insieme di credenziali chiave |  |  |
| `keyVaultResourceGroup` | Il nome del gruppo di risorse che contiene l'insieme di credenziali chiave |  |  |
| `keyVaultName` | Il nome dell'insieme di credenziali chiave è stato creato |  |  |
| `enableAzure` | Abilitare il Provider di Cloud di Azure | true <br> false | true |
| `aadClientId` | ID Azure Active Directory Client nota anche come ID dell'applicazione per l'entità servizio |  |  |
| `domainName` | Nome del nome di dominio personalizzato da utilizzare (se applicabile). Impostare su "none" in caso contrario la distribuzione cluster completamente privato |  | Nessuno |
| `masterClusterDnsType` | Tipo di dominio per la console web di OpenShift. 'default' userà l'etichetta DNS del master infrastrutture-indirizzo IP pubblico. 'custom' consente di definire il proprio nome | predefinito <br> personalizzato | predefinito |
| `masterClusterDns` | Il nome DNS personalizzato da usare per accedere alla console web di OpenShift se si seleziona 'personalizzata' per `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se impostato su 'nipio', `routingSubDomain` userà nip.io.  Usare 'custom' Se si ha il proprio dominio che si desidera utilizzare per il routing | nipio <br> personalizzato | nipio |
| `routingSubDomain` | Il nome DNS jolly per che si desidera utilizzare per il routing se si seleziona 'personalizzata' `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selezionare questa opzione per usare una rete virtuale esistente o crearne una nuova rete virtuale | Esistente <br> nuova | nuova |
| `virtualNetworkResourceGroupName` | Nome del gruppo di risorse per la nuova rete virtuale se si seleziona 'new' per `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | Il nome della nuova rete virtuale per creare se si seleziona 'new' per `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefisso degli indirizzi della nuova rete virtuale |  | 10.0.0.0/14 |
| `masterSubnetName` | Il nome della subnet master |  | mastersubnet |
| `masterSubnetPrefix` | Utilizzato per la subnet master - CIDR deve essere un subset del prefisso degli indirizzi |  | 10.1.0.0/16 |
| `infraSubnetName` | Il nome dell'infrastruttura subnet |  | infrasubnet |
| `infraSubnetPrefix` | CIDR usati per l'infrastruttura deve essere un subset del prefisso degli indirizzi subnet: |  | 10.2.0.0/16 |
| `nodeSubnetName` | Il nome della subnet nodo |  | nodesubnet |
| `nodeSubnetPrefix` | Deve essere un subset del prefisso degli indirizzi CIDR usati per la subnet del nodo: |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Informazioni di riferimento complete per la subnet esistente per i nodi master. Non è necessaria se la creazione della nuova rete virtuale / Subnet |  |  |
| `existingInfraSubnetReference` | Riferimento completo a subnet esistente per infrastrutture-nodi. Non è necessaria se la creazione della nuova rete virtuale / Subnet |  |  |
| `existingCnsSubnetReference` | Informazioni di riferimento complete per la subnet esistente per i nodi di nomi comuni. Non è necessaria se la creazione della nuova rete virtuale / Subnet |  |  |
| `existingNodeSubnetReference` | Informazioni di riferimento complete per la subnet esistente per i nodi di calcolo. Non è necessaria se la creazione della nuova rete virtuale / Subnet |  |  |
| `masterClusterType` | Specificare se il cluster Usa nodi master pubblici o privati. Se si sceglie privato, i nodi master non siano esposti a Internet tramite un indirizzo IP pubblico. Al contrario, userà l'indirizzo IP privato, specificato di `masterPrivateClusterIp` | pubblico <br> privato | pubblico |
| `masterPrivateClusterIp` | Se si selezionano nodi master privati, un indirizzo IP privato deve essere specificato per l'utilizzo dal servizio di bilanciamento del carico interno per i nodi master. Questo indirizzo IP statico deve essere all'interno del blocco CIDR per la subnet del master e non è già in uso. Se si selezionano nodi master pubblici, questo valore non verrà usato, ma è necessario comunque specificare |  | 10.1.0.200 |
| `routerClusterType` | Specificare se il cluster Usa pubblico o privato infra nodi. Se si sceglie privato, l'infrastruttura nodi non siano esposti a Internet tramite un indirizzo IP pubblico. Al contrario, userà l'indirizzo IP privato, specificato di `routerPrivateClusterIp` | pubblico <br> privato | pubblico |
| `routerPrivateClusterIp` | Se privata infrastruttura vengono selezionati i nodi, quindi specificare un indirizzo IP privato per usare dal servizio di bilanciamento del carico interno per infrastrutture-nodi. Questo indirizzo IP statico deve essere all'interno del blocco CIDR per la subnet del master e non è già in uso. Se pubblici infra vengono selezionati i nodi, questo valore non verrà usato, ma è necessario comunque specificare |  | 10.2.0.200 |
| `routingCertType` | Usa certificato personalizzato per il dominio di routing o il certificato autofirmato predefinito - seguire le istruzioni **certificati personalizzati** sezione | selfsigned <br> personalizzato | selfsigned |
| `masterCertType` | Usa certificato personalizzato per il certificato autofirmato predefinito o un dominio del master - seguire le istruzioni **certificati personalizzati** sezione | selfsigned <br> personalizzato | selfsigned |

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

La distribuzione richiede almeno 60 minuti, in base al numero totale di nodi distribuiti e opzioni configurate. Al termine della distribuzione, il nome di dominio completo del DNS Bastion e l'URL della console di OpenShift vengono visualizzati sul terminale.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se non si vuole bloccare la riga di comando in attesa del completamento della distribuzione, aggiungere `--no-wait` come una delle opzioni per la distribuzione del gruppo. L'output dalla distribuzione può essere recuperato dal portale di Azure nella sezione distribuzione del gruppo di risorse.

## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console di OpenShift con il browser usando il **URL della Console OpenShift**. È anche possibile SSH al Bastion host. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com/container-platform) (Introduzione a OpenShift Container Platform)
