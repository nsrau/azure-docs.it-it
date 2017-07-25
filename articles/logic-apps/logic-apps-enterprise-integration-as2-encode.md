---
title: Messaggi Encode AS2 - App per la logica di Azure | Documentazione Microsoft
description: Come usare il codificatore AS2 in Enterprise Integration Pack in App per la logica di Azure
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
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7889bf9e4e02143b6bb4c797531afa54f8647ce5
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Messaggi Encode AS2 in App per la logica di Azure con Enterprise Integration Pack

Per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi, usare il connettore di messaggi Encode AS2. Questo connettore offre funzionalità di firma digitale, crittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi, con il conseguente supporto del database di non ripudio.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Encode AS2 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto AS2](logic-apps-enterprise-integration-as2.md) già definito nell'account di integrazione.

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).

2. Il connettore di messaggi Encode AS2 non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.  Nella casella di ricerca, immettere "AS2" come filtro. Selezionare **AS2 - Codifica in un messaggio AS2**.
   
    ![Cercare "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi. 
   
    ![creare una connessione all'account di integrazione](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.  Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.
   
    ![Dettagli della connessione di integrazione](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Dopo aver creato la connessione, come illustrato nell'esempio, specificare i dettagli per gli identificatori **AS2-From**, **AS2-To** come configurato nel contratto, e **Corpo**, ovvero il payload del messaggio.
   
    ![specificare i campi obbligatori](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Dettagli codificatore AS2

Il connettore Encode AS2 esegue queste attività: 

* Elabora le intestazioni AS2/HTTP
* Firma i messaggi in uscita (se configurata)
* Crittografa i messaggi in uscita (se configurata)
* Comprime i messaggi (se configurata)

## <a name="try-this-sample"></a>Provare questo esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 


