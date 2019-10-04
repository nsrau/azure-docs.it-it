---
title: Messaggi AS2 per l'integrazione aziendale B2B-app per la logica di Azure
description: Scambiare messaggi AS2 in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 08/22/2019
ms.openlocfilehash: b1e7664aa08171c16c83e17ad93977b29e31b5c0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656416"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Scambiare messaggi AS2 per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Per lavorare con i messaggi AS2 in app per la logica di Azure, è possibile usare il connettore AS2, che fornisce trigger e azioni per la gestione della comunicazione AS2. Ad esempio, per stabilire sicurezza e affidabilità durante la trasmissione dei messaggi, è possibile usare le azioni seguenti:

* [Azione di **codifica AS2** ](#encode) per fornire la crittografia, la firma digitale e i riconoscimenti tramite notifiche sulla disposizione dei messaggi (MDN), che supportano il non ripudio. Questa azione, ad esempio, applica le intestazioni AS2/HTTP ed esegue queste attività quando viene configurata:

  * Firma i messaggi in uscita.
  * Crittografa i messaggi in uscita.
  * Comprime il messaggio.
  * Trasmette il nome del file nell'intestazione MIME.

* Azione di decodifica [ **AS2** ](#decode) per la decrittografia, la firma digitale e i riconoscimenti tramite notifiche sulla disposizione dei messaggi (MDN). Questa azione, ad esempio, esegue le attività seguenti:

  * Elabora le intestazioni AS2/HTTP.
  * Riconcilia gli MDN ricevuti con i messaggi in uscita originali.
  * Aggiorna e mette in correlazione i record nel database di non ripudio.
  * Scrive i record per la creazione di rapporti di stato AS2.
  * Restituisce il contenuto del payload come codificato in base 64.
  * Determina se i messaggi MDN sono obbligatori. In base all'accordo AS2, determina se i messaggi MDN devono essere sincroni o asincroni.
  * Genera MDN sincroni o asincroni basati sull'accordo AS2.
  * Imposta le proprietà e i token di correlazione su MDN.

  Questa azione esegue anche queste attività quando è configurata:

  * Verifica la firma.
  * Decrittografa i messaggi.
  * Decomprime il messaggio.
  * Verificare e impedire i duplicati degli ID messaggio.

Questo articolo illustra come aggiungere le azioni di codifica e decodifica AS2 a un'app per la logica esistente.

> [!IMPORTANT]
> Il connettore AS2 originale sarà deprecato, quindi assicurarsi di usare il connettore **AS2 (v2)** . Questa versione offre le stesse funzionalità della versione originale, è nativa per il runtime di app per la logica e offre miglioramenti significativi delle prestazioni in termini di velocità effettiva e dimensione dei messaggi. Inoltre, il connettore nativo V2 non richiede la creazione di una connessione all'account di integrazione. Al contrario, come descritto nei prerequisiti, assicurarsi di collegare l'account di integrazione all'app per la logica in cui si prevede di usare il connettore.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica da cui si vuole usare il connettore AS2 e un trigger che avvia il flusso di lavoro dell'app per la logica. Il connettore AS2 fornisce solo azioni, non trigger. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e collegato all'app per la logica in cui si prevede di usare il connettore AS2. Sia l'app per la logica che l'account di integrazione devono esistere nella stessa località o nell'area di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione utilizzando il qualificatore di identità AS2.

* Prima di poter utilizzare il connettore AS2, è necessario creare un [contratto](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 tra i partner commerciali e archiviare tale contratto nell'account di integrazione.

* Se si usa [Azure Key Vault](../key-vault/key-vault-overview.md) per la gestione dei certificati, verificare che le chiavi dell'insieme di credenziali consentano le operazioni di **crittografia** e decrittografia. In caso contrario, le azioni di codifica e decodifica avranno esito negativo.

  Nel portale di Azure passare all'insieme di credenziali delle chiavi, visualizzare le **operazioni**consentite della chiave dell'insieme di credenziali e verificare che le operazioni di **crittografia** e decrittografia siano selezionate.

  ![Controllare le operazioni di Key Vault](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2

1. Se non è già stato fatto, nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

1. Nella finestra di progettazione aggiungere una nuova azione all'app per la logica.

1. In **scegliere un'azione** e la casella di ricerca selezionare **tutti**. Nella casella di ricerca immettere "codifica AS2" e assicurarsi di selezionare l'azione AS2 (v2): **Codifica AS2**

   ![Selezionare "codifica AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Specificare ora le informazioni per queste proprietà:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Messaggio da codificare** | Payload del messaggio |
   | **AS2 da** | Identificatore del mittente del messaggio come specificato dall'accordo AS2 |
   | **Da AS2 a** | Identificatore del destinatario del messaggio come specificato dall'accordo AS2 |
   |||

   Esempio:

   ![Proprietà di codifica del messaggio](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificare i messaggi AS2

1. Se non è già stato fatto, nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

1. Nella finestra di progettazione aggiungere una nuova azione all'app per la logica.

1. In **scegliere un'azione** e la casella di ricerca selezionare **tutti**. Nella casella di ricerca immettere "Decode AS2" e assicurarsi di selezionare l'azione AS2 (v2): **Decodifica AS2**

   ![Selezionare "AS2 Decode"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Per il **messaggio da codificare** e le proprietà delle **intestazioni dei messaggi** , selezionare questi valori dal trigger precedente o dagli output dell'azione.

   Si supponga, ad esempio, che l'app per la logica riceva messaggi tramite un trigger di richiesta. È possibile selezionare gli output del trigger.

   ![Selezionare Corpo e Intestazioni dagli output della richiesta](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI (in precedenza spavalderia) del connettore, vedere la [pagina di riferimento del connettore](/connectors/as2/).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
