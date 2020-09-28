---
title: Aggiornare la versione di Mongo dell'API Azure Cosmos DB per l'account MongoDB
description: Come aggiornare facilmente la versione del protocollo di rete MongoDB per l'API Azure Cosmos DB esistente per gli account MongoDB
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409664"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Aggiornare la versione del protocollo wire di MongoDB dell'API del Azure Cosmos DB per l'account MongoDB

Questo articolo descrive come aggiornare la versione del protocollo Wire dell'API Azure Cosmos DB per l'account MongoDB. Dopo aver aggiornato la versione del protocollo wire, è possibile usare le funzionalità più recenti nell'API Azure Cosmos DB per MongoDB. Il processo di aggiornamento non interrompe la disponibilità dell'account e non utilizza ur/s né diminuisce la capacità del database in qualsiasi momento. Questo processo non influirà sui dati o sugli indici esistenti.

>[!Note]
> Attualmente, solo gli account validi che usano la versione 3,2 del server possono essere aggiornati alla versione 3,6. Se l'account non Visualizza l'opzione di aggiornamento, inviare [un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Aggiornamento dalla versione 3,2 alla versione 3,6

### <a name="benefits-of-upgrading-to-version-36"></a>Vantaggi dell'aggiornamento alla versione 3.6

Di seguito sono riportate le nuove funzionalità incluse nella versione 3,6:
- Prestazioni e stabilità migliorate
- Supporto per i nuovi comandi di database
- Supporto per la pipeline di aggregazione per impostazione predefinita e le nuove fasi di aggregazione
- Supporto per flussi di modifiche
- Supporto per indici composti
- Supporto tra partizioni per le operazioni seguenti: Update, DELETE, Count e Sort
- Miglioramento delle prestazioni per le operazioni di aggregazione seguenti: $count, $skip, $limit e $group
- L'indicizzazione con caratteri jolly è ora supportata

### <a name="changes-from-version-32"></a>Modifiche dalla versione 3,2

- Gli **errori di RequestRateIsLarge sono stati rimossi**. Le richieste provenienti dall'applicazione client non restituiranno più 16500 errori. Al contrario, le richieste verranno riavviate fino al completamento o al timeout.
- Il timeout per richiesta è impostato su 60 secondi.
- Per le raccolte MongoDB create nella nuova versione del protocollo wire la `_id` proprietà viene indicizzata per impostazione predefinita.

### <a name="action-required"></a>Azione richiesta

Per l'aggiornamento alla versione 3,6, il suffisso dell'endpoint dell'account del database verrà aggiornato nel formato seguente:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

È necessario sostituire l'endpoint esistente nelle applicazioni e i driver che si connettono a questo account del database. **Solo le connessioni che usano il nuovo endpoint avranno accesso alle funzionalità della versione 3,6 di MongoDB**. Il suffisso dell'endpoint precedente deve essere `.documents.azure.com` .

>[!Note]
> Questo endpoint potrebbe avere piccole differenze se l'account è stato creato in un cloud di Azure sovrano, governativo o limitato.

### <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

1. Per prima cosa, passare alla portale di Azure e passare al pannello di panoramica dell'API Azure Cosmos DB per MongoDB. Verificare che la versione del server sia `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Panoramica dell'account portale di Azure con MongoDB" border="false":::

2. Selezionare il pannello dalle opzioni a sinistra `Features` . Verranno rivelate le funzionalità a livello di account disponibili per l'account di database.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Panoramica di portale di Azure con l'account MongoDB con il pannello funzionalità evidenziato" border="false":::

3. Fare clic sulla `Upgrade to Mongo server version 3.6` riga. Se questa opzione non viene visualizzata, è possibile che l'account non sia idoneo per l'aggiornamento. Se questo è il caso, inviare [un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Pannello funzionalità con opzioni." border="false":::

4. Esaminare le informazioni visualizzate sull'aggiornamento specifico. Si noti che l'aggiornamento verrà completato solo se le applicazioni usano l'endpoint aggiornato, come evidenziato in questa sezione. Fare clic su on appena `Enable` si è pronti per avviare il processo.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Linee guida per l'aggiornamento espanse." border="false":::

5. Dopo l'avvio del processo, nel `Features` menu sarà visualizzato lo stato dell'aggiornamento. Lo stato passerà da `Pending` , a `In Progress` , a `Upgraded` . Questo processo non influirà sulle funzionalità o sulle operazioni esistenti dell'account del database.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Stato dell'aggiornamento dopo l'avvio." border="false":::

6. Una volta completato l'aggiornamento, lo stato verrà visualizzato come `Upgraded` . Fare clic su di esso per ulteriori informazioni sui passaggi successivi e sulle azioni che è necessario eseguire per finalizzare il processo. Se si è verificato un problema durante l'elaborazione della richiesta, [contattare il supporto tecnico](https://azure.microsoft.com/en-us/support/create-ticket/) .

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Stato dell'account aggiornato." border="false":::

7. **Per iniziare a usare la versione aggiornata dell'account del database**, tornare al pannello `Overview` e copiare la nuova stringa di connessione da usare nell'applicazione. Le applicazioni inizieranno a usare la versione aggiornata non appena si connetteranno al nuovo endpoint. Le connessioni esistenti non verranno interrotte e potranno essere aggiornate in praticità. Per garantire un'esperienza coerente, è necessario che tutte le applicazioni usino il nuovo endpoint.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nuovo pannello panoramica." border="false":::

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle funzionalità supportate e non supportate [della versione 3,6 di MongoDB](mongodb-feature-support-36.md).
- Per altre informazioni, vedere le [funzionalità della versione Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
