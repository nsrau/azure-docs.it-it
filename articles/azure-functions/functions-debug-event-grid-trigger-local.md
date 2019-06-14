---
title: Debug locale della griglia di eventi di Funzioni di Azure
description: Informazioni sul debug locale delle Funzioni di Azure attivate da una Griglia di eventi
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funzioni di azure, funzioni, architettura serverless
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60428267"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Debug locale del trigger della Griglia di eventi di Funzioni di Azure

Questo articolo illustra come eseguire il debug di una funzione locale che gestisce un evento della Griglia di eventi di Azure generato da un account di archiviazione. 

## <a name="prerequisites"></a>Prerequisiti

- Creare o usare un'app per le funzioni esistente
- Creare o usare un account di archiviazione esistente
- Scaricare [ngrok](https://ngrok.com/) per consentire ad Azure di chiamare la funzione locale

## <a name="create-a-new-function"></a>Creare una nuova funzione

Aprire l'app per le funzioni in Visual Studio, fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e fare clic su **Aggiungi > Nuova funzione di Azure**.

Nella finestra *Nuova funzione di Azure* selezionare **Trigger griglia di eventi** e fare clic su **OK**.

![Creare una nuova funzione](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Una volta creata la funzione, aprire il file di codice e copiare l'URL commentato nella parte superiore del file. Questa posizione viene usata quando si configura il trigger della Griglia di eventi.

![Copiare la posizione](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Impostare quindi un punto di interruzione sulla linea che inizi con `log.LogInformation`.

![Set di punti di interruzione](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


**Premere F5** per avviare una sessione di debug.

## <a name="allow-azure-to-call-your-local-function"></a>Consentire ad Azure di chiamare la funzione locale

Per entrare in una funzione in fase di debug sul computer è necessario abilitare un modo in cui Azure può comunicare con la funzione locale dal cloud.

L'utilità [ngrok](https://ngrok.com/) fornisce un modo in cui Azure può chiamare la funzione in esecuzione nel computer. Avviare *ngrok* usando il comando seguente:

```bash
ngrok http -host-header=localhost 7071
```
Poiché l'utilità è impostata, la finestra di comando dovrebbe apparire simile allo screenshot seguente:

![Avviare ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Copiare l'URL **HTTPS** generato quando *ngrok* viene eseguito. Questo valore viene usato quando si configura l'endpoint dell'evento della griglia di eventi.

## <a name="add-a-storage-event"></a>Aggiungere un evento di archiviazione

Aprire il portale di Azure, passare a un account di archiviazione e fare clic sull'opzione **Eventi**.

![Aggiungere un evento dell'account di archiviazione](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Nella finestra *Eventi* fare clic sul pulsante **Sottoscrizione di eventi**. Nella finestra *Sottoscrizione di eventi* fare clic sull'elenco a discesa *Tipo di endpoint* e selezionare **Webhook**.

![Selezionare il tipo di sottoscrizione](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Dopo aver configurato il tipo di endpoint fare clic su **Select an endpoint** (Selezionare un endpoint) per configurare il valore dell'endpoint.

![Selezionare il tipo di endpoint](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Il valore *Endpoint sottoscrittore* è costituito da tre valori diversi. Il prefisso è l'URL HTTPS generato da *ngrok*. Il resto dell'URL proviene dall'URL trovato nel file del codice della funzione, con il nome della funzione aggiunto alla fine. Partendo dall'URL del file del codice della funzione, l'URL *ngrok* sostituisce `http://localhost:7071` e il nome della funzione sostituisce `{functionname}`.

L'URL finale dovrebbe essere simile allo screenshot seguente:

![Selezione dell'endpoint](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Dopo aver immesso il valore appropriato, fare clic su **Confermare la selezione**.

> [!IMPORTANT]
> Ogni volta che si avvia *ngrok*, l'URL HTTPS viene rigenerato e il valore cambia. Pertanto è necessario creare una nuova Sottoscrizione di eventi ogni volta che si espone la propria funzione ad Azure tramite *ngrok*.

## <a name="upload-a-file"></a>Caricare un file

Ora è possibile caricare un file nell'account di archiviazione per attivare un evento della Griglia di eventi per la funzione locale da gestire. 

Aprire [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e connettersi all'account di archiviazione. 

- Espandere **contenitori BLOB** 
- Fare clic con il pulsante destro del mouse su **Crea contenitore BLOB**.
- Denominare il **test** del contenitore
- Selezionare il contenitore *test*
- Fare clic sul pulsante **Carica**
- Fare clic su **Carica file**
- Selezionare un file e caricarlo nel contenitore BLOB

## <a name="debug-the-function"></a>Eseguire il debug della funzione

Una volta che la Griglia di eventi riconosce che un nuovo file viene caricato nel contenitore di archiviazione, il punto di interruzione viene raggiunto nella funzione locale.

![Avviare ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per ripulire le risorse create in questo articolo, elimina il contenitore di **test** nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare il ridimensionamento delle immagini caricate con la griglia di eventi](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Trigger Griglia di eventi per Funzioni di Azure](./functions-bindings-event-grid.md)
