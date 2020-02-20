---
title: Archiviare i log delle risorse di Azure nell'account di archiviazione | Microsoft Docs
description: Informazioni su come archiviare i log delle risorse di Azure per la conservazione a lungo termine in un account di archiviazione.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: f4109359adaaeae0a1ba027ac04439ff60b5a939
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467182"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiviare i log delle risorse di Azure nell'account di archiviazione
I [log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività e i log delle risorse di Azure, forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono.  Questo articolo descrive come raccogliere i log della piattaforma in un account di archiviazione di Azure per conservare i dati per l'archiviazione.

## <a name="prerequisites"></a>Prerequisiti
È necessario [creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md) , se non ne è già presente uno. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della risorsa che invia log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC appropriato a entrambe le sottoscrizioni.


> [!IMPORTANT]
> Gli account Azure Data Lake Storage Gen2 non sono attualmente supportati come destinazione per le impostazioni di diagnostica anche se possono essere elencati come un'opzione valida nel portale di Azure.


Non usare un account di archiviazione esistente con altri dati non di monitoraggio archiviati, in modo da poter controllare meglio l'accesso ai dati. Se si archiviano i log attività e i log delle risorse insieme, è possibile scegliere di usare lo stesso account di archiviazione per conservare tutti i dati di monitoraggio in una posizione centrale.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
Inviare i log della piattaforma all'archiviazione e ad altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure. Per informazioni dettagliate, vedere [creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md) .


## <a name="collect-data-from-compute-resources"></a>Raccogliere dati dalle risorse di calcolo
Le impostazioni di diagnostica raccolgono i log delle risorse per le risorse di calcolo di Azure come qualsiasi altra risorsa, ma non il sistema operativo guest o i carichi di lavoro. Per raccogliere questi dati, installare l' [agente di diagnostica di Azure di Windows](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schema dei log della piattaforma nell'account di archiviazione

Dopo aver creato l'impostazione di diagnostica, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento in una delle categorie di log abilitate. I BLOB all'interno del contenitore usano la convenzione di denominazione seguente:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, il BLOB per un gruppo di sicurezza di rete potrebbe avere un nome simile al seguente:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m = 00) è sempre 00, perché gli eventi del log delle risorse sono suddivisi in singoli BLOB all'ora.

All'interno del file PT1H. JSON ogni evento viene archiviato con il formato seguente. Verrà usato uno schema di primo livello comune, ma sarà univoco per ogni servizio di Azure, come descritto in schema dei log [delle risorse](diagnostic-logs-schema.md) e [schema del log attività](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> I log di piattaforma vengono scritti nell'archiviazione BLOB usando le [righe JSON](http://jsonlines.org/), dove ogni evento è una riga e il carattere di nuova riga indica un nuovo evento. Questo formato è stato implementato nel 2018 novembre. Prima di questa data, i log venivano scritti nell'archiviazione BLOB come una matrice di record JSON, come descritto in [preparare la modifica del formato ai log della piattaforma di monitoraggio di Azure archiviati in un account di archiviazione](resource-logs-blob-format.md).

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui log delle risorse](platform-logs-overview.md).
* [Creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md).
* [Scaricare i BLOB per l'analisi](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiviare Azure Active Directory log con monitoraggio di Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
