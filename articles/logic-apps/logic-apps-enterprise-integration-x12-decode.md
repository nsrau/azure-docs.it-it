---
title: Messaggi Decode X12 - App per la logica di Azure | Documentazione Microsoft
description: "Convalidare le proprietà EDI e generare i riconoscimenti con il decodificatore di messaggi X12 in Enterprise Integration Pack in App per la logica di Azure"
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
ms.author: LADocs; padmavc
ms.openlocfilehash: 9605fc3a1096d053bfeffb2544499935601b2c0f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Messaggi Decode X12 in App per la logica di Azure con Enterprise Integration Pack

Il connettore di messaggi Decode X12 convalida la busta in base all'accordo tra partner commerciali, convalida le proprietà EDI e specifiche del partner, suddivide gli interscambi in set di transazioni o mantiene gli interscambi interi, nonché genera riconoscimenti per le transazioni elaborate. Per usare questo connettore, è necessario aggiungerlo a un trigger esistente nell'app per la logica.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Decode X12 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto X12](logic-apps-enterprise-integration-x12.md) già definito nell'account di integrazione.

## <a name="decode-x12-messages"></a>Messaggi Decode X12

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).

2. Il connettore di messaggi Decode X12 non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.  Nella casella di ricerca, immettere "x12" come filtro. Selezionare **X12 - Decodifica il messaggio X12**.
   
    ![Cercare "X12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi. 

    ![Fornire i dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.  Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.
   
    ![dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Dopo aver creato la connessione, come illustrato in questo esempio, selezionare il messaggio con file flat X12 da decodificare.

    ![connessione all'account di integrazione creata](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Ad esempio:

    ![Selezionare il messaggio con il file flat X12 da decodificare](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Il contenuto effettivo del messaggio o il payload per l'array di messaggio, valido o non valido, è con codifica base64. Pertanto è necessario specificare un'espressione che elabori questo contenuto.
   > Di seguito è riportato un esempio che elabora il contenuto come XML che è possibile inserire nella visualizzazione codice o usando il Generatore di espressioni nella finestra di progettazione.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Esempio di contenuto](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Dettagli Decode X12

Il connettore Decode X12 esegue queste attività:

* Convalida la busta in base all'accordo tra partner commerciali.
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
* Suddivide l'interscambio in set di transazioni o mantiene l'intero interscambio:
  * Suddivide l'interscambio in set di transazioni - sospende i set di transazioni in caso di errore: suddivide l'interscambio in set di transazioni e analizza ogni set di transazioni. 
  L'azione X12 Decode restituisce solo i set di transazioni che non sono stati convalidati in `badMessages` e restituisce i restanti set di transazioni in `goodMessages`.
  * Suddivide l'interscambio in set di transazioni - sospende l'interscambio in caso di errore: suddivide l'interscambio in set di transazioni e analizza ogni set di transazioni. 
  Se la convalida di uno o più set di transazioni dell'interscambio non riesce, l'azione X12 Decode restituisce tutti i set di transazioni in quell'interscambio in `badMessages`.
  * Mantiene l'interscambio - sospende i set transazioni in caso di errore: mantiene l'interscambio ed elabora l'intero interscambio in batch. 
  L'azione X12 Decode restituisce solo i set di transazioni che non sono stati convalidati in `badMessages` e restituisce i restanti set di transazioni in `goodMessages`.
  * Mantiene l'interscambio - sospende l'interscambio in caso di errore: mantiene l'interscambio ed elabora l'intero interscambio in batch. 
  Se la convalida di uno o più set di transazioni dell'interscambio non riesce, l'azione X12 Decode restituisce tutti i set di transazioni in quell'interscambio in `badMessages`. 
* Genera un riconoscimento tecnico e/o funzionale (se configurata).
  * Un riconoscimento tecnico viene generato in seguito alla convalida dell'intestazione. Il riconoscimento tecnico segnala lo stato dell'elaborazione di un'intestazione e finale di interscambio in base all'indirizzo del ricevitore.
  * Un riconoscimento funzionale viene generato in seguito alla convalida del corpo. Il riconoscimento funzionale segnala ogni errore rilevato durante l'elaborazione del documento ricevuto.

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 

