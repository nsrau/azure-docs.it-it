---
title: Messaggi Decode X12 - App per la logica di Azure | Documentazione Microsoft
description: "Convalidare le proprietà EDI e generare codice XML per set di transazioni con il decodificatore di messaggi X12 in Enterprise Integration Pack in App per la logica di Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 717069dbe211ea9cc04925875e0f28c85ef25ac2
ms.lasthandoff: 03/10/2017


---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Messaggi Decode X12 in App per la logica di Azure con Enterprise Integration Pack

Il connettore di messaggi Decode X12 convalida le proprietà EDI e specifiche del partner e genera un documento XML per ogni set di transazioni e un riconoscimento per le transazioni elaborate. Per usare questo connettore, è necessario aggiungerlo a un trigger esistente nell'app per la logica.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Decode X12 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto X12](logic-apps-enterprise-integration-x12.md) già definito nell'account di integrazione.

## <a name="decode-x12-messages"></a>Messaggi Decode X12

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).

2. Il connettore di messaggi Decode X12 non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.    Nella casella di ricerca, immettere "x12" come filtro. Selezionare **X12 - Decodifica il messaggio X12**.
   
    ![Cercare "X12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi. 

    ![Fornire i dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.    Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.
   
    ![dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Dopo aver creato la connessione, come illustrato in questo esempio, selezionare il messaggio con file flat X12 da decodificare.

    ![connessione all'account di integrazione creata](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Ad esempio:

    ![Selezionare il messaggio con il file flat X12 da decodificare](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>Dettagli Decode X12

Il connettore Decode X12 esegue queste attività:

* Convalida la busta in base all'accordo tra partner commerciali.
* Genera un documento XML per ogni set di transazioni.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida strutturale EDI e convalida estesa dello schema.
  * Convalida della struttura della busta dell'interscambio.
  * Convalida dello schema della busta in base allo schema di controllo.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni. 
* Verifica che i numeri di controllo di un set di interscambio, gruppo e di transazioni non siano duplicati.
  * Controlla il numero di controllo dell'interscambio rispetto agli interscambi ricevuti in precedenza.
  * Controlla il numero di controllo del gruppo con gli altri numeri di controllo del gruppo dell'interscambio.
  * Controlla il numero di controllo del set di transazioni con gli altri numeri di controllo del set transazioni in tale gruppo.
* Converte l'intero interscambio in XML. 
  * Suddivide l'interscambio in set di transazioni - Sospende i set di transazioni in caso di errore: analizza ogni set di transazioni di un interscambio in un documento XML separato. Se la convalida di uno o più set di transazioni dell'interscambio non riesce, X12 Decode sospende solo i set di transazioni interessati.
  * Suddivide l'interscambio in set di transazioni - Sospende l'interscambio in caso di errore: analizza ogni set di transazioni di un interscambio in un documento XML separato.  Se la convalida di uno o più set di transazioni dell'interscambio non riesce, X12 Decode sospende l'intero interscambio.
  * Mantiene l'interscambio - Sospende i set transazioni in caso di errore: crea un documento XML per l'intero interscambio in batch. X12 Decode sospende solo i set di transazioni che non superano la convalida, pur continuando a elaborare tutti gli altri set di transazioni.
  * Mantiene l'interscambio - Sospende l'interscambio in caso di errore: crea un documento XML per l'intero interscambio in batch. Se la convalida di uno o più set di transazioni dell'interscambio non riesce, X12 Decode sospende l'intero interscambio. 
* Genera un riconoscimento tecnico e/o funzionale (se configurata).
  * Un riconoscimento tecnico viene generato in seguito alla convalida dell'intestazione. Il riconoscimento tecnico segnala lo stato dell'elaborazione di un'intestazione e finale di interscambio in base all'indirizzo del ricevitore.
  * Un riconoscimento funzionale viene generato in seguito alla convalida del corpo. Il riconoscimento funzionale segnala ogni errore rilevato durante l'elaborazione del documento ricevuto.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 


