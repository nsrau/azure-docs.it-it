---
title: Messaggi AS2 per l'integrazione aziendale B2B - App per la logica di Azure
description: Scambiare messaggi AS2 in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729405"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Scambiare messaggi AS2 per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Per lavorare con i messaggi AS2 nelle App per la logica di Azure, è possibile usare il connettore AS2, che fornisce i trigger e azioni per la gestione delle comunicazioni AS2. Per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi, ad esempio, è possibile usare queste azioni:

* [**Codifica in un messaggio AS2** azione](#encode) per fornire la crittografia, digitale firma e riconoscimenti tramite notifiche MDN (Message Disposition), che consentono il supporto di non ripudio. Ad esempio, questa azione si applica le intestazioni AS2/HTTP ed esegue queste attività quando è configurato:

  * Firma dei messaggi in uscita.
  * Consente di crittografare i messaggi in uscita.
  * Comprime il messaggio.
  * Trasmette il nome del file in intestazione MIME.

* [**Decodifica il messaggio AS2** azione](#decode) per fornire la decrittografia, firma digitale e riconoscimenti tramite notifiche MDN (Message Disposition). Ad esempio, questa azione esegue queste attività: 

  * Elabora le intestazioni AS2/HTTP.
  * Riconcilia gli MDN ricevuti con i messaggi in uscita originali.
  * Aggiorna e mette in correlazione i record nel database di non ripudio.
  * Scrive i record per la creazione rapporti di stato AS2.
  * Contenuto del payload di output come con codifica base64.
  * Determina se gli MDN sono necessari. Base AS2 contratto, determina se gli MDN devono essere sincrone o asincrone.
  * Genera MDN sincrono o asincrono basato su accordo AS2.
  * Imposta il token di correlazione e le proprietà sui messaggi MDN.

  Questa azione esegue anche queste attività quando è configurato:

  * Verifica la firma.
  * Decrittografa i messaggi.
  * Decomprime il messaggio. 
  * Controllare e non consentire duplicati di ID di messaggio.

Questo articolo illustra come aggiungere la codifica AS2 e le azioni di decodifica da un'app per la logica esistente.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica da cui si desidera usare il connettore AS2 e un trigger che avvia flusso di lavoro dell'app per la logica. Il connettore AS2 fornisce solo le azioni, non i trigger. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) che ha associato alla sottoscrizione di Azure e collegato all'app per la logica in cui si prevede di usare il connettore AS2. Sia l'account per la logica app e l'integrazione deve esistere nella stessa posizione o area di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) che già definiti nell'account di integrazione con il qualificatore di identità AS2.

* Prima è possibile usare il connettore AS2, è necessario creare un AS2 [contratto](../logic-apps/logic-apps-enterprise-integration-agreements.md) tra partner commerciali e archivio tale contratto nell'account di integrazione.

* Se si usa [Azure Key Vault](../key-vault/key-vault-overview.md) per la gestione dei certificati, verificare che le chiavi dell'insieme di credenziali consentano la **Encrypt** e **decrittografare** operazioni. In caso contrario, la codifica e decodifica azioni esito negativo.

  Nel portale di Azure, passare all'insieme di credenziali delle chiavi, visualizzare la chiave di insieme di credenziali **operazioni consentite**e verificare che il **Encrypt** e **decrittografare** operazioni siano selezionate.

  ![Controllare le operazioni di insieme di credenziali delle chiavi](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2

1. Se già stato fatto, nelle [portale di Azure](https://portal.azure.com), aprire l'app per la logica in Progettazione App per la logica.

1. Nella finestra di progettazione, aggiungere una nuova azione di app per la logica. 

1. Sotto **scegliere un'azione** e la ricerca, scegliere **tutte**. Nella casella di ricerca immettere "codifica as2" e selezionare l'azione: **Codifica in un messaggio AS2**.

   ![Selezionare "Encode al messaggio AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Se non si ha una connessione esistente all'account di integrazione, viene chiesto di creare la connessione a questo punto. Denominare la connessione, selezionare l'account di integrazione che si desidera connettersi e scegliere **Create**.

   ![creare connessione all'account di integrazione](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Includono ora informazioni per queste proprietà:

   | Proprietà | DESCRIZIONE |
   |----------|-------------|
   | **AS2-From** | L'identificatore per il mittente del messaggio come specificato per il contratto AS2 |
   | **AS2-To** | L'identificatore per il destinatario del messaggio come specificato per il contratto AS2 |
   | **body** | Il payload del messaggio |
   |||

   Ad esempio: 

   ![Proprietà codifica dei messaggi](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificare i messaggi AS2

1. Se già stato fatto, nelle [portale di Azure](https://portal.azure.com), aprire l'app per la logica in Progettazione App per la logica.

1. Nella finestra di progettazione, aggiungere una nuova azione di app per la logica. 

1. Sotto **scegliere un'azione** e la ricerca, scegliere **tutte**. Nella casella di ricerca immettere "decodifica as2" e selezionare l'azione: **Decodifica il messaggio AS2**

   ![Selezionare "Decode AS2 message"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Se non si ha una connessione esistente all'account di integrazione, viene chiesto di creare la connessione a questo punto. Denominare la connessione, selezionare l'account di integrazione che si desidera connettersi e scegliere **Create**.

   ![creare connessione all'account di integrazione](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Per la **corpo** e **intestazioni**, selezionare questi valori dall'output del trigger o azione precedente.

   Si supponga, ad esempio, che l'app per la logica riceve i messaggi tramite un trigger di richiesta. È possibile selezionare gli output da tale trigger.

   ![Selezionare Corpo e Intestazioni dagli output della richiesta](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e i limiti, come descritto dalla definizione OpenAPI del connettore (in precedenza Swagger), vedere la [pagina di riferimento del connettore](/connectors/as2/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
