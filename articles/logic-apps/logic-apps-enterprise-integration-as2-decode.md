---
title: Messaggi Decode AS2 nelle app per la logica di Azure | Documentazione Microsoft
description: Come usare il decodificatore AS2 incluso con le app per la logica e di Enterprise Integration Pack
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
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>Introduzione alla decodifica di un messaggio AS2 nelle app per la logica
Connettersi a Decode AS2 Message per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi. Offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi.

## <a name="prereqs"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Decode AS2 Message, è necessario un account di integrazione. Vedere i dettagli su come creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md), [partner](logic-apps-enterprise-integration-partners.md) e un [contratto AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

## <a name="decode-as2-messages"></a>Decodificare i messaggi AS2
1. [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa e quindi immettere "AS2" nella casella di ricerca.  Selezionare AS2 - Decode AS2 Message:
   
    ![Ricerca di AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione:
   
    ![Create una connessione di integrazione](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie:
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
      Al termine, i dettagli della connessione saranno simili ai seguenti:
   
      ![connessione di integrazione](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. Selezionare **Crea**.
6. La connessione è stata creata.  A questo punto, procedere con gli altri passaggi nell'app per la logica:
   
    ![connessione di integrazione creata](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. Selezionare Corpo e Intestazioni dagli output della richiesta:
   
    ![specificare i campi obbligatori](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Dettagli del decodificatore AS2
Il connettore Decode AS2 esegue queste operazioni: 

* Elabora le intestazioni AS2/HTTP
* Verifica la firma (se configurata)
* Decrittografa i messaggi (se configurata)
* Decomprime i messaggi (se configurata)
* Riconcilia una notifica sulla ricezione del messaggio ricevuta con il messaggio in uscita originale
* Aggiorna e mette in correlazione i record nel database di non ripudio
* Scrive i record per la creazione di report di stato su AS2
* Il contenuto del payload di output è codificato con codifica Base&64;
* Determina se una notifica sulla ricezione del messaggio è obbligatoria, se deve essere sincrona o asincrona in base alla configurazione nel contratto AS2
* Genera una notifica sulla ricezione del messaggio sincrona o asincrona, in base alle configurazioni nel contratto
* Imposta le proprietà e il token di correlazione nella notifica sulla ricezione del messaggio

## <a name="try-it-yourself"></a>Provare
Provare. Usare il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) per distribuire un'app per la logica completamente operativa.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


