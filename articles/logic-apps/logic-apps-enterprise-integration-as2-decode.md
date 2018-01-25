---
title: Messaggi Decode AS2 - App per la logica di Azure | Documentazione Microsoft
description: Come usare il decodificatore AS2 in Enterprise Integration Pack in App per la logica di Azure
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
ms.author: LADocs; padmavc
ms.openlocfilehash: 4acae9f1837069c494985ff1456979490485f609
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Messaggi Decode AS2 in App per la logica di Azure con Enterprise Integration Pack 

Per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi, usare il connettore di messaggi Decode AS2. Questo connettore offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Decode AS2 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto AS2](logic-apps-enterprise-integration-as2.md) già definito nell'account di integrazione.

## <a name="decode-as2-messages"></a>Decodificare i messaggi AS2

1. [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Il connettore di messaggi Decode AS2 non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.  Nella casella di ricerca, immettere "AS2" come filtro. Selezionare **AS2 - Decodifica il messaggio AS2**.
   
    ![Cercare "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi.
   
    ![Create una connessione di integrazione](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.  Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.

    ![Dettagli della connessione di integrazione](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Dopo aver creato la connessione, come illustrato nell'esempio, selezionare **Corpo** e **Intestazioni** dagli output della richiesta.
   
    ![connessione di integrazione creata](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Ad esempio: 

    ![Selezionare Corpo e Intestazioni dagli output della richiesta](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Dettagli del decodificatore AS2

Il connettore Decode AS2 esegue queste attività: 

* Elabora le intestazioni AS2/HTTP
* Verifica la firma (se configurata)
* Decrittografa i messaggi (se configurata)
* Decomprime i messaggi (se configurata)
* Riconcilia una notifica sulla ricezione del messaggio ricevuta con il messaggio in uscita originale
* Aggiorna e mette in correlazione i record nel database di non ripudio
* Scrive i record per la creazione di report di stato su AS2
* Il contenuto del payload di output è codificato con codifica Base 64
* Determina se una notifica sulla ricezione del messaggio è obbligatoria, se deve essere sincrona o asincrona in base alla configurazione nel contratto AS2
* Genera una notifica sulla ricezione del messaggio sincrona o asincrona, in base alle configurazioni nel contratto
* Imposta le proprietà e il token di correlazione nella notifica sulla ricezione del messaggio

## <a name="try-this-sample"></a>Provare questo esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

