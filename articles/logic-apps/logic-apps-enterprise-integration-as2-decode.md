---
title: Messaggi Decode AS2 - App per la logica di Azure | Documentazione Microsoft
description: Messaggi Decode AS2 con App per la logica di Azure ed Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: ca297e1b4a007db3020b4369132b190608484738
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001627"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Messaggi Decode AS2 con App per la logica di Azure ed Enterprise Integration Pack 

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
* Seleziona e non consente ID messaggio duplicati (se configurata)
* Riconcilia una notifica sulla ricezione del messaggio ricevuta con il messaggio in uscita originale
* Aggiorna e mette in correlazione i record nel database di non ripudio
* Scrive i record per la creazione di report di stato su AS2
* Il contenuto del payload di output è codificato con codifica Base 64
* Determina se una notifica sulla ricezione del messaggio è obbligatoria, se deve essere sincrona o asincrona in base alla configurazione nel contratto AS2
* Genera una notifica sulla ricezione del messaggio sincrona o asincrona, in base alle configurazioni nel contratto
* Imposta le proprietà e il token di correlazione nella notifica sulla ricezione del messaggio


  > [!NOTE]
  > Se si usa Azure Key Vault per la gestione dei certificati, assicurarsi di configurare le chiavi per consentire l'operazione di **decrittografia**.
  > In caso contrario, la decodifica AS2 avrà esito negativo.
  >
  > ![Key Vault esegue la decrittografia](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Provare questo esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

