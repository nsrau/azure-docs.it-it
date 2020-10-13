---
title: Operazioni del piano di controllo e del piano dati
description: Descrive la differenza tra le operazioni del piano di controllo e del piano dati. Le operazioni del piano di controllo vengono gestite da Azure Resource Manager. Le operazioni del piano dati vengono gestite da un servizio.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371270"
---
# <a name="azure-control-plane-and-data-plane"></a>Piano di controllo e piano dati di Azure

Le operazioni di Azure possono essere divise in due categorie: piano di controllo e piano dati. Questo articolo descrive le differenze tra questi due tipi di operazioni.

Il piano di controllo viene usato per gestire le risorse nella sottoscrizione. Il piano dati viene usato per usare le funzionalità esposte dall'istanza di un tipo di risorsa.

Ad esempio:

* Si crea una macchina virtuale tramite il piano di controllo. Dopo aver creato la macchina virtuale, è possibile interagire con esso tramite operazioni del piano dati, ad esempio Remote Desktop Protocol (RDP).

* Si crea un account di archiviazione tramite il piano di controllo. Usare il piano dati per leggere e scrivere i dati nell'account di archiviazione.

* Si crea un database Cosmos tramite il piano di controllo. Per eseguire query sui dati nel database Cosmos, usare il piano dati.

## <a name="control-plane"></a>Piano di controllo

Tutte le richieste per le operazioni del piano di controllo vengono inviate all'URL Azure Resource Manager. Tale URL varia a seconda dell'ambiente Azure.

* Per Azure Global, l'URL è `https://management.azure.com` .
* Per Azure per enti pubblici, l'URL è `https://management.usgovcloudapi.net/` .
* Per Azure Germania, l'URL è `https://management.microsoftazure.de/` .
* Per Microsoft Azure Cina 21Vianet, l'URL è `https://management.chinacloudapi.cn` .

Per individuare le operazioni che usano l'URL di Azure Resource Manager, vedere l' [API REST di Azure](/rest/api/azure/). Ad esempio, l' [operazione di creazione o aggiornamento](/rest/api/mysql/databases/createorupdate) per MySQL è un'operazione del piano di controllo perché l'URL della richiesta è:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager gestisce tutte le richieste del piano di controllo. Applica automaticamente le funzionalità di Azure implementate per gestire le risorse, ad esempio:

* [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)
* [Criteri di Azure](../../governance/policy/overview.md)
* [Blocchi di gestione](lock-resources.md)
* [Log attività](view-activity-logs.md)

Dopo l'autenticazione della richiesta, Azure Resource Manager la invia al provider di risorse, che completa l'operazione.

Il piano di controllo include due scenari per la gestione delle richieste: "campo verde" e "campo marrone". Il campo verde si riferisce alle nuove risorse. Il campo marrone fa riferimento alle risorse esistenti. Quando si distribuiscono le risorse, Azure Resource Manager riconosce quando creare nuove risorse e quando aggiornare le risorse esistenti. Non è necessario preoccuparsi che vengano create risorse identiche.

## <a name="data-plane"></a>Piano dati

Le richieste per le operazioni del piano dati vengono inviate a un endpoint specifico dell'istanza. Ad esempio, l' [operazione di rilevamento della lingua](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) in Servizi cognitivi è un'operazione del piano dati perché l'URL della richiesta è:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Le operazioni del piano dati non sono limitate all'API REST. Potrebbero richiedere credenziali aggiuntive, ad esempio l'accesso a una macchina virtuale o a un server di database.

Le funzionalità che applicano la gestione e la governance potrebbero non essere applicate alle operazioni del piano dati. È necessario considerare i diversi modi in cui gli utenti interagiscono con le soluzioni. Ad esempio, un blocco che impedisce agli utenti di eliminare un database non impedisce agli utenti di eliminare dati tramite query.

È possibile utilizzare alcuni criteri per governare le operazioni del piano dati. Per altre informazioni, vedere [modalità del provider di risorse (anteprima) in criteri di Azure](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Passaggi successivi

* Per una panoramica di Azure Resource Manager, vedere [che cos'è Azure Resource Manager?](overview.md)

* Per altre informazioni sull'effetto delle definizioni dei criteri sulle nuove risorse e sulle risorse esistenti, vedere [valutare l'impatto di una nuova definizione di criteri di Azure](../../governance/policy/concepts/evaluate-impact.md).
