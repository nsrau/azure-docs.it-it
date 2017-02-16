---
title: Messaggi Encode AS2 nelle app per la logica di Azure | Documentazione Microsoft
description: Come usare il codificatore AS2 incluso con le app per la logica e con le app di Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Introduzione a Encode AS2 Message
Connettersi a Encode AS2 Message per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi. Offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi, con il conseguente supporto del database di non ripudio.

## <a name="prereqs"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode AS2 Message, è necessario un account di integrazione. Vedere i dettagli su come creare un [account integrazione](logic-apps-enterprise-integration-create-integration-account.md), [partner](logic-apps-enterprise-integration-partners.md) e un [contratto AS2](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2
1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa e quindi immettere "AS2" nella casella di ricerca.  Selezionare AS2 - Encode AS2 Message:
   
    ![Ricerca di AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione:
   
    ![creare una connessione all'account di integrazione](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie:
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
      Al termine i dettagli della connessione saranno simili ai seguenti:
   
      ![connessione di integrazione stabilita](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. Selezionare **Crea**.
6. La connessione è stata creata.  Specificare gli identificatori per AS2-From, AS2-To (come configurato nel contratto) e i dettagli del corpo (payload del messaggio):
   
    ![specificare i campi obbligatori](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Dettagli codificatore AS2
Il connettore Encode AS2 esegue queste operazioni: 

* Elabora le intestazioni AS2/HTTP
* Firma i messaggi in uscita (se configurata)
* Crittografa i messaggi in uscita (se configurata)
* Comprime i messaggi (se configurata)

## <a name="try-it-yourself"></a>Provare
Provare. Usare il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) per distribuire un'app per la logica completamente operativa.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


