---
title: Aggiungere il connettore di archiviazione BLOB di Azure alle app per la logica | Microsoft Docs
description: Iniziare a configurare il connettore di archiviazione BLOB di Azure in un'app per la logica
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Usare il connettore di archiviazione BLOB di Azure in un'app per la logica
Usare il connettore di archiviazione BLOB di Azure per caricare, aggiornare, ottenere ed eliminare i BLOB nell'account di archiviazione, il tutto all'interno di un'app per la logica.  

Con Archiviazione BLOB di Azure:

* Il flusso di lavoro si crea caricando nuovi progetti o recuperando file aggiornati di recente.
* Le azioni consentono di ottenere i metadati del file, eliminare un file, copiare file e altro ancora. Ad esempio, quando viene aggiornato uno strumento in un sito Web di Azure (trigger), viene aggiornato un file nell'archivio BLOB (azione). 

Questo argomento illustra come usare il connettore di archiviazione BLOB in un'app per la logica.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Connettersi all'archivio BLOB di Azure
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a un account di archiviazione, si crea prima una *connessione* all'archivio BLOB. Per creare una connessione, immettere le credenziali usate normalmente per accedere al servizio a cui connettersi. Con Archiviazione di Azure immettere quindi le credenziali dell'account di archiviazione per creare la connessione. 

#### <a name="create-the-connection"></a>Creare la connessione
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Usare un trigger
Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, come un trigger di ricorrenza, un trigger Webhook HTTP, i trigger disponibili con altri connettori e altri ancora. [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) illustra un esempio.

## <a name="use-an-action"></a>Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica.

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "blob" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. Nell'esempio scegliere **AzureBlob - Ottieni metadati file in base al percorso**. Se esiste già una connessione, fare clic sul pulsante **...** (Mostra selezione) per selezionare un file.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-azureblobstorage.md#create-the-connection) di questo argomento descrive queste proprietà. 
   
   > [!NOTE]
   > In questo esempio si ottengono i metadati di un file. Per visualizzare i metadati, aggiungere un'altra azione che crea un nuovo file tramite un altro connettore. Ad esempio, aggiungere un'azione OneDrive che crea un nuovo file "test" in base ai metadati. 


5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

> [!TIP]
> [Storage Explorer](http://storageexplorer.com/) è lo strumento ideale per gestire più account di archiviazione.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).


