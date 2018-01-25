---
title: 'Ripristino di emergenza per l''account di integrazione B2B: App per la logica di Azure | Microsoft Docs'
description: Ripristino di emergenza delle app per la logica B2B
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 0ef3965adac03f21c386765a43290c93d58d1c18
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Ripristino di emergenza tra più aree delle app per la logica B2B

I carichi di lavoro B2B coinvolgono le transazioni di denaro, come ad esempio gli ordini e le fatture. In caso di un evento di emergenza, per un'azienda è essenziale eseguire rapidamente un ripristino per soddisfare i contratti di servizio a livello di business secondo gli accordi presi con i partner. In questo articolo viene illustrato come creare un piano di continuità aziendale per i carichi di lavoro B2B. 

* Preparazione al ripristino di emergenza 
* Eseguire il failover all'area secondaria durante un evento di emergenza 
* Eseguire il failover all'area primaria dopo un evento di emergenza

## <a name="disaster-recovery-readiness"></a>Preparazione al ripristino di emergenza  

1. Identificare un'area secondaria e crearvi un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

2. Aggiungere partner, schemi e contratti per i flussi di messaggi richiesti dove lo stato di esecuzione deve essere replicato nell'account di integrazione dell'area secondaria.

   > [!TIP]
   > Assicurare la coerenza degli elementi dell'account di integrazione secondo la convenzione di denominazione nelle aree. 

3. Per eseguire il pull dello stato di esecuzione dall'area primaria, creare un'app per la logica nell'area secondaria, 

   che deve avere un *trigger* e un'*azione*. 
   Il trigger deve connettersi all'account di integrazione dell'area primaria, mentre l'azione deve connettersi all'account di integrazione dell'area secondaria. 
   In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella sullo stato di esecuzione dell'area primaria ed esegue il pull dei nuovi record, se presenti. L'azione li aggiorna nell'account di integrazione dell'area secondaria. 
   Questa operazione consente di ottenere lo stato di runtime incrementale dall'area primaria a quella secondaria.

4. La continuità aziendale nell'account di integrazione delle app per la logica è pensata per il supporto basato sui protocolli B2B - X12, AS2 ed EDIFACT. Per trovare la procedura dettagliata, selezionare i rispettivi collegamenti.

5. Si consiglia di distribuire tutte le risorse dell'area primaria anche in un'area secondaria. 

   Le risorse dell'area primaria includono il database SQL di Azure o Azure Cosmos DB, il bus di servizio di Azure e Hub eventi di Azure usati per la messaggistica, Gestione API di Azure e la funzionalità App per la logica di Azure di Servizio app di Azure.   

6. Stabilire una connessione dall'area primaria a quella secondaria. Per eseguire il pull dello stato di esecuzione dall'area primaria, creare un'app per la logica nell'area secondaria, 

   che deve avere un trigger e un'azione. 
   Il trigger deve connettersi all'account di integrazione dell'area primaria, mentre 
   l'azione deve connettersi all'account di integrazione dell'area secondaria. 
   In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella sullo stato di esecuzione dell'area primaria ed esegue il pull dei nuovi record, se presenti. 
   L'azione li aggiorna nell'account di integrazione dell'area secondaria. 
   Questa operazione consente di ottenere lo stato di runtime incrementale dall'area primaria a quella secondaria.

La continuità aziendale nell'account di integrazione delle app per la logica offre il supporto basato sui protocolli B2B X12, AS2 ed EDIFACT. Per informazioni dettagliate sull'uso di X12 e AS2, vedere [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) e [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) in questo articolo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Eseguire il failover all'area secondaria durante un evento di emergenza

Durante un evento di emergenza, quando l'area primaria non è disponibile per la continuità aziendale, dirigere il traffico verso l'area secondaria. Un'area secondaria consente a un'azienda di ripristinare rapidamente le funzioni per soddisfare gli RPO/RTO concordati con i partner. Riduce al minimo gli sforzi per il failover da un'area a un'altra. 

Esiste una latenza prevista durante la copia dei numeri di controllo dall'area primaria a quella secondaria. Per evitare l'invio ai partner di doppioni di numeri di controllo generati durante un evento di emergenza, è consigliabile incrementare i numeri di controllo negli accordi dell'area secondaria usando i [cmdlet di PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Eseguire il fallback all'area primaria dopo un evento di emergenza

Per eseguire il fallback a un'area primaria quando è disponibile, seguire questa procedura:

