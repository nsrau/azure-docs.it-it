---
title: Trasformazione modifica riga flusso di dati mapping
description: Come aggiornare la destinazione del database utilizzando la trasformazione alter Row del flusso di dati del mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2019
ms.openlocfilehash: 1301b89ef1a6fb02356c6dcd4e568401eb5e9cd2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930472"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory trasformazione alter Row

Utilizzare la trasformazione alter Row per impostare i criteri INSERT, DELETE, Update e Upsert sulle righe. È possibile aggiungere condizioni uno-a-molti come espressioni. Queste condizioni devono essere specificate in ordine di priorità, in quanto ogni riga verrà contrassegnata con i criteri corrispondenti alla prima espressione corrispondente. Ognuna di queste condizioni può comportare l'inserimento, l'aggiornamento, l'eliminazione o la corretto di una riga o di righe. Alter Row può produrre entrambe le azioni DDL & DML nel database.



![Alter Row-impostazioni](media/data-flow/alter-row1.png "Alter Row-impostazioni")

> [!NOTE]
> Le trasformazioni alter Row funzioneranno solo sui sink di database o CosmosDB nel flusso di dati. Le azioni assegnate alle righe (Insert, Update, DELETE, Upsert) non vengono eseguite durante le sessioni di debug. È necessario aggiungere un'attività Esegui flusso di dati a una pipeline e utilizzare il debug o i trigger della pipeline per applicare i criteri alter Row nelle tabelle di database.

## <a name="indicate-a-default-row-policy"></a>Indicare un criterio di riga predefinito

Creare una trasformazione alter Row e specificare un criterio di riga con una condizione di `true()`. Ogni riga che non soddisfa le espressioni definite in precedenza verrà contrassegnata per i criteri di riga specificati. Per impostazione predefinita, ogni riga che non soddisfa alcuna espressione condizionale verrà contrassegnata per `Insert`.

![Modifica criteri riga uno](media/data-flow/alter-row4.png "Modifica criteri riga uno")

> [!NOTE]
> Per contrassegnare tutte le righe con un criterio, è possibile creare una condizione per quel criterio e specificare la condizione come `true()`.

## <a name="view-policies"></a>Visualizzare i criteri

Attivare la modalità di debug del flusso di dati per visualizzare i risultati dei criteri alter Row nel riquadro di anteprima dei dati. L'esecuzione di un'istruzione ALTER Row nella modalità di debug del flusso di dati non produrrà azioni DDL o DML sulla destinazione. Per eseguire tali azioni, eseguire il flusso di dati all'interno di un'attività Esegui flusso di dati all'interno di una pipeline.

![Alter Row (criteri)](media/data-flow/alter-row3.png "Alter Row (criteri)")

Ciò consentirà di verificare e visualizzare lo stato di ogni riga in base alle condizioni. L'icona rappresenta per ogni azione di inserimento, aggiornamento, eliminazione e Upsert che si verificherà nel flusso di dati, che indica l'azione che verrà eseguita quando si esegue il flusso di dati all'interno di una pipeline.

## <a name="sink-settings"></a>Impostazioni sink

Per il corretto funzionamento di alter Row è necessario disporre di un tipo di sink di database. Nelle impostazioni del sink è necessario impostare ogni azione corrispondente alle condizioni alter Row da consentire.

![Alter Row sink](media/data-flow/alter-row2.png "Alter Row sink")

Il comportamento predefinito nel flusso di dati di ADF con i sink di database consiste nell'inserire righe. Se si desidera consentire aggiornamenti, Upsert ed eliminazioni, è necessario controllare anche queste caselle nel sink per consentire le azioni.

> [!NOTE]
> Se inserimenti, aggiornamenti o Upsert modifica lo schema della tabella di destinazione nel sink, il flusso di dati avrà esito negativo. Per modificare lo schema di destinazione nel database, è necessario scegliere l'opzione "ricrea tabella" nel sink. Verrà eliminata e ricreata la tabella con la nuova definizione dello schema.

## <a name="next-steps"></a>Passaggi successivi

Dopo la trasformazione alter Row, è possibile che si desideri eseguire il [sink dei dati in un archivio dati di destinazione](data-flow-sink.md).
