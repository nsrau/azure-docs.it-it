---
title: Messaggi Encode AS2 - App per la logica di Azure | Documentazione Microsoft
description: Messaggi Encode AS2 con App per la logica di Azure ed Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 455056feaa1b13022be3ab3c15880a87c50dedd9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003548"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Messaggi Encode AS2 con App per la logica di Azure ed Enterprise Integration Pack

Per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi, usare il connettore di messaggi Encode AS2. Questo connettore offre funzionalità di firma digitale, crittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi, con il conseguente supporto del database di non ripudio.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Encode AS2 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto AS2](logic-apps-enterprise-integration-as2.md) già definito nell'account di integrazione.

## <a name="encode-as2-messages"></a>Codificare i messaggi AS2

1. [Creare un'app per la logica](quickstart-create-first-logic-app-workflow.md).

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
* Trasmetti nome file in intestazione MIME (se configurato)


  > [!NOTE]
  > Se si usa Azure Key Vault per la gestione dei certificati, assicurarsi di configurare le chiavi per consentire l'operazione di **crittografia**.
  > In caso contrario, la codifica AS2 avrà esito negativo.
  >
  > ![Key Vault esegue la decrittografia](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Provare questo esempio

Per distribuire un'app per la logica completamente operativa e uno scenario AS2 di esempio, vedere il [modello e lo scenario di app per la logica AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 

