---
title: Impostazioni dei messaggi AS2
description: Guida di riferimento per le impostazioni di invio e ricezione AS2 in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793035"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Informazioni di riferimento per le impostazioni dei messaggi AS2 in app per la logica di Azure con Enterprise Integration Pack

Questo riferimento descrive le proprietà che è possibile impostare per specificare il modo in cui un contratto AS2 gestisce i messaggi inviati e ricevuti tra i partner commerciali. Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Impostazioni di ricezione AS2

![Selezionare "impostazioni di ricezione"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Proprietà | Obbligatoria | Description |
|----------|----------|-------------|
| **Ignora proprietà del messaggio** | No | Esegue l'override delle proprietà dei messaggi in arrivo con le impostazioni delle proprietà. |
| **Il messaggio deve essere firmato** | No | Specifica se tutti i messaggi in ingresso devono essere firmati digitalmente. Se è necessaria la firma, dall'elenco **certificato** selezionare un certificato pubblico del partner guest esistente per convalidare la firma dei messaggi. Se non si dispone di un certificato, vedere altre informazioni sull' [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Il messaggio deve essere crittografato** | No | Specifica se tutti i messaggi in ingresso devono essere crittografati. I messaggi non crittografati vengono rifiutati. Se è necessaria la crittografia, dall'elenco **certificato** selezionare un certificato privato del partner host esistente per la decrittografia dei messaggi in ingresso. Se non si dispone di un certificato, vedere altre informazioni sull' [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Il messaggio deve essere compresso** | No | Specifica se tutti i messaggi in ingresso devono essere compressi. I messaggi non compressi vengono rifiutati. |
| **Non consentire duplicati ID messaggio** | No | Specifica se consentire messaggi con ID duplicati. Se gli ID duplicati non sono consentiti, selezionare il numero di giorni tra i controlli. È anche possibile scegliere se sospendere i duplicati. |
| **Testo MDN** | No | Specifica la notifica di disposizione del messaggio (MDN) predefinita che si desidera inviare al mittente del messaggio. |
| **Invia MDN** | No | Specifica se inviare MDN sincroni per i messaggi ricevuti.  |
| **Invia MDN firmato** | No | Specifica se inviare messaggi MDN firmati per i messaggi ricevuti. Se è necessaria la firma, dall'elenco **algoritmo MIC** selezionare l'algoritmo da usare per la firma dei messaggi. |
| **Invia MDN asincrono** | No | Specifica se inviare messaggi MDN in modo asincrono. Se si seleziona MDN asincrono, nella casella **URL** specificare l'URL in cui inviare i messaggi MDN. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Impostazioni di invio AS2

![Selezionare "Invia impostazioni"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Proprietà | Obbligatoria | Description |
|----------|----------|-------------|
| **Abilita firma messaggi** | No | Specifica se tutti i messaggi in uscita devono essere firmati digitalmente. Se è necessaria la firma, selezionare i valori seguenti: <p>-Nell'elenco **algoritmo di firma** selezionare l'algoritmo da usare per la firma dei messaggi. <br>-Nell'elenco **certificato** selezionare un certificato privato del partner host esistente per la firma dei messaggi. Se non si dispone di un certificato, vedere altre informazioni sull' [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Abilitare la crittografia del messaggio** | No | Specifica se tutti i messaggi in uscita devono essere crittografati. Se è necessaria la crittografia, selezionare i valori seguenti: <p>-Nell'elenco **algoritmo di crittografia** selezionare l'algoritmo del certificato pubblico del partner Guest da usare per crittografare i messaggi. <br>-Nell'elenco **certificato** selezionare un certificato privato del partner guest esistente per la crittografia dei messaggi in uscita. Se non si dispone di un certificato, vedere altre informazioni sull' [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Abilitare la compressione dei messaggi** | No | Specifica se devono essere compressi tutti i messaggi in uscita. |
| **Espandi intestazioni HTTP** | No | Inserisce l'intestazione HTTP `content-type` su una sola riga. |
| **Trasmetti nome file in intestazione MIME** | No | Specifica se includere il nome file nell'intestazione MIME. |
| **Richiedi MDN** | No | Specifica se ricevere notifiche di disposizione messaggi (MDN) per tutti i messaggi in uscita. |
| **Richiedi MDN firmato** | No | Specifica se ricevere messaggi MDN firmati per tutti i messaggi in uscita. Se è necessaria la firma, dall'elenco **algoritmo MIC** selezionare l'algoritmo da usare per la firma dei messaggi. |
| **Richiedi MDN asincrono** | No | Specifica se ricevere MDN in modo asincrono. Se si seleziona MDN asincrono, nella casella **URL** specificare l'URL in cui inviare i messaggi MDN. |
| **Abilita NRR** | No | Specifica se richiedere la ricezione di non ripudio (NRR). Questo attributo di comunicazione fornisce l'evidenza che i dati sono stati ricevuti come destinatari. |
| **Formato dell'algoritmo SHA2** | No | Specifica il formato dell'algoritmo MIC da utilizzare per l'accesso alle intestazioni per i messaggi AS2 in uscita o MDN |
||||

## <a name="next-steps"></a>Passaggi successivi

[Messaggi AS2 di Exchange](../logic-apps/logic-apps-enterprise-integration-as2.md)
