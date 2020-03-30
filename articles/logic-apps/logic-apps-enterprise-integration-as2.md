---
title: Inviare e ricevere messaggi AS2 per B2BSend and receive AS2 messages for B2B
description: Messaggi AS2 di Exchange per scenari di integrazione aziendale B2B tramite App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650564"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Scambiare messaggi AS2 per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

> [!IMPORTANT]
> Il connettore AS2 originale è deprecato, quindi assicurati di utilizzare il connettore **AS2 (v2).** Questa versione offre le stesse funzionalità della versione originale, è nativa del runtime di App per la logica e offre miglioramenti significativi delle prestazioni in termini di velocità effettiva e dimensione dei messaggi. Inoltre, il connettore v2 nativo non richiede la creazione di una connessione all'account di integrazione. Invece, come descritto nei prerequisiti, assicurarsi di collegare l'account di integrazione all'app per la logica in cui si prevede di usare il connettore.

Per usare i messaggi AS2 in App per la logica di Azure, è possibile usare il connettore AS2, che fornisce trigger e azioni per la gestione delle comunicazioni AS2. Ad esempio, per stabilire la sicurezza e l'affidabilità durante la trasmissione dei messaggi, è possibile utilizzare le seguenti azioni:

* [ **Azione di codifica AS2** ](#encode) per fornire crittografia, firma digitale e riconoscimenti tramite le notifiche di disposizione dei messaggi (MDN), che consentono di supportare il non ripudio. Ad esempio, questa azione applica le intestazioni AS2/HTTP ed esegue queste attività quando viene configurata:For example, this action applies AS2/HTTP headers and performs these tasks when configured:

  * Firma i messaggi in uscita.
  * Crittografa i messaggi in uscita.
  * Comprime il messaggio.
  * Trasmette il nome del file nell'intestazione MIME.

* [ **L'azione** ](#decode) di decodifica AS2 per fornire la decrittografia, la firma digitale e i riconoscimenti tramite le notifiche di disposizione dei messaggi (MDN). Ad esempio, questa azione esegue queste attività:For example, this action performs these tasks:

  * Elabora le intestazioni AS2/HTTP.
  * Riconcilia gli MDN ricevuti con i messaggi in uscita originali.
  * Aggiorna e correla i record nel database di non ripudio.
  * Scrive i record per la creazione di relazioni sullo stato AS2.
  * Restituisce il contenuto del payload come codifica base64.
  * Determina se gli MDN sono necessari. In base all'accordo AS2, determina se gli MDN devono essere sincroni o asincroni.
  * Genera MDN sincroni o asincroni in base all'accordo AS2.
  * Imposta i token di correlazione e le proprietà sulle MDN.

  Questa azione esegue anche queste attività quando viene configurata:This action also performs these tasks when configured:

  * Consente di verificare la firma.
  * Decrittografa i messaggi.
  * Decomprime il messaggio.
  * Controllare e non consentire duplicati dell'ID messaggio.

Questo articolo illustra come aggiungere le azioni di codifica e decodifica AS2 a un'app per la logica esistente.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica da cui vuoi usare il connettore AS2 e un trigger che avvia il flusso di lavoro dell'app per la logica. Il connettore AS2 fornisce solo azioni, non trigger. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e collegato all'app per la logica in cui si prevede di usare il connettore AS2. Sia l'app per la logica che l'account di integrazione devono esistere nello stesso percorso o nell'area di Azure.Both logic app and integration account must exist in the same location or Azure region.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione tramite il qualificatore di identità AS2.

* Prima di poter utilizzare il connettore AS2, è necessario creare un [accordo](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 tra i partner commerciali e archiviare tale contratto nell'account di integrazione.

* Se si usa [L'insieme di credenziali delle chiavi](../key-vault/key-vault-overview.md) di Azure per la gestione dei certificati, verificare che le chiavi dell'insieme di credenziali consentano le operazioni di **crittografia** e **decrittografia.** In caso contrario, le azioni di codifica e decodifica hanno esito negativo.

  Nel portale di Azure passare alla chiave nell'insieme di credenziali delle chiavi, esaminare le **operazioni consentite**della chiave e verificare che le operazioni **di crittografia** e **decrittografia** siano selezionate, ad esempio:In the Azure portal, go to the key in your key vault, review your key's Allowed operations , and confirm that the Encrypt and Decrypt operations are selected, for example:

  ![Controllare le operazioni con le chiavi dell'insieme di credenziali](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2

1. Se non è già stato fatto, nel portale di [Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nella finestra di progettazione aggiungere una nuova azione all'app per la logica.

1. In **Scegliere un'azione** e nella casella di ricerca selezionare **Tutte**. Nella casella di ricerca, immettere "come2 codifica" e assicurarsi di selezionare l'azione AS2 (v2): **AS2 Codifica**

   ![Selezionare "Codifica AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. A questo punto fornire informazioni per queste proprietà:Now provide information for these properties:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Messaggio da codificare** | Il payload del messaggio |
   | **AS2 da** | Identificatore per il mittente del messaggio come specificato dall'accordo AS2 |
   | **AS2 per** | Identificatore per il destinatario del messaggio come specificato dall'accordo AS2 |
   |||

   Ad esempio:

   ![Proprietà di codifica dei messaggi](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Se si verificano problemi durante l'invio di messaggi firmati o crittografati, provare diversi formati di algoritmo SHA256. La specifica AS2 non fornisce alcuna informazione sui formati SHA256, pertanto ogni provider usa la propria implementazione o formato.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificare i messaggi AS2

1. Se non è già stato fatto, nel portale di [Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nella finestra di progettazione aggiungere una nuova azione all'app per la logica.

1. In **Scegliere un'azione** e nella casella di ricerca selezionare **Tutte**. Nella casella di ricerca, immettere "as2 decode" e assicurarsi di selezionare l'azione AS2 (v2): **AS2 Decode**

   ![Selezionare "AS2 Decode"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Per le proprietà **Message da codificare** e **Message headers,** selezionare questi valori dagli output dei trigger o delle azioni precedenti.

   Si supponga, ad esempio, che l'app per la logica riceva messaggi tramite un trigger di richiesta. È possibile selezionare gli output da tale trigger.

   ![Selezionare Corpo e Intestazioni dagli output della richiesta](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/as2/) 

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE originale di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
