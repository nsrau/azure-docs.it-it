---
title: Impostazioni dei messaggi AS2
description: Guida di riferimento per le impostazioni di invio e ricezione AS2 in App per la logica di Azure con Enterprise Integration PackReference guide for AS2 send and receive settings in Azure Logic Apps with Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793035"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Riferimento per le impostazioni dei messaggi AS2 nelle app per la logica di Azure con Enterprise Integration Pack

In questo riferimento vengono descritte le proprietà che è possibile impostare per specificare il modo in cui un accordo AS2 gestisce i messaggi inviati e ricevuti tra i partner commerciali. Impostare queste proprietà in base all'accordo con il partner che scambia messaggi con l'utente.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Impostazioni di ricezione AS2

![Selezionare "Impostazioni di ricezione"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Proprietà | Obbligatoria | Descrizione |
|----------|----------|-------------|
| **Override message properties** | No | Esegue l'override delle proprietà dei messaggi in arrivo con le impostazioni delle proprietà. |
| **Il messaggio deve essere firmato** | No | Specifica se tutti i messaggi in arrivo devono essere firmati digitalmente. Se è necessaria la firma, nell'elenco **Certificato** selezionare un certificato pubblico del partner guest esistente per la convalida della firma nei messaggi. Se non si dispone di un certificato, vedere altre informazioni [sull'aggiunta](../logic-apps/logic-apps-enterprise-integration-certificates.md)di certificati. |
| **Il messaggio deve essere crittografato** | No | Specifica se tutti i messaggi in arrivo devono essere crittografati. I messaggi non crittografati vengono rifiutati. Se è necessaria la crittografia, nell'elenco **Certificato** selezionare un certificato privato del partner host esistente per decrittografare i messaggi in arrivo. Se non si dispone di un certificato, vedere altre informazioni [sull'aggiunta](../logic-apps/logic-apps-enterprise-integration-certificates.md)di certificati. |
| **Il messaggio deve essere compresso** | No | Specifica se tutti i messaggi in arrivo devono essere compressi. I messaggi non compressi vengono rifiutati. |
| **Non consentire duplicati dell'ID messaggio** | No | Specifica se consentire i messaggi con ID duplicati. Se non si consentono ID duplicati, selezionare il numero di giorni tra i controlli. È inoltre possibile scegliere se sospendere i duplicati. |
| **Testo MDN** | No | Specifica la notifica di disposizione del messaggio (MDN) predefinita che si desidera inviare al mittente del messaggio. |
| **Send MDN (Invia MDN)** | No | Specifica se inviare MDN sincroni per i messaggi ricevuti.  |
| **Send signed MDN (Invia MDN firmato)** | No | Specifica se inviare mDN firmati per i messaggi ricevuti. Se è necessaria la firma, nell'elenco **Algoritmo MIC** selezionare l'algoritmo da utilizzare per la firma dei messaggi. |
| **Send asynchronous MDN (Invia MDN asincrono)** | No | Specifica se inviare MDN in modo asincrono. Se si seleziona MDN asincroni, nella casella **URL** specificare l'URL a cui inviare gli MDN. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Impostazioni di invio AS2

![Seleziona "Impostazioni di invio"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Proprietà | Obbligatoria | Descrizione |
|----------|----------|-------------|
| **Enable message signing (Abilita la firma dei messaggi)** | No | Specifica se tutti i messaggi in uscita devono essere firmati digitalmente. Se è necessaria la firma, selezionare i valori seguenti:If you require signing, select these values: <p>- Dall'elenco Algoritmo di **firma,** selezionare l'algoritmo da utilizzare per la firma dei messaggi.- From the Signing Algorithm list, select the algorithm to use for signing messages. <br>- Nell'elenco **Certificato** selezionare un certificato privato del partner host esistente per la firma dei messaggi. Se non si dispone di un certificato, vedere altre informazioni [sull'aggiunta](../logic-apps/logic-apps-enterprise-integration-certificates.md)di certificati. |
| **Enable message encryption (Abilita la crittografia dei messaggi)** | No | Specifica se tutti i messaggi in uscita devono essere crittografati. Se è necessaria la crittografia, selezionare i valori seguenti:If you require encryption, select these values: <p>- Dall'elenco Algoritmo di **crittografia,** selezionare l'algoritmo di certificato pubblico del partner guest da utilizzare per la crittografia dei messaggi. <br>- Dall'elenco **Certificato,** selezionare un certificato privato del partner guest esistente per crittografare i messaggi in uscita. Se non si dispone di un certificato, vedere altre informazioni [sull'aggiunta](../logic-apps/logic-apps-enterprise-integration-certificates.md)di certificati. |
| **Abilita compressione messaggio** | No | Specifica se tutti i messaggi in uscita devono essere compressi. |
| **Espandi intestazioni HTTP** | No | Inserisce `content-type` l'intestazione HTTP su una singola riga. |
| **Trasmetti nome file in intestazione MIME** | No | Specifica se includere il nome del file nell'intestazione MIME. |
| **Richiedi MDN** | No | Specifica se ricevere le notifiche di disposizione dei messaggi (MDN) per tutti i messaggi in uscita. |
| **Richiedi MDN firmato** | No | Specifica se ricevere gli MDN firmati per tutti i messaggi in uscita. Se è necessaria la firma, nell'elenco **Algoritmo MIC** selezionare l'algoritmo da utilizzare per la firma dei messaggi. |
| **Richiedi MDN asincrono** | No | Specifica se ricevere MDN in modo asincrono. Se si seleziona MDN asincroni, nella casella **URL** specificare l'URL a cui inviare gli MDN. |
| **Enable NRR (Attiva NRR)** | No | Specifica se richiedere l'entrata senza ripudio (NRR). Questo attributo di comunicazione fornisce la prova che i dati sono stati ricevuti come indirizzati. |
| **Formato di algoritmo SHA2** | No | Specifica il formato dell'algoritmo MIC da utilizzare per la firma delle intestazioni per i messaggi AS2 o MDN in uscita |
||||

## <a name="next-steps"></a>Passaggi successivi

[Scambiare messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
