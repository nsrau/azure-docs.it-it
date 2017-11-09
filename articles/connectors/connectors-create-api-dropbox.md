---
title: Connettore Dropbox per App per la logica di Azure | Microsoft Docs
description: "Creare app per la logica in Servizio app di Azure. Connettersi a Dropbox per gestire i file. In Dropbox è possibile eseguire diverse azioni, ad esempio caricare, aggiornare, ottenere ed eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-dropbox-connector"></a>Introduzione al connettore Dropbox
Connettersi a Dropbox per gestire i file. In Dropbox è possibile eseguire diverse azioni, ad esempio caricare, aggiornare, ottenere ed eliminare file.

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Connettersi a Dropbox
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a Dropbox creare prima di tutto una *connessione* a Dropbox. Per creare una connessione, è necessario fornire le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Nell'esempio relativo a Dropbox saranno quindi necessarie le credenziali dell'account di Dropbox per creare la connessione a Dropbox. [Altre informazioni sulle connessioni]()

### <a name="create-a-connection-to-dropbox"></a>Creare una connessione a Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Usare un trigger Dropbox
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

In questo esempio si userà il trigger **Quando viene creato un file**. Con questo trigger viene chiamata l'azione **Ottenere contenuto di file in base al percorso** di Dropbox. 

1. Immettere *dropbox* nella casella di ricerca della finestra di progettazione di app per la logica, quindi selezionare il trigger **Dropbox - Quando viene creato un file**.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Selezionare la cartella in cui tenere traccia della creazione di file. Selezionare... (identificato nella casella rossa) e passare alla cartella che si vuole selezionare per l'input del trigger.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Usare un'azione Dropbox
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Ora che il trigger è stato aggiunto, seguire questi passaggi per aggiungere un'azione che ottiene il contenuto del nuovo file.

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione da eseguire quando viene creato un nuovo file.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Selezionare **Aggiungi un'azione**. Viene aperta la casella di ricerca per cercare qualsiasi azione si desideri eseguire.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Immettere *dropbox* per cercare le azioni correlate a Dropbox.  
4. Selezionare **Dropbox - Ottenere contenuto di file in base al percorso** come azione da eseguire quando viene creato un nuovo file nella cartella di Dropbox selezionata. Si apre il blocco di controllo azione. Verrà richiesto di autorizzare l'app per la logica ad accedere all'account di Dropbox, se non è stato fatto in precedenza.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Selezionare... sul lato destro del controllo **Percorso file** e passare al percorso file che si vuole usare. In alternativa, usare il token **file path** per rendere più rapida la creazione di app per la logica.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Salvare il lavoro e creare un nuovo file in Dropbox per attivare il flusso di lavoro.  

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/dropbox/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).