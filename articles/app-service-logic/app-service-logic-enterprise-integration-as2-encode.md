---
title: Informazioni sul connettore Encode AS2 Message ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ebfefbafe6a47beb406bbea0f7c48b61a37ac151


---
# <a name="get-started-with-encode-as2-message"></a>Introduzione a Encode AS2 Message
Connettersi a Encode AS2 Message per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi. Offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi, con il conseguente supporto del database di non ripudio.

## <a name="create-the-connection"></a>Creare la connessione
### <a name="prerequisites"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode AS2 Message, è necessario un account di integrazione. Vedere i dettagli su come creare un [account integrazione](app-service-logic-enterprise-integration-create-integration-account.md), [partner](app-service-logic-enterprise-integration-partners.md) e un [contratto AS2](app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-encode-as2-message-using-the-following-steps"></a>Connettersi a Encode AS2 Message usando questa procedura:
1. [Creare un'app per la logica](app-service-logic-create-a-logic-app.md) illustra un esempio
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa e quindi immettere "AS2" nella casella di ricerca.  Selezionare AS2 - Encode AS2 Message.
   
    ![Ricerca di AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione.
   
    ![creare una connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)  
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie.
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
      Al termine, i dettagli della connessione saranno simili ai seguenti:
   
      ![connessione di integrazione stabilita](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)  
5. Selezionare **Crea**
6. La connessione è stata creata.  Fornire gli identificatori per AS2-From, AS2-To (come configurato nel contratto) e i dettagli del corpo (il payload del messaggio). 
   
    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## <a name="the-as2-encode-does-the-following"></a>AS2 Encode esegue queste operazioni
* Elabora le intestazioni AS2/HTTP
* Firma i messaggi in uscita (se configurata)
* Crittografa i messaggi in uscita (se configurata)
* Comprime i messaggi (se configurata)

## <a name="try-it-for-yourself"></a>Prova
Per fare una prova, fare clic [qui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) per distribuire un'app per la logica personalizzata completamente operativa usando le funzionalità AS2 di App per la logica.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


