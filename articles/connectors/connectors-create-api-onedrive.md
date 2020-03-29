---
title: Accedere e gestire file in Microsoft OneDrive
description: Caricare e gestire file in OneDrive creando flussi di lavoro automatizzati nelle app per la logica di AzureUpload and manage files in OneDrive by creating automated workflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378433"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Accedere e gestire i file nel connettore OneDrive tramite app per la logica di AzureAccess and manage files in OneDrive connector by using Azure Logic Apps

Usando [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il [connettore OneDrive,](/connectors/onedriveconnector/)è possibile creare attività e flussi di lavoro automatizzati per gestire i file, inclusi il caricamento, il get, l'eliminazione di file e altro ancora. Con OneDrive è possibile eseguire le attività seguenti:With OneDrive, you can perform these tasks:

* Creare un flusso di lavoro mediante l'archiviazione di file in OneDrive o aggiornare i file esistenti in OneDrive. 
* Usare trigger per avviare il flusso di lavoro quando un file viene creato o aggiornato in OneDrive.
* Usare le azioni per creare un file, eliminarlo e così via. Ad esempio, creare un nuovo file in OneDrive (azione) quando viene ricevuto un nuovo messaggio di posta elettronica di Office 365 con un allegato (trigger).

Questo articolo illustra come usare il connettore OneDrive in un'app per la logica ed elenca i trigger e le azioni.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-overview.md) e [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Connettersi a OneDrive

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a OneDrive, è necessaria prima di tutto una *connessione* a OneDrive. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Quindi, per creare la connessione a OneDrive, immettere le credenziali dell'account OneDrive.

### <a name="create-the-connection"></a>Creare la connessione

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Usare un trigger

Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. In Progettazione app `onedrive` per la logica digitare per ottenere un elenco dei trigger:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selezionare **Quando viene modificato un file**. Se esiste già una connessione, selezionare il pulsante Mostra selezione per selezionare una cartella.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-onedrive.md#create-the-connection) di questo articolo elenca i passaggi necessari.

   In questo esempio l'app per la logica viene eseguita quando viene aggiornato un file nella cartella scelta. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un messaggio di posta elettronica al proprio indirizzo. Ad esempio, aggiungere l'azione di Office 365 Outlook *Invia un messaggio di posta elettronica* per ricevere un messaggio di posta elettronica quando un file viene aggiornato.

3. Selezionare il pulsante **Modifica** e impostare i valori **Frequenza** e **Intervallo**. Ad esempio, se si vuole che il trigger esegua il poll ogni 15 minuti, impostare **Frequenza** su **Minuto** e **Intervallo** su **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="use-an-action"></a>Usare un'azione

Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Selezionare **Aggiungi un'azione**.

3. Nella casella di `onedrive` ricerca digitare per ottenere un elenco di tutte le azioni disponibili.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. Nell'esempio scegliere **OneDrive - Crea file**. Se esiste già una connessione, selezionare il **percorso della cartella** in cui inserire il file, immettere il **nome del file** e scegliere il **contenuto del file** desiderato:  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Se vengono richieste le informazioni di connessione, immettere i dettagli per [creare la connessione come descritto](#create-the-connection) in questo argomento.

   In questo esempio, si crea un nuovo file in una cartella oneDrive.In this example, you create a new file in a OneDrive folder. Per creare il file di OneDrive è possibile usare l'output di un altro trigger. Ad esempio aggiungere il trigger di Office 365 Outlook *All'arrivo di un nuovo messaggio di posta elettronica*. Quindi aggiungere l'azione di OneDrive *Crea file* che usa i campi Allegati e Content-Type in un ciclo ForEach per creare il nuovo file in OneDrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Passaggi successivi

* [Connettori per App per la logica di Azure](apis-list.md)