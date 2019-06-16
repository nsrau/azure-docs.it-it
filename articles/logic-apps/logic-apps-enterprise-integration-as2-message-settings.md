---
title: Impostazioni dei messaggi AS2 - App per la logica di Azure
description: Guida di riferimento per AS2 inviare e ricevere le impostazioni nelle App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769452"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Informazioni di riferimento per le impostazioni dei messaggi AS2 nelle App per la logica di Azure con Enterprise Integration Pack

Questo riferimento descrive le proprietà che è possibile impostare per la specifica come un accordo AS2 gestisce i messaggi inviati e ricevuti tra partner commerciali. Consente di impostare queste proprietà in base al contratto con il partner che scambia messaggi con l'utente.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Impostazioni di ricezione AS2

![Selezionare "Impostazioni di ricezione"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Proprietà | Obbligatorio | Descrizione |
|----------|----------|-------------|
| **Ignora proprietà del messaggio** | No | Esegue l'override di proprietà nei messaggi in arrivo con le impostazioni delle proprietà. |
| **Il messaggio deve essere firmato** | No | Specifica se tutti i messaggi in ingresso devono essere firmati digitalmente. Se è necessaria la firma, dal **certificato** selezionare un guest partner pubblica certificato esistente per la convalida della firma dei messaggi. Se non si dispone di un certificato, altre informazioni sulle [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Messaggio deve essere crittografato** | No | Specifica se tutti i messaggi in ingresso devono essere crittografati. I messaggi non crittografati vengono rifiutati. Se necessaria la crittografia, dal **certificato** selezionare un esistente host certificato privato del partner per la decrittografia dei messaggi in arrivo. Se non si dispone di un certificato, altre informazioni sulle [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Il messaggio deve essere compresso** | No | Specifica se tutti i messaggi in arrivo devono essere compressi. I messaggi non compressi vengono rifiutati. |
| **Non consentire ID messaggio duplicati** | No | Specifica se consentire i messaggi con ID duplicati. Se non si consentono di ID duplicati, selezionare il numero di giorni tra i controlli. È anche possibile scegliere se sospendere i duplicati. |
| **Testo MDN** | No | Specifica il predefinita messaggio notifica sulla ricezione del (messaggio MDN) che si desidera inviata al mittente del messaggio. |
| **Invio di MDN** | No | Specifica se inviare un MDN sincrono per i messaggi ricevuti.  |
| **Invia MDN firmato** | No | Specifica se inviare firmate per i messaggi ricevuti. Se è necessaria la firma, dal **algoritmo MIC** elencare, selezionare l'algoritmo da usare per firmare i messaggi. |
| **Invio di MDN asincroni** | No | Specifica se inviare i messaggi MDN in modo asincrono. Se si seleziona gli MDN asincroni, il **URL** specificare l'URL a cui inviare le MDN. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Impostazioni di invio AS2

![Selezionare "Impostazioni di invio"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Proprietà | Obbligatorio | Descrizione |
|----------|----------|-------------|
| **Abilita firma del messaggio** | No | Specifica se tutti i messaggi in uscita devono essere firmati digitalmente. Se è necessaria la firma, selezionare questi valori: <p>-From la **algoritmo di firma** elencare, selezionare l'algoritmo da usare per firmare i messaggi. <br>-From la **certificato** elencare, selezionare un host partner privata certificato esistente per firmare i messaggi. Se non si dispone di un certificato, altre informazioni sulle [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Abilita crittografia messaggio** | No | Specifica se tutti i messaggi in uscita devono essere crittografati. Se necessaria la crittografia, selezionare questi valori: <p>-From la **algoritmo di crittografia** elencare, selezionare l'algoritmo del certificato pubblico partner guest da utilizzare per la crittografia dei messaggi. <br>-From la **certificato** selezionare un esistente guest certificato privato del partner per la crittografia dei messaggi in uscita. Se non si dispone di un certificato, altre informazioni sulle [aggiunta di certificati](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Abilita compressione messaggio** | No | Specifica se tutti i messaggi in uscita devono essere compressi. |
| **Espandi intestazioni HTTP** | No | Inserisce il protocollo HTTP `content-type` intestazione in una singola riga. |
| **Trasmetti nome file in intestazione MIME** | No | Specifica se includere il nome del file in intestazione MIME. |
| **Richiedi MDN** | No | Specifica se ricevere le notifiche di eliminazione del messaggio (MDN) per tutti i messaggi in uscita. |
| **Richiedi MDN firmato** | No | Specifica se per la ricezione di messaggi MDN firmato per tutti i messaggi in uscita. Se è necessaria la firma, dal **algoritmo MIC** elencare, selezionare l'algoritmo da usare per firmare i messaggi. |
| **Richiedi MDN asincrono** | No | Specifica se per la ricezione di messaggi MDN in modo asincrono. Se si seleziona gli MDN asincroni, il **URL** specificare l'URL a cui inviare le MDN. |
| **Abilitare NRR** | No | Specifica se richiedere la ricezione di non ripudio (NRR). Questo attributo comunicazione fornisce evidenza che sono stati ricevuti i dati come inviati. |
| **Formato di algoritmo SHA2** | No | Specifica il formato di algoritmo MIC da usare per la firma nelle intestazioni per i messaggi AS2 in uscita o MDN |
||||

## <a name="next-steps"></a>Passaggi successivi

[Scambiare messaggi AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
