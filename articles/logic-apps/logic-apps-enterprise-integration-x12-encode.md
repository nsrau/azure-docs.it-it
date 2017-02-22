---
title: Messaggi Encode X12 nelle app per la logica di Azure | Microsoft Docs
description: Utilizzo del codificatore X12 in Enterprise Integration Pack nelle app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>Introduzione a Encode X12 Message
Convalida le proprietà EDI e specifiche del partner, converte i messaggi con codifica XML in set di transazioni EDI nell'interscambio e richiede un riconoscimento tecnico e/o funzionale.

## <a name="prereqs"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode x12 Message, è necessario un account di integrazione. Vedere i dettagli su come creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md), i [partner](logic-apps-enterprise-integration-partners.md) e un [contratto X12](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>Messaggi Encode X12

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa, quindi immettere "x12" nella casella di ricerca.  Selezionare X12 - Encode X12 Message by agreement name (Messaggio Encode X12 per nome contratto) o X12 - Encode to X 12 message by identities (Codifica per messaggio X12 per identità):  
   
    ![ricerca x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione:
   
    ![connessione all'account di integrazione](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie:
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
    Al termine i dettagli della connessione saranno simili ai seguenti:
   
    ![connessione all'account di integrazione creata](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. Selezionare **Create**.
6. La connessione è stata creata.
   
    ![dettagli della connessione all'account di integrazione](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 Message by agreement name (Messaggio Encode X12 per nome contratto)
Selezionare il contratto X12 dall'elenco a discesa e il messaggio MXL da codificare:     
    ![specificare i campi obbligatori](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>Encode X12 Message by identities (Messaggio Encode X12 per identità)
Inserire identificatore del mittente, qualificatore del mittente, identificatore del destinatario e qualificatore del destinatario come configurati nel contratto X12.  Selezionare il messaggio XML da codificare:
   
   ![specificare i campi obbligatori](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Dettagli di Encode X12

Il connettore Encode X12 effettua le operazioni seguenti:

* Risoluzione del contratto associando le proprietà del contesto di mittente e del destinatario.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Un riconoscimento tecnico viene generato in seguito alla convalida dell'intestazione. Il riconoscimento tecnico segnala lo stato dell'elaborazione di un'intestazione e finale di interscambio in base all'indirizzo del ricevitore.
  * Un riconoscimento funzionale viene generato in seguito alla convalida del corpo. Il riconoscimento funzionale segnala ogni errore rilevato durante l'elaborazione del documento ricevuto.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


