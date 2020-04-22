---
title: Modificare la trasformazione di riga nel mapping del flusso di datiAlter row transformation in mapping data flow
description: Come aggiornare la destinazione del database utilizzando la trasformazione Modifica riga nel mapping del flusso di datiHow to update database target using the alter row transformation in mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729129"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Modificare la trasformazione di riga nel mapping del flusso di datiAlter row transformation in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione Altera riga per impostare i criteri di inserimento, eliminazione, aggiornamento e backup nelle righe. È possibile aggiungere condizioni uno-a-molti come espressioni. Queste condizioni devono essere specificate in ordine di priorità, poiché ogni riga verrà contrassegnata con il criterio corrispondente all'espressione corrispondente. Ognuna di queste condizioni può comportare l'inserimento, l'aggiornamento, l'eliminazione o l'upserted di una riga o di righe. Alter Row può produrre entrambe le azioni DDL & DML sul database.

![Modificare le impostazioni delle righe](media/data-flow/alter-row1.png "Modifica impostazioni riga")

Le trasformazioni Alter Row opereranno solo su database o sink CosmosDB nel flusso di dati. Le azioni assegnate alle righe (inserimento, aggiornamento, eliminazione, upsert) non verranno eseguite durante le sessioni di debug. Eseguire un'attività Esegui flusso di dati in una pipeline per attuare i criteri di modifica delle righe nelle tabelle del database.

## <a name="specify-a-default-row-policy"></a>Specificare un criterio di riga predefinitoSpecify a default row policy

Creare una trasformazione Altera riga e `true()`specificare un criterio di riga con una condizione di . Ogni riga che non corrisponde a nessuna delle espressioni definite in precedenza verrà contrassegnata per i criteri di riga specificati. Per impostazione predefinita, ogni riga che non corrisponde `Insert`ad alcuna espressione condizionale verrà contrassegnata per .

![Modificare i criteri di riga](media/data-flow/alter-row4.png "Modificare i criteri di riga")

> [!NOTE]
> Per contrassegnare tutte le righe con un criterio, è possibile `true()`creare una condizione per tale criterio e specificare la condizione come .

## <a name="view-policies-in-data-preview"></a>Visualizzare i criteri nell'anteprima dei datiView policies in data preview

Utilizzare la [modalità di debug](concepts-data-flow-debug-mode.md) per visualizzare i risultati dei criteri di modifica riga nel riquadro di anteprima dei dati. Un'anteprima dei dati di una trasformazione di riga di modifica non produrrà azioni DDL o DML contro la destinazione.

![Modificare i criteri di riga](media/data-flow/alter-row3.png "Alterare i criteri di riga")

Ogni criterio di modifica riga è rappresentato da un'icona che indica se verrà eseguita un'azione di inserimento, aggiornamento, upsert o eliminazione. L'intestazione superiore mostra il numero di righe interessate da ogni criterio nell'anteprima.

## <a name="allow-alter-row-policies-in-sink"></a>Consenti criteri di alterazione riga nel sinkAllow alter row policies in sink

Affinché i criteri di modifica delle righe funzionino, il flusso di dati deve scrivere in un database o in un sink Cosmos. Nella scheda **Impostazioni** del sink abilitare i criteri di modifica delle righe consentiti per tale sink.

![Altera sink di riga](media/data-flow/alter-row2.png "Altera sink di riga")

Il comportamento predefinito prevede che vengano consentiti solo gli inserimenti. Per consentire aggiornamenti, upsert o eliminazioni, selezionare la casella nel sink corrispondente a tale condizione. Se gli aggiornamenti, gli upsert o le eliminazioni sono abilitati, è necessario specificare in quali colonne chiave del sink trovare la corrispondenza.

> [!NOTE]
> Se gli inserimenti, gli aggiornamenti o gli upsert modificano lo schema della tabella di destinazione nel sink, il flusso di dati avrà esito negativo. Per modificare lo schema di destinazione nel database, scegliere **Ricrea tabella** come azione tabella. In questo modo la tabella verrà rilasciata e ricreata con la nuova definizione dello schema.

La trasformazione sink richiede una singola chiave o una serie di chiavi per l'identificazione di riga univoca nel database di destinazione. Per i sink SQL, impostare le chiavi nella scheda Impostazioni sink. Per CosmosDB, impostare la chiave di partizione nelle impostazioni e anche il campo di sistema CosmosDB "id" nella mappatura sink. Per CosmosDB, è obbligatorio includere la colonna di sistema "id" per gli aggiornamenti, upserts, e le eliminazioni.

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

L'esempio seguente è una `CleanData` trasformazione di `SpecifyUpsertConditions` riga di modifica denominata che accetta un flusso in ingresso e crea tre condizioni di riga di modifica. Nella trasformazione precedente viene `alterRowCondition` calcolata una colonna denominata che determina se una riga viene inserita, aggiornata o eliminata nel database. Se il valore della colonna ha un valore stringa che corrisponde alla regola di modifica della riga, gli viene assegnato tale criterio.

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Esempio di modifica riga](media/data-flow/alter-row4.png "Esempio di modifica riga")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Passaggi successivi

Dopo la trasformazione Alter row, è possibile [inserirli in un archivio dati](data-flow-sink.md)di destinazione.
