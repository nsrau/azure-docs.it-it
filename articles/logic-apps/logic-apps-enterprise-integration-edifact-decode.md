---
title: Decodificare messaggi EDIFACT - App per la logica di Azure | Documentazione Microsoft
description: "Convalidare le proprietà EDI e generare i riconoscimenti con il decodificatore di messaggi EDIFACT in Enterprise Integration Pack in App per la logica di Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: e3787b48037360bf6066ddce2bacba6842213b2d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Messaggi Decode EDIFACT in App per la logica di Azure con Enterprise Integration Pack

Il connettore di messaggi EDIFACT convalida le proprietà EDI e specifiche del partner, suddivide gli interscambi in set di transazioni o mantiene gli interscambi interi, nonché genera riconoscimenti per le transazioni elaborate. Per usare questo connettore, è necessario aggiungerlo a un trigger esistente nell'app per la logica.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Decode EDIFACT è necessario un account di integrazione. 
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto EDIFACT](logic-apps-enterprise-integration-edifact.md) già definito nell'account di integrazione.

## <a name="decode-edifact-messages"></a>Decodificare messaggi EDIFACT

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).

2. Il connettore di messaggi Decode EDIFACT non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3. Nella casella di ricerca, digitare "EDIFACT" come filtro. Selezionare **Decodifica il messaggio EDIFACT**.
   
    ![ricerca di EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi.
   
    ![creare un account di integrazione](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

4. Dopo aver completato la creazione della connessione, scegliere **Crea**. I dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente:

    ![dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Dopo aver creato la connessione, come illustrato in questo esempio, selezionare il messaggio con file flat EDIFACT da decodificare.

    ![connessione all'account di integrazione creata](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Ad esempio:

    ![Selezionare il messaggio con il file flat EDIFACT da decodificare](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Dettagli decodificatore EDIFACT

Il connettore Decode EDIFACT esegue queste attività: 

* Convalida la busta in base all'accordo tra partner commerciali.
* Risolve il contratto associando qualificatore del mittente e identificatore e qualificatore del ricevitore e identificatore.
* Suddivide un interscambio in più transazioni quando l'interscambio ha più di una transazione basata sulla configurazione delle impostazioni di ricezione dell'accordo.
* Disassembla l'interscambio.
* Convalida le proprietà EDI e specifiche del partner, comprese:
  * Convalida della struttura della busta dell'interscambio
  * Convalida dello schema della busta in base allo schema di controllo
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
* Verifica che i numeri di controllo di un set di interscambio, gruppo e di transazioni non siano duplicati (se configurata). 
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
* Genera un riconoscimento tecnico (controllo) e/o funzionale (se configurata).
  * Un riconoscimento tecnico o CONTRL ACK segnala i risultati di un controllo sintattico dell'interscambio completo ricevuto.
  * Un riconoscimento funzionale riconosce l'accettazione o il rifiuto di un interscambio o un gruppo ricevuto.

## <a name="view-swagger-file"></a>Visualizzare il file Swagger
Per visualizzare i dettagli di Swagger per il connettore EDIFACT, vedere [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 

