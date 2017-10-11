---
title: Informazioni su come usare il connettore FTP nelle app per la logica | Documentazione Microsoft
description: "Creare app per la logica con Servizio app di Azure. Connettersi a un server FTP per gestire i file. Nel server FTP è possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 61bfbedfd4f1e84b6976099323a32f3a720634c0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-ftp-connector"></a>Introduzione al connettore FTP
Usare il connettore FTP per monitorare, gestire e creare file in un server FTP. 

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Connettersi a FTP
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-ftp"></a>Creare una connessione a FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Usare un trigger FTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!IMPORTANT]
> Il connettore FTP richiede un server FTP che sia accessibile da Internet e sia configurato per operare con la modalità passiva. Il connettore FTP, inoltre, **non è compatibile con FTPS implicito (FTP su SSL)**. Il connettore FTP supporta solo FTPS esplicito (FTP su SSL).  
> 
> 

Questo esempio spiega come usare il trigger **FTP - Quando viene aggiunto o modificato un file** per avviare un flusso di lavoro dell'app per la logica quando viene aggiunto o modificato un file in un server FTP. In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella FTP per nuovi file di ordini dei clienti.  È quindi possibile usare un'azione connettore FTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviarlo nel database degli ordini.

1. Immettere *ftp* nella casella di ricerca della finestra di progettazione delle app per la logica, quindi selezionare il trigger **FTP - Quando viene aggiunto o modificato un file**   
   ![Immagine di trigger FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Il controllo **Quando viene aggiunto o modificato un file** viene visualizzato.  
   ![Immagine di trigger FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Selezionare **...** sul lato destro del controllo. Viene visualizzato il controllo di selezione della cartella.  
   ![Immagine di trigger FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Selezionare **>** (freccia destra) e individuare la cartella da monitorare per rilevare i file nuovi o modificati. Selezionare la cartella e notare che ora è visualizzata nel controllo **Cartella**.  
   ![Immagine di trigger FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

A questo punto, l’app per la logica è stata configurata con un trigger che avvierà l'esecuzione di altri trigger e altre azioni nel flusso di lavoro quando un file viene modificato o creato nella cartella FTP specificata. 

> [!NOTE]
> Affinché sia funzionale, l'app per la logica deve contenere almeno un trigger e un'azione. Seguire i passaggi nella sezione successiva per aggiungere un'azione.  
> 
> 

## <a name="use-a-ftp-action"></a>Usare un'azione FTP
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Dopo aver aggiunto un trigger, seguire questi passaggi per aggiungere un'azione che otterrà il contenuto del file nuovo o modificato individuato dal trigger.    

1. Selezionare **+ Nuovo passaggio** per aggiungere l'azione che recupera il contenuto del file sul server FTP  
2. Selezionare il collegamento **Aggiungi un'azione** .  
   ![Immagine di azione FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Immettere *FTP* per cercare tutte le azioni correlate a FTP.
4. Selezionare **FTP - Recuperare i contenuti del file** come azione da eseguire quando viene individuato un file nuovo o modificato nella cartella FTP.      
   ![Immagine di azione FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Si apre il controllo **Recuperare i contenuti del file**. **Nota**: verrà richiesto di autorizzare l'app per la logica ad accedere all'account sul server FTP, se non lo si è già fatto in precedenza.  
   ![Immagine di azione FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Selezionare il controllo **File** (lo spazio vuoto sotto **FILE***). In questo caso è possibile usare le varie proprietà dal file nuovo o modificato individuato sul server FTP.  
6. Selezionare l'opzione **Contenuto file**.  
   ![Immagine di azione FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Il controllo viene aggiornato e indica che l'azione **FTP - Recuperare i contenuti del file** recupererà il *contenuto del file* nuovo o modificato nel server FTP.      
   ![Immagine di azione FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Salvare il lavoro e poi aggiungere un file nella cartella FTP per testare il flusso di lavoro.    

A questo punto l'app per la logica è stata configurata con un trigger per monitorare una cartella su un server FTP e avviare il flusso di lavoro quando viene individuato un nuovo file o un file modificato sul server FTP stesso. 

L'app per la logica è stata configurata con un'azione per ottenere il contenuto del file nuovo o modificato.

È ora possibile aggiungere un'altra azione, come [SQL Server - Inserisci riga](connectors-create-api-sqlazure.md), per inserire il contenuto del file nuovo o modificato in una tabella di database SQL.  

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)

