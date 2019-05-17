---
title: Mapping di trasformazione Modifica righe del flusso di dati di Azure Data Factory
description: Come aggiornare la destinazione di database usando Azure Data Factory il Mapping di trasformazione dei dati del flusso Alter riga
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: f0ac5bb36079983b10e4d86cc776bd4e5ee6817d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520154"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory riga modifica trasformazione

Utilizzare la trasformazione di riga Alter per impostare i criteri di upsert, delete, update e insert in righe. È possibile aggiungere condizioni di uno-a-molti come espressioni. Ognuna di queste condizioni può determinare una riga (o righe) inserita, aggiornata, eliminata o upsert. Modifica riga può produrre le azioni di DDL e DML nel database.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Modificare le impostazioni di riga](media/data-flow/alter-row1.png "Alter Settings riga")

> [!NOTE]
> Trasformazioni di righe ALTER avrà effetto solo sui database sink nel flusso di dati. Le azioni che si assegna a righe (insert, update, delete, upsert) non verranno eseguita durante le sessioni di debug. È necessario aggiungere un'attività di esecuzione del flusso di dati a una pipeline e usare il debug di pipeline o i trigger per applicare i criteri di riga alter su tabelle di database.

## <a name="view-policies"></a>Visualizza i criteri

Passare alla modalità di flusso di eseguire il Debug dei dati su Sì e quindi visualizzare i risultati dei criteri alter riga nel riquadro di anteprima dei dati. L'esecuzione di una riga di modifica in modalità dati flusso di Debug non produrrà azioni DDL o DML rispetto alla destinazione. Per avere tali azioni si verificano, eseguire il flusso di dati all'interno di un'attività di esecuzione del flusso di dati all'interno di una pipeline.

![Modificare i criteri di riga](media/data-flow/alter-row3.png "modificare i criteri di riga")

In questo modo sarà possibile verificare e visualizzare lo stato di ogni riga in base alle condizioni. Icona rappresenta per ogni istruzione insert, upsert, delete e update azione che verrà eseguita nel flusso di dati, che indica l'azione verrà eseguita quando si esegue il flusso di dati all'interno di una pipeline.

## <a name="sink-settings"></a>Le impostazioni del sink

È necessario disporre di un database di tipo per riga Alter funzionamento di sink. Nel sink le impostazioni, è necessario impostare ogni azione deve essere autorizzato.

![ALTER sink di riga](media/data-flow/alter-row2.png "Alter Sink di riga")

Il comportamento predefinito nel flusso di dati di Azure Data factory con i sink di database consiste nell'inserire righe. Se si desidera consentire gli aggiornamenti, esegue l'Upsert e anche le eliminazioni, è necessario controllare anche queste finestre nel sink per consentire le azioni.

> [!NOTE]
> Se l'inserimenti, aggiornamenti o esegue l'Upsert modificarla lo schema della tabella di destinazione nel sink, il flusso di dati avrà esito negativo. Per modificare lo schema di destinazione nel database, è necessario scegliere l'opzione "Ricreare la tabella" nel sink. Questo verrà eliminare e ricreare la tabella con la nuova definizione di schema.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver della trasformazione Modifica righe, potrebbe voler [sink dei dati in un archivio dati di destinazione](data-flow-sink.md).
