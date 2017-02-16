---
title: Codificare messaggi EDIFACT in App per la logica di Azure | Documentazione Microsoft
description: Come usare il codificatore EDIFACT in Enterprise Integration Pack con app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>Introduzione a Encode EDIFACT Message
Convalida le proprietà EDI e specifiche del partner. 

## <a name="prereqs"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode EDIFACT Message, è necessario un account di integrazione. Visualizzare i dettagli su come creare un [account integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), un [partner](logic-apps-enterprise-integration-partners.md) e un [contratto EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>Codificare messaggi EDIFACT
1. [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa, quindi immettere "EDIFACT" nella casella di ricerca.  Selezionare Encode EDIFACT Message by agreement name (Encode EDIFACT Message per nome contratto) o Encode to EDIFACT message by identities (Encode EDIFACT message per identità):
   
    ![ricerca di EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione:
   
    ![creare connessione all'account di integrazione](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie:
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
    Al termine i dettagli della connessione saranno simili ai seguenti:
   
    ![connessione all'account di integrazione](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. Selezionare **Crea**.
6. La connessione è stata creata:
   
    ![dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message by agreement name
Immettere il nome del contratto EDIFACT e il messaggio XML da codificare:
   
   ![specificare i campi obbligatori](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message by identities
Immettere identificatore e qualificatore del mittente e del ricevitore come configurati nel contratto EDIFACT. Selezionare il messaggio XML da codificare:  
    ![specificare i campi obbligatori](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Dettagli della codifica EDIFACT
Il connettore Encode EDIFACT esegue queste operazioni: 

* Risolvere il contratto associando il qualificatore e l'identificatore del mittente e il qualificatore e l'identificatore del ricevitore.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Genera un documento XML per ogni set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Come riconoscimento tecnico, il messaggio CONTRL indica la ricezione di un interscambio.
  * Come riconoscimento funzionale, il messaggio CONTRL indica l'accettazione o il rifiuto dell'interscambio, del gruppo o del messaggio ricevuto, con un elenco di errori o funzionalità non supportate.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


