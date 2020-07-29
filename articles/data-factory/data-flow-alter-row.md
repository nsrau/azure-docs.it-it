---
title: Trasformazione alter Row nel flusso di dati del mapping
description: Come aggiornare la destinazione del database utilizzando la trasformazione alter Row nel flusso di dati di mapping
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982650"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Trasformazione alter Row nel flusso di dati del mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione alter Row per impostare i criteri INSERT, DELETE, Update e Upsert sulle righe. È possibile aggiungere condizioni uno-a-molti come espressioni. Queste condizioni devono essere specificate in ordine di priorità, in quanto ogni riga verrà contrassegnata con i criteri corrispondenti alla prima espressione corrispondente. Ognuna di queste condizioni può comportare l'inserimento, l'aggiornamento, l'eliminazione o la corretto di una riga o di righe. Alter Row può produrre entrambe le azioni DDL & DML nel database.

![Alter Row-impostazioni](media/data-flow/alter-row1.png "Alter Row-impostazioni")

Le trasformazioni alter Row funzioneranno solo sui sink di database o CosmosDB nel flusso di dati. Le azioni assegnate alle righe (Insert, Update, DELETE, Upsert) non vengono eseguite durante le sessioni di debug. Eseguire un'attività Esegui flusso di dati in una pipeline per applicare i criteri alter Row nelle tabelle di database.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>Specificare un criterio di riga predefinito

Creare una trasformazione alter Row e specificare un criterio di riga con la condizione `true()` . Ogni riga che non corrisponde ad alcuna espressione definita in precedenza verrà contrassegnata per i criteri di riga specificati. Per impostazione predefinita, ogni riga che non corrisponde ad alcuna espressione condizionale verrà contrassegnata per `Insert` .

![Alter Row Policy](media/data-flow/alter-row4.png "Alter Row Policy")

> [!NOTE]
> Per contrassegnare tutte le righe con un criterio, è possibile creare una condizione per quel criterio e specificare la condizione come `true()` .

## <a name="view-policies-in-data-preview"></a>Visualizzare i criteri nell'anteprima dei dati

Utilizzare la [modalità di debug](concepts-data-flow-debug-mode.md) per visualizzare i risultati dei criteri alter Row nel riquadro di anteprima dei dati. Un'anteprima dei dati di una trasformazione alter Row non produrrà azioni DDL o DML sulla destinazione.

![Alter Row (criteri)](media/data-flow/alter-row3.png "Alter Row (criteri)")

Tutti i criteri alter Row sono rappresentati da un'icona che indica se si verificherà un'azione INSERT, Update, Upsert o Deleted. L'intestazione superiore mostra il numero di righe interessate da ogni criterio nell'anteprima.

## <a name="allow-alter-row-policies-in-sink"></a>Consenti alter Row Policy in sink

Affinché i criteri alter Row funzionino, il flusso di dati deve scrivere in un database o in un sink Cosmos. Nella scheda **Impostazioni** del sink abilitare i criteri alter Row consentiti per il sink.

![Alter Row sink](media/data-flow/alter-row2.png "Alter Row sink")

Il comportamento predefinito è consentire solo gli inserimenti. Per consentire aggiornamenti, Upsert o eliminazioni, selezionare la casella nel sink corrispondente a tale condizione. Se le eliminazioni Updates, Upsert o, sono abilitate, è necessario specificare le colonne chiave nel sink per le quali trovare la corrispondenza.

> [!NOTE]
> Se inserimenti, aggiornamenti o Upsert modifica lo schema della tabella di destinazione nel sink, il flusso di dati avrà esito negativo. Per modificare lo schema di destinazione nel database, scegliere **ricrea tabella** come azione tabella. Verrà eliminata e ricreata la tabella con la nuova definizione dello schema.

La trasformazione sink richiede una singola chiave o una serie di chiavi per l'identificazione univoca delle righe nel database di destinazione. Per i sink SQL, impostare le chiavi nella scheda Impostazioni sink. Per CosmosDB, impostare la chiave di partizione nelle impostazioni e impostare anche il campo di sistema CosmosDB "ID" nel mapping del sink. Per CosmosDB, è obbligatorio includere la colonna di sistema "ID" per gli aggiornamenti, Upsert ed eliminazioni.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Merge e Upsert con il database SQL di Azure e la sinapsi

I flussi di dati ADF supportano le unioni con il database SQL di Azure e il pool di database sinapsi (data warehouse) con l'opzione Upsert.

Tuttavia, è possibile che si verifichino scenari in cui lo schema del database di destinazione utilizza la proprietà Identity delle colonne chiave. Con ADF è necessario identificare le chiavi che si utilizzeranno per trovare la corrispondenza con i valori di riga per gli aggiornamenti e Upsert. Tuttavia, se per la colonna di destinazione è impostata la proprietà Identity e si utilizza il criterio Upsert, il database di destinazione non consentirà di scrivere nella colonna. È inoltre possibile che si verifichino errori quando si tenta di eseguire il Upsert sulla colonna di distribuzione di una tabella distribuita.

Ecco alcuni modi per risolvere il problema:

1. Passare alle impostazioni di trasformazione del sink e impostare "Ignora scrittura colonne chiave". Questo consentirà a ADF di non scrivere la colonna selezionata come valore chiave per il mapping.

2. Se tale colonna chiave non è la colonna che causa il problema per le colonne Identity, è possibile utilizzare l'opzione SQL di pre-elaborazione della trasformazione sink: ```SET IDENTITY_INSERT tbl_content ON``` . Quindi, disattivarla con la proprietà SQL di post-elaborazione: ```SET IDENTITY_INSERT tbl_content OFF``` .

3. Sia per il caso di identità che per la colonna di distribuzione, è possibile cambiare la logica da Upsert a utilizzando una condizione di aggiornamento separata e una condizione di inserimento separata utilizzando una trasformazione Suddivisione condizionale. In questo modo, è possibile impostare il mapping sul percorso di aggiornamento per ignorare il mapping delle colonne chiave.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una trasformazione alter Row denominata `CleanData` che accetta un flusso in ingresso `SpecifyUpsertConditions` e crea tre condizioni alter Row. Nella trasformazione precedente `alterRowCondition` viene calcolata una colonna denominata che determina se una riga viene inserita, aggiornata o eliminata nel database. Se il valore della colonna ha un valore stringa corrispondente alla regola alter Row, viene assegnato a tale criterio.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di alter Row](media/data-flow/alter-row4.png "Esempio di alter Row")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Passaggi successivi

Dopo la trasformazione alter Row, è possibile che si desideri eseguire il [sink dei dati in un archivio dati di destinazione](data-flow-sink.md).
