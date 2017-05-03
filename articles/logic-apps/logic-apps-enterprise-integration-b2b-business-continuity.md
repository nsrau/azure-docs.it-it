---
title: Ripristino di emergenza dell&quot;account per l&quot;integrazione B2B delle app per la logica - App per la logica di Azure | Microsoft Docs
description: Ripristino di emergenza delle app per la logica B2B di Azure
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Ripristino di emergenza da più aree delle app per la logica B2B
I carichi di lavoro B2B coinvolgono le transazioni di denaro, come ad esempio gli ordini e le fatture.  Per l'azienda è essenziale eseguire rapidamente un ripristino per soddisfare i contratti di servizio a livello di business secondo gli accordi presi con i partner durante un evento di emergenza.  In questo argomento viene illustrata la creazione di un piano di continuità aziendale per i carichi di lavoro B2B.  Contenuti

* Creare un account di integrazione nell'area secondaria
* Stabilire una connessione dall'area primaria a quella secondaria 
* Eseguire il failover all'area secondaria durante un evento di emergenza
* Eseguire il failover all'area primaria dopo un evento di emergenza

## <a name="create-an-integration-account-in-secondary-region"></a>Creare un account di integrazione nell'area secondaria
1. Selezionare un'area secondaria e creare un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).  

2. Aggiungere partner, schemi e contratti per i flussi di messaggi richiesti dove lo stato di esecuzione deve essere replicato nell'account di integrazione dell'area secondaria.

    > [!Tip]
    > Assicurare la coerenza degli elementi dell'account di integrazione secondo la convenzione di denominazione nelle aree 
    > 
    > 

3. È consigliabile distribuire tutte le risorse dell'area primaria (ad esempio i database SQL Azure o DocumentDB, oppure gli hub eventi/bus di servizio usati per la messaggistica, l'APIM, le app per la logica) anche nell'area secondaria.  

## <a name="establish-a-connection-from-primary-to-secondary"></a>Stabilire una connessione dall'area primaria a quella secondaria 
Per eseguire il pull dello stato di esecuzione dall'area primaria, creare un'app per la logica nell'area secondaria,  che deve avere un **trigger** e un'**azione**.  Il trigger deve connettersi all'account di integrazione dell'area primaria, mentre l'azione deve connettersi all'account di integrazione dell'area secondaria.  In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella sullo stato di esecuzione dell'area primaria ed esegue il pulling dei nuovi record (se presenti), mentre l'azione li aggiorna nell'account di integrazione dell'area secondaria. Questa operazione consente di ottenere lo stato di runtime incrementale dall'area primaria a quella secondaria.

La continuità aziendale nell'account di integrazione delle app per la logica è pensata per il supporto basato sui protocolli B2B - X12, AS2 ed EDIFACT.  Per trovare la procedura dettagliata, selezionare i rispettivi collegamenti.

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (presto disponibile)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>Eseguire il failover all'area secondaria durante un evento di emergenza
Durante un evento di emergenza, quando l'area primaria non è disponibile per la continuità aziendale, dirigere il traffico verso l'area secondaria. L'area secondaria consente di ripristinare rapidamente le funzioni aziendali per soddisfare gli obiettivi di tempo/punto di ripristino (RPO/RTO) come concordato con i partner.  Inoltre, riduce al minimo gli sforzi per il failover da un'area a un'altra. 

Esiste una latenza prevista durante la copia dei numeri di controllo dall'area primaria a quella secondaria.  Per evitare l'invio ai partner di doppioni di numeri di controllo generati durante un evento di emergenza, è consigliabile promuovere i numeri di controllo nei **contratti dell'area secondaria** usando i [cmdlet di PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-primary-region-post-disaster-event"></a>Eseguire il failover all'area primaria dopo un evento di emergenza
Quando l'area primaria è disponibile, eseguire il fallback all'area primaria attenendosi alla procedura seguente     
* Interrompere l'accettazione dei messaggi dai partner nell'**area secondaria**   
* Promuovere i numeri di controllo generati per tutti i **contratti dell'area primaria** usando i [cmdlet di PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)   
* Dirigere il traffico dall'area secondaria a quella primaria   
* Controllare che l'app per la logica creata nell'area secondaria per eseguire il pulling dello stato di esecuzione dall'area primario sia abilitata    

## <a name="x12"></a>X12 
La continuità aziendale per i documenti EDI X12 è stata progettata basandosi sui numeri di controllo   
* Numeri di controllo ricevuti (messaggi in ingresso) dai partner  
* Numeri di controllo generati (messaggi in uscita) e inviati ai partner  
    
    > [!Tip]
    > È inoltre possibile usare [il modello di avvio rapido X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) per creare app per la logica.  La creazione degli account di integrazione primari e secondari è un prerequisito per l'uso del modello.  Il modello consente di creare 2 app per la logica, una per i numeri di controllo ricevuti e l'altra per i numeri di controllo generati.  I trigger e le azioni corrispondenti vengono creati nelle app per la logica; il trigger si connette all'account di integrazione primario e l'azione si connette a quello secondario.
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>Numeri di controllo ricevuti dai partner
1. Creare un'[app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) nell'area secondaria   