1. Interrompere l'accettazione dei messaggi dai partner nell'area secondaria.  

2. Incrementare i numeri di controllo generati per tutti i contratti dell'area primaria usando i [cmdlet di PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Indirizzare il traffico dall'area secondaria all'area primaria.

4. Controllare che l'app per la logica creata nell'area secondaria per il pull dello stato di esecuzione dall'area primaria sia abilitata.

## <a name="x12"></a>X12 

La continuità aziendale per i documenti EDI X12 si basa sui numeri di controllo:

> [!TIP]
> È anche possibile usare [il modello di avvio rapido X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) per creare app per la logica. La creazione degli account di integrazione primari e secondari è un prerequisito per l'uso del modello. Il modello consente di creare 2 app per la logica, una per i numeri di controllo ricevuti e l'altra per i numeri di controllo generati. I trigger e le azioni corrispondenti vengono creati nelle app per la logica collegando il trigger all'account di integrazione primario e l'azione a quello secondario.

**Prerequisiti**

Per abilitare il ripristino di emergenza per i messaggi in ingresso, selezionare le impostazioni per la verifica dei duplicati nelle impostazioni di ricezione dell'accordo X12.

![Selezionare le impostazioni per la verifica dei duplicati](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Creare un'[app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) nell'area secondaria.    

2. Cercare in **X12** e selezionare **X12 - Quando viene modificato un numero di controllo**.   

   ![Cercare X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Il trigger richiede di stabilire una connessione con l'account di integrazione. 
   Il trigger deve connettersi all'account di integrazione dell'area primaria.

3. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area primaria* dall'elenco e scegliere **Crea**.   

   ![Nome dell'account di integrazione dell'area primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. L'impostazione **DateTime per avviare la sincronizzazione dei numeri di controllo** è facoltativa. Il campo **Frequenza** può essere impostato su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.   

   ![DateTime e Frequenza](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Selezionare **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Nuovo passaggio, Aggiungi un'azione](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Cercare in **X12** e selezionare **X12 - Aggiungi o aggiorna numeri di controllo**.   

   ![Aggiungi o aggiorna numeri di controllo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Per collegare un'azione a un account di integrazione dell'area secondaria, selezionare **Modifica connessione** > **Aggiungi nuova connessione** per un elenco degli account di integrazione disponibili. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area secondaria* dall'elenco e scegliere **Crea**. 

   ![Nome dell'account di integrazione dell'area secondaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Passare agli input non elaborati facendo clic sull'icona nell'angolo in alto a destra.

   ![Passare agli input non elaborati](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Selezionare Corpo nell'utilità di selezione del contenuto dinamico e salvare l'app per la logica.

   ![Campi per il contenuto dinamico](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dei numeri di controllo ricevuti dell'area primaria ed esegue il pull dei nuovi record. 
   L'azione aggiorna i record nell'account di integrazione dell'area secondaria. 
   Se non ci sono aggiornamenti disponibili, lo stato del trigger appare come **Ignorato**.   

   ![Tabella dei numeri di controllo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

In base all'intervallo di tempo, lo stato di runtime incrementale viene replicato dall'area primaria a quella secondaria. Durante un evento di emergenza, quando l'area primaria non è disponibile, dirigere il traffico verso l'area secondaria per assicurare la continuità aziendale. 

## <a name="edifact"></a>EDIFACT 

La continuità aziendale per i documenti EDI EDIFACT si basa sui numeri di controllo.

**Prerequisiti**

Per abilitare il ripristino di emergenza per i messaggi in ingresso, selezionare le impostazioni per la verifica dei duplicati nelle impostazioni di ricezione dell'accordo EDIFACT.

![Selezionare le impostazioni per la verifica dei duplicati](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Creare un'[app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) nell'area secondaria.    

2. Cercare in **EDIFACT** e selezionare **EDIFACT - Quando viene modificato un numero di controllo**.

   ![Cercare EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Il trigger richiede di stabilire una connessione con l'account di integrazione. 
   Il trigger deve connettersi all'account di integrazione dell'area primaria. 

3. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area primaria* dall'elenco e scegliere **Crea**.    

   ![Nome dell'account di integrazione dell'area primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. L'impostazione **DateTime per avviare la sincronizzazione dei numeri di controllo** è facoltativa. Il campo **Frequenza** può essere impostato su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.    

   ![DateTime e Frequenza](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Selezionare **Nuovo passaggio** > **Aggiungi un'azione**.    

   ![Nuovo passaggio, Aggiungi un'azione](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Cercare in **EDIFACT** e selezionare **EDIFACT - Aggiungi o aggiorna numeri di controllo**.   

   ![Aggiungi o aggiorna numeri di controllo](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Per collegare un'azione a un account di integrazione dell'area secondaria, selezionare **Modifica connessione** > **Aggiungi nuova connessione** per un elenco degli account di integrazione disponibili. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area secondaria* dall'elenco e scegliere **Crea**.

   ![Nome dell'account di integrazione dell'area secondaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Passare agli input non elaborati facendo clic sull'icona nell'angolo in alto a destra.

   ![Passare agli input non elaborati](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Selezionare Corpo nell'utilità di selezione del contenuto dinamico e salvare l'app per la logica.   

   ![Campi per il contenuto dinamico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dei numeri di controllo ricevuti dell'area primaria ed esegue il pull dei nuovi record.
   L'azione aggiorna i record per l'account di integrazione dell'area secondaria. 
   Se non ci sono aggiornamenti disponibili, lo stato del trigger appare come **Ignorato**.

   ![Tabella dei numeri di controllo](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

In base all'intervallo di tempo, lo stato di runtime incrementale viene replicato dall'area primaria a quella secondaria. Durante un evento di emergenza, quando l'area primaria non è disponibile, dirigere il traffico verso l'area secondaria per assicurare la continuità aziendale. 

## <a name="as2"></a>AS2 

La continuità aziendale per i documenti che usano il protocollo AS2 si basai sull'ID di messaggio e sul valore MIC.

> [!TIP]
> È anche possibile usare [il modello di avvio rapido AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) per creare le app per la logica. La creazione degli account di integrazione primari e secondari è un prerequisito per l'uso del modello. Il modello consente di creare un'app per la logica, con un trigger e un'azione. L'app per la logica crea una connessione tra il trigger e l'account di integrazione primario e tra l'azione e l'account di integrazione secondario.

1. Creare un'[app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) nell'area secondaria.  

2. Cercare in **AS2** e selezionare **AS2 -When a MIC value is created** (AS2 - Quando viene creato un valore MIC).   

   ![Cercare AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Il trigger richiede di stabilire una connessione con l'account di integrazione. 
   Il trigger deve connettersi all'account di integrazione dell'area primaria. 
   
3. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area primaria* dall'elenco e scegliere **Crea**.

   ![Nome dell'account di integrazione dell'area primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. L'impostazione **DateTime per avviare la sincronizzazione del valore MIC** è facoltativa. Il campo **Frequenza** può essere impostato su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.   

   ![DateTime e Frequenza](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Selezionare **Nuovo passaggio** > **Aggiungi un'azione**.  

   ![Nuovo passaggio, Aggiungi un'azione](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Cercare in **AS2** e selezionare **AS2 - Add or update MIC contents** (AS2 - Aggiungi o aggiorna contenuti MIC).  

   ![Aggiunta o aggiornamento MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Per collegare un'azione a un account di integrazione secondario, selezionare **Modifica connessione** > **Aggiungi nuova connessione** per un elenco degli account di integrazione disponibili. Inserire un nome di connessione, selezionare l'*account di integrazione dell'area secondaria* dall'elenco e scegliere **Crea**.

   ![Nome dell'account di integrazione dell'area secondaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Passare agli input non elaborati facendo clic sull'icona nell'angolo in alto a destra.

   ![Passare agli input non elaborati](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Selezionare Corpo nell'utilità di selezione del contenuto dinamico e salvare l'app per la logica.   

   ![Contenuto dinamico](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dell'area primaria ed esegue il pull dei nuovi record. L'azione li aggiorna nell'account di integrazione dell'area secondaria. 
   Se non ci sono aggiornamenti disponibili, lo stato del trigger appare come **Ignorato**.  

   ![Tabella dell'area primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

In base all'intervallo di tempo, lo stato di runtime incrementale viene replicato dall'area primaria a quella secondaria. Durante un evento di emergenza, quando l'area primaria non è disponibile, dirigere il traffico verso l'area secondaria per assicurare la continuità aziendale. 

## <a name="next-steps"></a>Passaggi successivi

[Monitorare i messaggi B2B](logic-apps-monitor-b2b-message.md)

