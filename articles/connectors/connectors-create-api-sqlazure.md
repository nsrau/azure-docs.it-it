---
title: Aggiungere il connettore del database SQL di Azure alle app per la logica | Microsoft Docs
description: Panoramica del connettore del database SQL di Azure con i parametri dell'API REST
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Introduzione al connettore del database SQL di Azure
Usando il connettore del database SQL di Azure, creare flussi di lavoro per l'organizzazione che gestiscano i dati nelle tabelle. 

Con il database SQL è possibile:

* Creare il flusso di lavoro aggiungendo un nuovo cliente in un database di clienti o aggiornando un ordine in un database di ordini.
* Usare le azioni per ottenere una riga di dati, inserire una nuova riga e persino eliminare una riga. Ad esempio, quando viene creato un record in Dynamics CRM Online (trigger), inserire una riga in un database SQL di Azure (azione). 

Questo argomento illustra come usare il connettore database SQL in un'app per la logica ed elenca le azioni.

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Connettersi al database SQL di Azure
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi al database SQL, si crea una *connessione* al database SQL. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui connettersi. Pertanto, per creare la connessione al database SQL, immettere le credenziali del database SQL. 

#### <a name="create-the-connection"></a>Creare la connessione
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Usare un trigger
Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, come un trigger di ricorrenza, un trigger Webhook HTTP, i trigger disponibili con altri connettori e altri ancora. [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) illustra un esempio.

## <a name="use-an-action"></a>Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "sql" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. Nell'esempio scegliere **SQL Server - Ottieni riga**. Se esiste già una connessione, selezionare il **nome della tabella** dall'elenco a discesa e immettere l'**ID riga** che si vuole restituire.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-sqlazure.md#create-the-connection) di questo argomento descrive queste proprietà. 
   
   > [!NOTE]
   > In questo esempio si restituisce una riga da una tabella. Per visualizzare i dati in questa riga aggiungere un'altra azione che crea un file usando i campi della tabella. Ad esempio, aggiungere un'azione OneDrive che usa i campi Nome e Cognome per creare un nuovo file nell'account di archiviazione cloud. 
   > 
   > 
5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/sql/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).

