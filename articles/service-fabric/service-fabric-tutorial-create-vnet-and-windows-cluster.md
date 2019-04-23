---
title: Creare un cluster di Service Fabric che esegue Windows in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come usare PowerShell per distribuire un cluster di Service Fabric che segue Windows in una rete virtuale di Azure e in un gruppo di sicurezza di rete.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: dabbefa8ca2073e30948f1c70782f730bceae030
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050007"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Esercitazione: Distribuire un cluster di Service Fabric che esegue Windows in una rete virtuale di Azure

Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster di Service Fabric di Azure che esegue Windows in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) e in un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) usando PowerShell e un modello. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni. Per creare un cluster Linux usando l'interfaccia della riga di comando di Azure, vedere l'articolo su come [creare un cluster Linux protetto in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Questa esercitazione descrive uno scenario di produzione. Se si vuole creare un cluster di piccole dimensioni a scopo di test, vedere [Creare un cluster di Service Fabric](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando PowerShell
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Configurare l'autenticazione di Azure Active Directory
> * Configurare la raccolta di diagnostica
> * Configurare il servizio EventStore
> * Configurare i log di Monitoraggio di Azure
> * Creare un cluster sicuro di Service Fabric in Azure PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster sicuro in Azure
> * [Eseguire il monitoraggio di un cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminare un cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md).
* Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Esaminare i concetti chiave dei [cluster di Azure](service-fabric-azure-clusters-overview.md).
* [Pianificare e preparare](service-fabric-cluster-azure-deployment-preparation.md) la distribuzione di un cluster di produzione.

Le procedure seguenti creano un cluster di Service Fabric a sette nodi. Usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per calcolare i costi legati all'esecuzione di un cluster di Service Fabric in Azure.

## <a name="download-and-explore-the-template"></a>Scaricare ed esplorare il modello

Scaricare i file del modello di Azure Resource Manager seguenti:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Questo modello distribuisce un cluster sicuro di sette macchine virtuali e tre tipi di nodi in una rete virtuale e un gruppo di sicurezza di rete.  Altri modelli di esempio sono disponibili su [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [azuredeploy.json][template] distribuisce alcune risorse, incluse le seguenti.

### <a name="service-fabric-cluster"></a>Cluster di Service Fabric

Nella risorsa **Microsoft.ServiceFabric/clusters** viene configurato un cluster Windows con le caratteristiche seguenti:

* Tre tipi di nodi.
* Cinque nodi del tipo di nodo primario (configurabile nei parametri del modello) e un nodo di ognuno degli altri due tipi di nodi.
* Sistema operativo: Windows Server 2016 Datacenter con contenitori (configurabile nei parametri del modello).
* Protezione con certificato (configurabile nei parametri del modello).
* [Proxy inverso](service-fabric-reverseproxy.md) abilitato.
* [Servizio DNS](service-fabric-dnsservice.md) abilitato.
* [Livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronzo (configurabile nei parametri del modello).
* [Livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Argento (configurabile nei parametri del modello).
* Endpoint di connessione client: 19000 (configurabile nei parametri del modello).
* Endpoint del gateway HTTP: 19080 (configurabile nei parametri del modello).

### <a name="azure-load-balancer"></a>Azure Load Balancer

Nella risorsa **Microsoft.Network/loadBalancers** viene configurato un servizio di bilanciamento del carico. Vengono impostati probe e regole per le porte seguenti:

* Endpoint di connessione client: 19000
* Endpoint del gateway HTTP: 19080
* Porta dell'applicazione: 80
* Porta dell'applicazione: 443
* Proxy inverso di Service Fabric: 19081

Se sono necessarie altre porte dell'applicazione, si dovranno modificare le risorse **Microsoft.Network/loadBalancers** e **Microsoft.Network/networkSecurityGroups** in modo da consentire il traffico in ingresso.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rete virtuale, subnet e gruppo di sicurezza di rete

I nomi della rete virtuale, della subnet e del gruppo di sicurezza di rete vengono dichiarati nei parametri del modello, così come gli spazi indirizzi della rete virtuale e della subnet. Questi ultimi vengono configurati nella risorsa **Microsoft.Network/virtualNetworks**:

* Spazio degli indirizzi della rete virtuale: 172.16.0.0/20
* Spazio degli indirizzi della subnet di Service Fabric: 172.16.2.0/23

Le regole per il traffico in ingresso seguenti vengono abilitate nella risorsa **Microsoft.Network/networkSecurityGroups**. È possibile modificare i valori di porta modificando le variabili del modello.

* Endpoint di connessione client (TCP): 19000
* Endpoint del gateway HTTP (HTTP/TCP): 19080
* SMB: 445
* Comunicazione tra nodi: 1025, 1026, 1027
* Intervallo di porte temporaneo: da 49152 a 65534 (sono necessarie almeno 256 porte).
* Porte utilizzabili per l'applicazione: 80 e 443
* Intervallo di porte dell'applicazione: da 49152 a 65534 (usato per la comunicazione tra servizi. Non sono aperte altre porte nel servizio di bilanciamento del carico).
* Bloccare tutte le altre porte

Se sono necessarie altre porte dell'applicazione, si dovranno modificare le risorse **Microsoft.Network/loadBalancers** e **Microsoft.Network/networkSecurityGroups** in modo da consentire il traffico in ingresso.

### <a name="windows-defender"></a>Windows Defender
Per impostazione predefinita, il programma [Windows Defender Antivirus](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) è installato e funzionante in Windows Server 2016. L'interfaccia utente viene installata per impostazione predefinita in alcuni SKU, ma non è obbligatoria. Per ogni tipo di nodo/set di scalabilità di macchina virtuale dichiarato nel modello, viene usata l'estensione [Azure VM Antimalware](/azure/virtual-machines/extensions/iaas-antimalware-windows) per escludere le directory e i processi di Service Fabric:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Impostare i parametri del modello

Nel file dei parametri [azuredeploy.parameters.json][parameters] vengono dichiarati molti valori usati per distribuire il cluster e le risorse associate. Di seguito sono elencati i parametri da modificare per la distribuzione:

**Parametro** | **Valore di esempio** | **Note** 
|---|---|---|
|adminUserName|vmadmin| Nome utente amministratore per le VM del cluster. [Requisiti dei nomi utente per le VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Password amministratore per le VM del cluster. [Requisiti delle password per le VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| Nome del cluster. Può contenere solo lettere e numeri. La lunghezza deve essere compresa tra 3 e 23 caratteri.|
|location|southcentralus| Località del cluster. |
|certificateThumbprint|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore di identificazione personale SHA1 del certificato, ad esempio "6190390162C988701DB5676EB81083EA608DCCF3".</p> |
|certificateUrlValue|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato. </p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere l'URL del certificato, ad esempio "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore dell'insieme di credenziali di origine, ad esempio "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurare l'autenticazione client di Azure Active Directory
Per i cluster di Service Fabric distribuiti in una rete pubblica ospitata in Azure, la raccomandazione per l'autenticazione reciproca client-nodo è:
* Usare Azure Active Directory per l'identità del client.
* Usare un certificato per l'identità del server e la crittografia SSL della comunicazione HTTP.

È necessario configurare Azure Active Directory (Azure AD) per autenticare i client per un cluster di Service Fabric prima di [creare il cluster](#createvaultandcert). Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso degli utenti alle applicazioni. 

Un cluster di Service Fabric offre diversi punti di accesso alle proprie funzionalità di gestione, tra cui [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e [Visual Studio](service-fabric-manage-application-in-visual-studio.md) basati sul Web. Si creano quindi due applicazioni Azure AD per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa.  Dopo aver creato le applicazioni, si assegnano gli utenti ai ruoli di sola lettura e di amministratore.

> [!NOTE]
> È necessario completare i passaggi seguenti prima di creare il cluster. Poiché per gli script sono previsti nomi ed endpoint dei cluster, i valori devono essere pianificati e non quelli già creati.

In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Per semplificare la procedura di configurazione di Azure AD con un cluster di Service Fabric, è stato creato un set di script di Windows PowerShell. [Scaricare gli script](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) nel computer.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Creare applicazioni Azure AD e assegnare gli utenti ai ruoli
Creare due applicazioni Azure AD per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa. Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai [ruoli supportati da Service Fabric](service-fabric-cluster-security-roles.md): sola lettura e amministratore.

Eseguire `SetupApplications.ps1` e indicare l'ID tenant, il nome del cluster e l'URL di risposta dell'applicazione Web come parametri. Specificare i nomi utente e le password per gli utenti. Ad esempio: 

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Per i cloud nazionali (ad esempio Azure per enti pubblici, Azure Cina o Azure Germania), specificare il parametro `-Location`.

L'*ID tenant* o l'ID directory si trovano nel [portale di Azure](https://portal.azure.com). Selezionare **Azure Active Directory** > **Proprietà** e copiare il valore di **ID directory**.

*ClusterName* viene usato come prefisso per le applicazioni Azure AD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster. Facilita solo il mapping degli artefatti di Azure AD al cluster di Service Fabric in uso.

*WebApplicationReplyUrl* è l'endpoint predefinito che Azure AD restituisce agli utenti dopo che hanno completato l'accesso. Impostare questo endpoint come endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

https://&lt;cluster_domain&gt;:19080/Explorer

Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Nelle applicazioni del tenant nel [portale di Azure](https://portal.azure.com) verranno visualizzate due nuove voci:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

È consigliabile tenere aperta la finestra di PowerShell perché, durante la creazione del cluster, lo script visualizza il codice JSON richiesto dal modello di Resource Manager.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Aggiungere la configurazione di Azure AD per l'uso di Azure AD per l'accesso client
In [azuredeploy.json][template] configurare Azure AD nella sezione **Microsoft.ServiceFabric/clusters**. Aggiungere parametri per l'ID tenant, l'ID applicazione del cluster e l'ID dell'applicazione client.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Aggiungere i valori dei parametri nel file dei parametri [azuredeploy.parameters.json][parameters]. Ad esempio: 

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configurare la raccolta di diagnostica nel cluster
Quando si esegue un cluster di Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Azure, che carica i log in Archiviazione di Azure e offre anche la possibilità di inviarli ad Azure Application Insights o Hub eventi. È anche possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in una piattaforma di analisi, ad esempio i log di Monitoraggio di Azure o un'altra soluzione di analisi dei log.

Se si segue questa esercitazione, la raccolta di diagnostica è già configurata nel [modello][template].

Se in un cluster esistente non è stata distribuita l'estensione Diagnostica, è possibile aggiungerla o aggiornarla tramite il modello del cluster. Modificare il modello di Resource Manager usato per creare il cluster esistente o scaricare il modello dal portale. Modificare il file template.json eseguendo le attività seguenti:

Aggiungere una nuova risorsa di archiviazione alla sezione resources del modello:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Aggiungere quindi i parametri relativi al nome e al tipo dell'account di archiviazione alla sezione parameters del modello. Sostituire il testo segnaposto STORAGE ACCOUNT NAME GOES HERE con il nome dell'account di archiviazione preferito.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Aggiungere l'estensione **IaaSDiagnostics** alla matrice di estensioni della proprietà **VirtualMachineProfile** di ogni risorsa **Microsoft.Compute/virtualMachineScaleSets** del cluster.  Se si usa il [modello di esempio][template], sono disponibili tre set di scalabilità di macchine virtuali, uno per ogni tipo di nodo del cluster.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Configurare il servizio EventStore
Il servizio EventStore è un'opzione di monitoraggio di Service Fabric. EventStore offre la possibilità di comprendere lo stato del cluster o dei carichi di lavoro in un determinato punto nel tempo. EventStore è un servizio di Service Fabric con stato che gestisce gli eventi del cluster. L'evento viene esposto usando Service Fabric Explorer, REST e le API. EventStore esegue direttamente una query sul cluster per visualizzare i dati di diagnostica relativi a qualsiasi entità presente nel cluster e deve essere usato per:

* Diagnosticare problemi di sviluppo o test o laddove sia possibile usare una pipeline di monitoraggio
* Verificare che le azioni di gestione eseguite nel cluster vengano elaborate correttamente
* Ottenere un'istantanea del modo in cui Service Fabric interagisce con una particolare entità



Per abilitare il servizio EventStore nel cluster, aggiungere quanto segue alla proprietà **fabricSettings** della risorsa **Microsoft.ServiceFabric/clusters**:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Configurare i log di Monitoraggio di Azure per il cluster

I log di Monitoraggio di Azure sono consigliati per il monitoraggio di eventi a livello di cluster. Per configurare i log di Monitoraggio di Azure per il monitoraggio del cluster, è necessario [abilitare il servizio di diagnostica per la visualizzazione di eventi a livello di cluster](#configure-diagnostics-collection-on-the-cluster).  

L'area di lavoro deve essere connessa ai dati di diagnostica provenienti dal cluster.  Questi dati di log vengono archiviati nell'account di archiviazione *applicationDiagnosticsStorageAccountName* e nelle tabelle WADServiceFabric*EventTable, WADWindowsEventLogsTable e WADETWEventTable.

Aggiungere l'area di lavoro Azure Log Analytics e aggiungere la soluzione all'area di lavoro:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Aggiungere quindi i parametri:
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Aggiungere le variabili:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Aggiungere l'estensione dell'agente di Log Analytics a ogni set di scalabilità di macchine virtuali del cluster e connettere l'agente all'area di lavoro Log Analytics. Ciò consente la raccolta dei dati di diagnostica relativi a contenitori, applicazioni e monitoraggio delle prestazioni. Aggiungendolo come un'estensione alla risorsa del set di scalabilità di macchine virtuali, Azure Resource Manager ne garantisce l'installazione su ogni nodo, anche in caso di ridimensionamento del cluster.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuire la rete virtuale e il cluster

Configurare quindi la topologia di rete e distribuire il cluster di Service Fabric. Il modello di Resource Manager [azuredeploy.json][template] consente di creare una rete virtuale, una subnet e un gruppo di sicurezza di rete per Service Fabric. Il modello distribuisce anche un cluster in cui è abilitata la sicurezza basata su certificati. Come certificato per i cluster di produzione usare un certificato di un'autorità di certificazione. Per proteggere i cluster di test è possibile usare un certificato autofirmato.

Il modello in questo articolo distribuisce un cluster che usa l'identificazione personale del certificato per identificare il certificato del cluster. Nessun certificato può avere la stessa identificazione personale di un altro, il che rende più difficile gestire i certificati. Passare dalle identificazioni personali di certificato all'uso di nomi comuni di certificato in un cluster distribuito semplifica notevolmente la gestione dei certificati. Per informazioni su come aggiornare il cluster per l'uso dei nomi comuni del certificato per la gestione dei certificati, vedere [Passare dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato in un cluster](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Creare un cluster usando un certificato esistente

Lo script seguente usa il cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e un modello per distribuire un nuovo cluster in Azure. Il cmdlet crea un nuovo insieme di credenziali delle chiavi in Azure e carica il certificato.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Creare un cluster usando un nuovo certificato autofirmato

Lo script seguente usa il cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e un modello per distribuire un nuovo cluster in Azure. Il cmdlet crea un insieme di credenziali delle chiavi in Azure, aggiunge un nuovo certificato autofirmato all'insieme di credenziali delle chiavi e scarica il file del certificato in locale.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro

Connettersi al cluster usando il modulo di PowerShell Service Fabric installato con Service Fabric SDK.  Installare prima di tutto il certificato nell'archivio personale (My) dell'utente corrente nel computer in uso. Eseguire il seguente comando PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

È ora possibile connettersi al cluster sicuro.

Il modulo di PowerShell **Service Fabric** include molti cmdlet per la gestione di cluster, applicazioni e servizi di Service Fabric. Usare il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi al cluster sicuro. L'identificazione personale SHA1 del certificato e i dettagli dell'endpoint della connessione sono disponibili nell'output del passaggio precedente.

Se in precedenza è stata configurata l'autenticazione del client Azure AD, eseguire il comando seguente: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se l'autenticazione del client Azure AD non è stata configurata, eseguire il comando seguente:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verificare di essere connessi e che il cluster sia integro usando il cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Pulire le risorse

Gli altri articoli di questa serie di esercitazioni usano il cluster che è stato creato. Se non si intende passare subito all'articolo successivo, è opportuno [eliminare il cluster](service-fabric-cluster-delete.md) per evitare di sostenere costi.

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'esercitazione seguente per scoprire come ridimensionare il cluster.

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando PowerShell
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Configurare l'autenticazione di Azure Active Directory
> * Configurare la raccolta di diagnostica
> * Configurare il servizio EventStore
> * Configurare i log di Monitoraggio di Azure
> * Creare un cluster sicuro di Service Fabric in Azure PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come eseguire il monitoraggio del cluster.
> [!div class="nextstepaction"]
> [Eseguire il monitoraggio di un cluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