2. Cercare **X12** e selezionare **X12, quando viene modificato un numero di controllo ricevuto**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. Selezionare i prompt del trigger per stabilire una connessione con l'account di integrazione. Il trigger da connettere all'account di integrazione dell'area primaria.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area primaria** dall'elenco e fare clic su Crea.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. Il valore DateTime per avviare la sincronizzazione dei numeri di controllo è facoltativo.  La frequenza può essere impostata su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. Fare clic su **Nuovo passaggio** e su **Aggiungi un'azione**    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. Cercare **X12** e selezionare **X12, per aggiungere o aggiornare un numero di controllo ricevuto**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. Azione da collegare all'account di integrazione secondario.  Selezionare **Cambia connessione** e **Aggiungi nuova connessione** elenca gli account di integrazione disponibili.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area secondaria** dall'elenco e fare clic su Crea.     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. Selezionare il contenuto dinamico e salvare l'app per la logica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dei numeri di controllo dell'area primaria ed esegue il pulling dei nuovi record, mentre l'azione li aggiorna nell'account di integrazione dell'area secondaria.  Se non ci sono aggiornamenti disponibili, lo stato del trigger viene ignorato.
    
    > [!Tip]
    > L'abilitazione della verifica dei duplicati nelle impostazioni di ricezione del contratto mantiene lo stato di runtime dei numeri di controllo ricevuti e aiuta ad attivare i trigger a intervalli regolari
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>Numeri di controllo generati e inviati ai partner
1. Creare un'[app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) nell'area secondaria  

2. Cercare **X12** e selezionare **X12, quando viene modificato un numero di controllo generalo**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. Selezionare i prompt del trigger per stabilire una connessione con l'account di integrazione. Il trigger da connettere all'account di integrazione dell'area primaria.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area primaria** dall'elenco e fare clic su Crea.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. Il valore DateTime per avviare la sincronizzazione dei numeri di controllo è facoltativo.  La frequenza può essere impostata su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Fare clic su **Nuovo passaggio** e su **Aggiungi un'azione**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Cercare **X12** e selezionare **X12, per aggiungere o aggiornare un numero di controllo generato**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Azione da collegare all'account di integrazione secondario.  Selezionare **Cambia connessione** e **Aggiungi nuova connessione** elenca gli account di integrazione disponibili.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area secondaria** dall'elenco e fare clic su Crea.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Selezionare il contenuto dinamico e salvare l'app per la logica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dei numeri di controllo dell'area primaria ed esegue il pulling dei nuovi record, mentre l'azione li aggiorna nell'account di integrazione dell'area secondaria.  Se non ci sono aggiornamenti disponibili, lo stato del trigger viene ignorato.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

In base all'intervallo di tempo, lo stato di runtime incrementale viene replicato dall'area primaria a quella secondaria.  Durante un evento di emergenza, quando l'area primaria non è disponibile, dirigere il traffico verso l'area secondaria per assicurare la continuità aziendale. 

## <a name="as2"></a>AS2 
La continuità aziendale per i documenti che usano il protocollo AS2 è stata progetta basandosi sull'ID di messaggio e sul valore MIC

> [!Tip]
    > È inoltre possibile usare [il modello di avvio rapido AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) per creare app per la logica.  La creazione degli account di integrazione primari e secondari è un prerequisito per l'uso del modello.  Il modello consente di creare un'app per la logica, con un trigger e un'azione.  Crea una connessione tra il trigger e l'account di integrazione primario e tra l'azione e l'account di integrazione secondario.
    > 
    >

1. Creare un'[app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) nell'area secondaria  

2. Cercare **AS2** e selezionare **AS2, quando viene creato un valore MIC**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. Selezionare i prompt del trigger per stabilire una connessione con l'account di integrazione. Il trigger da connettere all'account di integrazione dell'area primaria.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area primaria** dall'elenco e fare clic su Crea.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. Il valore DateTime per avviare la sincronizzazione del valore MIC è facoltativo.  La frequenza può essere impostata su **Giorno**, **Ora**, **Minuto** o **Secondo** con un intervallo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Fare clic su **Nuovo passaggio** e su **Aggiungi un'azione**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Cercare **AS2** e selezionare **AS2 per aggiungere o aggiornare un MIC**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Azione da collegare all'account di integrazione secondario.  Selezionare **Cambia connessione** e **Aggiungi nuova connessione** elenca gli account di integrazione disponibili.  Assegnare un nome di connessione, selezionare l'**account di integrazione dell'area secondaria** dall'elenco e fare clic su Crea.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Selezionare il contenuto dinamico e salvare l'app per la logica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. In base all'intervallo di tempo, il trigger esegue il sondaggio della tabella dell'area primaria ed esegue il pulling dei nuovi record (se presenti), mentre l'azione li aggiorna nell'account di integrazione dell'area secondaria.  Se non ci sono aggiornamenti disponibili, lo stato del trigger viene ignorato.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

In base all'intervallo di tempo, lo stato di runtime incrementale viene replicato dall'area primaria a quella secondaria.  Durante un evento di emergenza, quando l'area primaria non è disponibile, dirigere il traffico verso l'area secondaria per assicurare la continuità aziendale. 

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md).   
