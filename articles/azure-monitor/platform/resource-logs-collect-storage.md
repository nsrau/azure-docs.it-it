---
title: Archiviare i log delle risorse di Azure nell'account di archiviazione | Microsoft Docs
description: Informazioni su come archiviare i log delle risorse di Azure per la conservazione a lungo termine in un account di archiviazione.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 306f6cb0b50b7befcbf51e6164a5da887d35616e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74030883"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Archiviare i log delle risorse di Azure nell'account di archiviazione
I [log delle risorse](resource-logs-overview.md) in Azure forniscono dati avanzati e frequenti sul funzionamento interno di una risorsa di Azure. Questo articolo descrive come raccogliere i log delle risorse in un account di archiviazione di Azure per conservare i dati per l'archiviazione.

## <a name="prerequisites"></a>prerequisiti
È necessario [creare un account di archiviazione di Azure](../../storage/common/storage-quickstart-create-account.md) , se non ne è già presente uno. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della risorsa che invia log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC appropriato a entrambe le sottoscrizioni.


> [!IMPORTANT]
> Gli account Azure Data Lake Storage Gen2 non sono attualmente supportati come destinazione per le impostazioni di diagnostica anche se possono essere elencati come un'opzione valida nel portale di Azure.


Non usare un account di archiviazione esistente con altri dati non di monitoraggio archiviati, in modo da poter controllare meglio l'accesso ai dati di monitoraggio. Se tuttavia si archivia anche il [log attività](activity-logs-overview.md) in un account di archiviazione, è possibile scegliere di usare lo stesso account di archiviazione per conservare tutti i dati di monitoraggio in una posizione centrale.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
I log delle risorse non vengono raccolti per impostazione predefinita. Raccoglierli in un account di archiviazione di Azure e in altre destinazioni creando un'impostazione di diagnostica per una risorsa di Azure. Per informazioni dettagliate, vedere [creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](diagnostic-settings.md) .


## <a name="data-retention"></a>Conservazione dei dati
I criteri di conservazione definiscono il numero di giorni per cui conservare i dati di ogni categoria di log e i dati delle metriche archiviati in un account di archiviazione. Un criterio di conservazione può essere un numero qualsiasi di giorni compreso tra 0 e 365. Un criterio di conservazione pari a zero specifica che gli eventi per tale categoria di log vengono archiviati per un periodo illimitato.

I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata, i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata attuale vengono eliminati i log relativi alla giornata precedente a ieri. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Schema dei log delle risorse nell'account di archiviazione

Dopo aver creato l'impostazione di diagnostica, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento in una delle categorie di log abilitate. I BLOB all'interno del contenitore usano la convenzione di denominazione seguente:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, il BLOB per un gruppo di sicurezza di rete potrebbe avere un nome simile al seguente:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m = 00) è sempre 00, perché gli eventi del log delle risorse sono suddivisi in singoli BLOB all'ora.

All'interno del file PT1H. JSON ogni evento viene archiviato con il formato seguente. Verrà usato uno schema di primo livello comune, ma sarà univoco per ogni servizio di Azure, come descritto in [schema dei log delle risorse](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> I log di piattaforma vengono scritti nell'archiviazione BLOB usando le [righe JSON](http://jsonlines.org/), dove ogni evento è una riga e il carattere di nuova riga indica un nuovo evento. Questo formato è stato implementato nel 2018 novembre. Prima di questa data, i log venivano scritti nell'archiviazione BLOB come una matrice di record JSON, come descritto in [preparare la modifica del formato ai log della piattaforma di monitoraggio di Azure archiviati in un account di archiviazione](resource-logs-blob-format.md).

## <a name="next-steps"></a>Passaggi successivi

* [Scaricare i BLOB per l'analisi](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiviare Azure Active Directory log con monitoraggio di Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Altre informazioni sui log delle risorse](../../azure-monitor/platform/resource-logs-overview.md).

