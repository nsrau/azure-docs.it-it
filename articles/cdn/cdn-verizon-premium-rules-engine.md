---
title: Override del comportamento HTTP con la rete CDN di Azure da Verizon Premium motore regole di Business | Microsoft Docs
description: Il motore regole consente di personalizzare come richieste HTTP vengono gestite dalla rete CDN di Azure fornita da Verizon Premium, ad esempio la distribuzione di determinati tipi di contenuto, definire un criterio di memorizzazione nella cache e modificare le intestazioni HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: fa08f59f3b69395d548649bb131e04c5071c053d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593169"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Override del comportamento HTTP con la rete CDN di Azure da Verizon Premium motore regole di Business

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica

Il motore regole della rete CDN di Azure consente di personalizzare la modalità di gestione delle richieste HTTP. Ad esempio, è possibile bloccare il recapito di alcuni tipi di contenuto, definire criteri di memorizzazione nella cache o modificare un'intestazione HTTP. Questa esercitazione illustra la creazione di una regola che modifica il comportamento di memorizzazione nella cache degli asset della rete CDN. Per ulteriori informazioni sulla sintassi del motore regole, vedere [Informazioni di riferimento sul motore regole della rete CDN di Azure](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Accesso

Per accedere al motore regole è necessario prima di tutto selezionare **Gestisci** nella parte superiore della pagina **Profilo rete CDN** per accedere alla pagina di gestione della rete CDN di Azure. In base all'attivazione o meno dell'ottimizzazione basata su Accelerazione sito dinamico per l'endpoint, si accede al motore regole con il set di regole appropriato per il tipo di endpoint:

- Endpoint ottimizzati per la distribuzione Web generale o altre ottimizzazioni non basate su Accelerazione sito dinamico:
    
    Fare clic sulla scheda **HTTP Large** (HTTP esteso) e quindi su **Motore regole**.

    ![Motore regole per HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Endpoint ottimizzati per Accelerazione sito dinamico:
    
    Fare clic sulla scheda **ADN** e quindi su **Motore regole**.
    
    ADN è un termine usato da Verizon per specificare il contenuto con Accelerazione sito dinamico. Le regole create qui vengono ignorate dagli endpoint del profilo non ottimizzati per Accelerazione sito dinamico.

    ![Motore regole per Accelerazione sito dinamico](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Esercitazione

1. Nella pagina **Profilo rete CDN** selezionare **Gestisci**.
   
    ![Pulsante Manage (Gestisci) del profilo di rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.

2. Fare clic sulla scheda **HTTP Large** (HTTP esteso) e quindi su **Motore regole**.
   
    Vengono visualizzate le opzioni per una nuova regola.
   
    ![Opzioni delle regole della nuova rete CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.
   >

3. Inserire un nome per la casella di testo **Nome / Descrizione** .

4. Identificare il tipo di richieste a cui verrà applicata la regola. Usare la condizione di corrispondenza predefinita, **Sempre**.
   
   ![Condizione di corrispondenza regola della rete CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Nell'elenco a discesa sono disponibili più tipi di condizioni di corrispondenza. Per informazioni sulla condizione di corrispondenza attualmente selezionata, selezionare l'icona informativa blu alla sua sinistra.
   >
   >  Per un elenco dettagliato delle espressioni condizionali, vedere [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Per un elenco dettagliato delle condizioni di corrispondenza, vedere [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Per aggiungere una nuova funzionalità, fare clic sul pulsante **+** accanto a **Funzionalità**.  Nell'elenco a discesa a sinistra, selezionare **Forza interna Max-Age**.  Nella casella di testo che viene visualizzata, inserire **300**. Non modificare i valori predefiniti restanti.
   
   ![Funzionalità della regola CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Nell'elenco a discesa sono disponibili più funzionalità. Per informazioni sulla funzionalità attualmente selezionata, selezionare l'icona informativa blu alla sua sinistra.
   >
   > Nel caso di **Force Internal Max-Age** (Forza validità massima interna), viene eseguito l'override delle intestazioni `Cache-Control` e `Expires` dell'asset per controllare quando il nodo perimetrale della rete CDN aggiornerà l'asset dall'origine. In questo esempio, il nodo perimetrale della rete CDN memorizza l'asset nella cache per 300 secondi o 5 minuti prima di aggiornarlo dall'origine.
   >
   > Per un elenco dettagliato delle funzionalità, vedere [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Selezionare **Aggiungi** per salvare la nuova regola.  La nuova regola ora è in attesa di approvazione. Dopo l'approvazione, lo stato viene modificato da **Pending XML** (XML in sospeso) a **Active XML** (XML attivo).
   
   > [!IMPORTANT]
   > La propagazione delle modifiche alle regole in tutta la rete CDN di Azure può richiedere fino a 10 minuti.
   >
   >

## <a name="see-also"></a>Vedere anche

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Nuove potenti funzionalità premium di rete CDN di Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)