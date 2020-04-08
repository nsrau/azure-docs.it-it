---
title: Archiviare i log delle risorse di Azure nell'account di archiviazione Documenti Microsoft
description: Informazioni su come archiviare i log delle risorse di Azure per la conservazione a lungo termine in un account di archiviazione.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804606"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiviare i log delle risorse di Azure nell'account di archiviazione
[I log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività di Azure e i log delle risorse, forniscono informazioni dettagliate su diagnostica e controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono.  Questo articolo descrive la raccolta dei log della piattaforma in un account di archiviazione di Azure per conservare i dati per l'archiviazione.

## <a name="prerequisites"></a>Prerequisiti
È necessario [creare un account di archiviazione](../../storage/common/storage-account-create.md) di Azure se non è già presente. Non è necessario che l'account di archiviazione si svolgi nella stessa sottoscrizione della risorsa che invia i log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC appropriato a entrambe le sottoscrizioni.

> [!IMPORTANT]
> Per inviare i dati a un archivio non modificabile, impostare i criteri non modificabili per l'account di archiviazione come descritto in Impostare e gestire i criteri di [immutabilità per l'archiviazione BLOB](../../storage/blobs/storage-blob-immutability-policies-manage.md). È necessario seguire tutti i passaggi descritti in questo articolo, inclusa l'abilitazione delle scritture di BLOB di accodamento protetto.

> [!IMPORTANT]
> Gli account Azure Data Lake Storage Gen2 non sono attualmente supportati come destinazione per le impostazioni di diagnostica anche se possono essere elencati come opzione valida nel portale di Azure.Azure Data Lake Storage Gen2 accounts are not currently supported as a destination for diagnostic settings even though they may be listed as a valid option in the Azure portal.


È consigliabile non usare un account di archiviazione esistente contenente altri dati non di monitoraggio archiviati in modo da poter controllare meglio l'accesso ai dati. Se si archiviano tuttavia il log attività e i log delle risorse, è possibile scegliere di usare lo stesso account di archiviazione per mantenere tutti i dati di monitoraggio in una posizione centrale.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
Inviare i log della piattaforma all'archiviazione e ad altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure.Send platform logs to storage and other destinations by creating a diagnostic setting for an Azure resource. Per informazioni dettagliate, vedere [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.See Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md) for details.


## <a name="collect-data-from-compute-resources"></a>Raccogliere dati dalle risorse di calcoloCollect data from compute resources
Le impostazioni di diagnostica raccoglieranno i log delle risorse per le risorse di calcolo di Azure come qualsiasi altra risorsa, ma non per il sistema operativo guest o i carichi di lavoro. Per raccogliere questi dati, installare l'agente di diagnostica di [Windows Azure.](diagnostics-extension-overview.md) 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schema dei log della piattaforma nell'account di archiviazioneSchema of platform logs in storage account

Dopo aver creato l'impostazione di diagnostica, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento in una delle categorie di log abilitate. I BLOB all'interno del contenitore usano la convenzione di denominazione seguente:The blobs within the container use the following naming convention:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, il BLOB per un gruppo di sicurezza di rete potrebbe avere un nome simile al seguente:For example, the blob for a network security group might have a name similar to the following:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m-00) è sempre 00, poiché gli eventi del log delle risorse vengono suddivisi in singoli BLOB all'ora.

All'interno del file PT1H.json, ogni evento viene archiviato con il seguente formato. Verrà usato uno schema di primo livello comune, ma sarà univoco per ogni servizio di Azure, come descritto in [Schema log delle risorse](diagnostic-logs-schema.md) e schema del log [attività.](activity-log-schema.md)

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> I log della piattaforma vengono scritti nell'archiviazione BLOB usando [righe JSON](http://jsonlines.org/), dove ogni evento è una riga e il carattere di nuova riga indica un nuovo evento. Questo formato è stato implementato nel novembre 2018. Prima di questa data, i log venivano scritti nell'archivio BLOB come matrice json di record, come descritto in Preparare la modifica del formato ai log della piattaforma di Monitoraggio di [Azure archiviati in un account di archiviazione.](resource-logs-blob-format.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sui log delle risorse](platform-logs-overview.md).
* [Creare l'impostazione di diagnostica per raccogliere log e metriche in Azure.Create diagnostic setting to collect logs and metrics in Azure](diagnostic-settings.md).
* [Scaricare i BLOB per l'analisi](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiviare i log](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)di Azure Active Directory con Monitoraggio di Azure .
