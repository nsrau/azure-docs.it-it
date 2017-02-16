---
title: Decodificare messaggi EDIFACT in App per la logica di Azure | Documentazione Microsoft
description: Come usare il decodificatore EDIFACT in Enterprise Integration Pack con app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 430a3add46053b5969597aa625df899f4d2e83f6


---

# <a name="get-started-with-decode-edifact-message"></a>Introduzione a Decode EDIFACT Message
Il connettore Decode EDIFACT convalida le proprietà EDI e specifiche del partner e genera un documento XML per ogni set di transazioni e un riconoscimento per ogni transazione elaborata.

## <a name="prereqs"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Decode EDIFACT Message, è necessario un account di integrazione. Vedere i dettagli per come creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md), [partner](logic-apps-enterprise-integration-partners.md) e un [contratto EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="decode-edifact-messages"></a>Decodificare messaggi EDIFACT
1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa, quindi immettere "EDIFACT" nella casella di ricerca.  Selezionare Decode EDIFACT Message:
   
    ![ricerca di EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione:
   
    ![creare un account di integrazione](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)  
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie:
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
    Al termine i dettagli della connessione saranno simili ai seguenti:
   
    ![account di integrazione creato](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  
5. Selezionare **Crea**.
6. La connessione è stata creata:
   
    ![dettagli della connessione all'account di integrazione](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  
7. Selezionare il messaggio con il file flat EDIFACT da decodificare:
   
    ![specificare i campi obbligatori](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Dettagli decodificatore EDIFACT
Il connettore Decode EDIFACT esegue queste operazioni: 

* Risolve il contratto associando qualificatore del mittente e identificatore e qualificatore del ricevitore e identificatore.
* Suddivide più interscambi in un singolo messaggio in messaggi separati.
* Convalida la busta in base all'accordo tra partner commerciali.
* Disassembla l'interscambio.
* Convalida le proprietà EDI e specifiche del partner, incluse
  * Convalida della struttura della busta dell'interscambio.
  * Convalida dello schema della busta in base allo schema di controllo.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
* Verifica che i numeri di controllo di un set di interscambio, gruppo e di transazioni non siano duplicati (se configurata). 
  * Controlla il numero di controllo dell'interscambio rispetto agli interscambi ricevuti in precedenza. 
  * Controlla il numero di controllo del gruppo con gli altri numeri di controllo del gruppo dell'interscambio. 
  * Controlla il numero di controllo del set di transazioni con gli altri numeri di controllo del set transazioni in tale gruppo.
* Genera un documento XML per ogni set di transazioni.
* Converte l'intero interscambio in XML. 
  * Suddivide l'interscambio in set di transazioni - Sospende i set di transazioni in caso di errore: analizza ogni set di transazioni di un interscambio in un documento XML separato. Se la convalida di uno o più set di transazioni dell'interscambio non riesce, EDIFACT Decode sospende solo i set di transazioni interessati. 
  * Suddivide l'interscambio in set di transazioni - Sospende l'interscambio in caso di errore: analizza ogni set di transazioni di un interscambio in un documento XML separato.  Se la convalida di uno o più set di transazioni dell'interscambio non riesce, EDIFACT Decode sospende l'intero interscambio.
  * Mantiene l'interscambio - Sospende i set transazioni in caso di errore: crea un documento XML per l'intero interscambio in batch. EDIFACT Decode sospende solo i set di transazioni che non superano la convalida, pur continuando a elaborare tutti gli altri set di transazioni.
  * Mantiene l'interscambio - Sospende l'interscambio in caso di errore: crea un documento XML per l'intero interscambio in batch. Se la convalida di uno o più set di transazioni dell'interscambio non riesce, EDIFACT Decode sospende l'intero interscambio. 
* Genera un riconoscimento tecnico (controllo) e/o funzionale (se configurata).
  * Un riconoscimento tecnico o CONTRL ACK segnala i risultati di un controllo sintattico dell'interscambio completo ricevuto.
  * Un riconoscimento funzionale riconosce l'accettazione o il rifiuto di un interscambio o un gruppo ricevuto.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


