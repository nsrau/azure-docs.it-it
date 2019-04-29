---
title: Creare un flusso di dati di mapping di Azure Data Factory
description: Creare un flusso di dati di mapping di Azure Data Factory
author: WenJason
ms.author: v-jay
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
origin.date: 02/12/2019
ms.date: 04/22/2019
ms.openlocfilehash: bb6ae9f97d681625218118b8adca116de1c0fb21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883738"
---
# <a name="create-azure-data-factory-data-flow"></a>Creare un flusso di dati di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I flussi di dati di mapping in Azure Data Factory consentono di trasformare i dati su larga scala senza scrivere codice. È possibile progettare un processo di trasformazione dei dati nella finestra di progettazione del flusso dei dati costruendo una serie di trasformazioni. Iniziare con un numero qualsiasi di trasformazioni di origine seguite da passaggi di trasformazione dei dati. Completare quindi il flusso di dati con sink per indirizzare i risultati a una destinazione.

Per iniziare, creare una nuova data factory V2 dal portale di Azure. Dopo aver creato la nuova factory, fare clic sul riquadro "Author & Monitor" (Crea e monitora) per avviare l'interfaccia utente di Data Factory.

![Opzioni del flusso di dati](media/data-flow/v2dataflowportal.png "Creazione del flusso di dati")

Nell'interfaccia utente di Data Factory è possibile usare flussi di dati di esempio. Gli esempi sono disponibili dalla raccolta di modelli di Azure Data Factory. In Azure Data Factory creare una pipeline da modello ("Pipeline from Template") e selezionare la categoria del flusso di dati dalla raccolta di modelli.

![Opzioni del flusso di dati](media/data-flow/template.png "Creazione del flusso di dati")

Verrà richiesto di immettere le informazioni sull'account di Archiviazione BLOB di Azure.

![Opzioni del flusso di dati](media/data-flow/template2.png "Creazione del flusso di dati 2")

[I dati usati per questi esempi sono disponibili qui](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Scaricare i dati di esempio e archiviare i file negli account di Archiviazione BLOB di Azure in modo che sia possibile eseguire gli esempi.

## <a name="create-new-data-flow"></a>Crea nuovo flusso di dati

Usare il pulsante "segno più" Crea risorsa nell'interfaccia utente di Azure Data Factory per creare flussi di dati.

![Opzioni del flusso di dati](media/data-flow/newresource.png "Nuova risorsa")

## <a name="next-steps"></a>Passaggi successivi

Iniziare a creare la trasformazione dei dati con un [trasformazione sorgente](data-flow-source.md).
