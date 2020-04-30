---
title: Trasformazione alter Row nel flusso di dati del mapping
description: Come aggiornare la destinazione del database utilizzando la trasformazione alter Row nel flusso di dati di mapping
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729129"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Trasformazione alter Row nel flusso di dati del mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione alter Row per impostare i criteri INSERT, DELETE, Update e Upsert sulle righe. È possibile aggiungere condizioni uno-a-molti come espressioni. Queste condizioni devono essere specificate in ordine di priorità, in quanto ogni riga verrà contrassegnata con i criteri corrispondenti alla prima espressione corrispondente. Ognuna di queste condizioni può comportare l'inserimento, l'aggiornamento, l'eliminazione o la corretto di una riga o di righe. Alter Row può produrre entrambe le azioni DDL & DML nel database.

![Alter Row-impostazioni](media/data-flow/alter-row1.png "Alter Row-impostazioni")

Le trasformazioni alter Row funzioneranno solo sui sink di database o CosmosDB nel flusso di dati. Le azioni assegnate alle righe (Insert, Update, DELETE, Upsert) non vengono eseguite durante le sessioni di debug. Eseguire un'attività Esegui flusso di dati in una pipeline per applicare i criteri alter Row nelle tabelle di database.

## <a name="specify-a-default-row-policy"></a>Specificare un criterio di riga predefinito

Creare una trasformazione alter Row e specificare un criterio di `true()`riga con la condizione. Ogni riga che non corrisponde ad alcuna espressione definita in precedenza verrà contrassegnata per i criteri di riga specificati. Per impostazione predefinita, ogni riga che non corrisponde ad alcuna espressione condizionale verrà `Insert`contrassegnata per.

![Alter Row Policy](media/data-flow/alter-row4.png "Alter Row Policy")

> [!NOTE]
> Per contrassegnare tutte le righe con un criterio, è possibile creare una condizione per quel criterio e specificare la `true()`condizione come.

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

Nell'esempio seguente viene illustrata una trasformazione ALTER `CleanData` Row denominata che accetta un flusso `SpecifyUpsertConditions` in ingresso e crea tre condizioni alter Row. Nella trasformazione precedente viene calcolata una colonna `alterRowCondition` denominata che determina se una riga viene inserita, aggiornata o eliminata nel database. Se il valore della colonna ha un valore stringa corrispondente alla regola alter Row, viene assegnato a tale criterio.

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
