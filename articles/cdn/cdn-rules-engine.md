---
title: Eseguire l'override del comportamento HTTP con il motore regole della rete CDN di Azure | Documentazione Microsoft
description: "Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP nella rete CDN di Azure, ad esempio la distribuzione di determinati tipi di contenuto, la definizione di criteri di memorizzazione nella cache e la modifica delle intestazioni HTTP."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Eseguire l'override del comportamento HTTP con il motore regole della rete CDN di Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica
Il motore regole consente di personalizzare la modalità con cui vengono gestite le richieste HTTP, come ad esempio il blocco della distribuzione di determinati tipi di contenuto, la definizione di un criterio di memorizzazione nella cache e la modifica della intestazioni HTTP.  Questa esercitazione illustra la creazione di una regola che modifica il comportamento di memorizzazione nella cache degli asset della rete CDN.  Nella sezione "[Vedere anche](#see-also)" sono disponibili anche contenuti video.

   > [!TIP] 
   > Per informazioni dettagliate sulla sintassi, vedere le [informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Esercitazione
1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Fare clic sulla scheda **HTTP Large** (HTTP esteso) e quindi su **Motore regole**.
   
    Vengono visualizzate le opzioni per una nuova regola.
   
    ![Opzioni delle regole della nuova rete CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.
   > 
   > 
3. Inserire un nome per la casella di testo **Nome / Descrizione** .
4. Identificare il tipo di richieste che a cui verrà applicata la regola.  Per impostazione predefinita, viene selezionata la condizione corrispondente **Sempre** .  Si utilizzerà **Sempre** per questa esercitazione, quindi lasciarla selezionata.
   
   ![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Vi sono molti tipi di condizioni di corrispondenza disponibili nell'elenco a discesa.  Facendo clic sull'icona blu delle informazioni a sinistra della condizione di corrispondenza viene illustrata la condizione selezionata in modo dettagliato.
   > 
   >  Per l'elenco completo e dettagliato delle espressioni condizionali, vedere l'articolo relativo alle [espressioni condizionali del motore regole](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Per l'elenco completo e dettagliato delle condizioni di corrispondenza, vedere l'articolo relativo alle [condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Fare clic sul pulsante **+** accanto a **Funzionalità** per aggiungere una nuova funzionalità.  Nell'elenco a discesa a sinistra, selezionare **Forza interna Max-Age**.  Nella casella di testo che viene visualizzata, inserire **300**.  Lasciare i valori predefiniti restanti.
   
   ![Funzionalità della rete CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Come con le condizioni di corrispondenza, fare clic sull'icona blu delle informazioni a sinistra della nuova funzionalità per visualizzare i dettagli su questa funzionalità.  Nel caso di **Force Internal Max-Age** (Forza validità massima interna), viene eseguito l'override delle intestazioni **Cache-Control** ed **Expires** dell'asset per controllare quando il nodo perimetrale della rete CDN aggiornerà l'asset dall'origine.  L’esempio di 300 secondi indica che il nodo edge della rete CDN memorizza l’asset nella cache per 5 minuti prima di aggiornare la risorsa dall'origine.
   > 
   > Per l'elenco completo e dettagliato delle funzionalità, vedere l'articolo contenente [informazioni dettagliate sulle funzionalità del motore regole](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Fare clic sul pulsante **Aggiungi** per salvare la nuova regola.  La nuova regola ora è in attesa di approvazione. Dopo l'approvazione, lo stato viene modificato da **Pending XML** (XML in sospeso) a **Active XML** (XML attivo).
   
   > [!IMPORTANT]
   > La propagazione delle modifiche delle regole in tutta la rete CDN può durare fino a 90 minuti.
   > 
   > 

## <a name="see-also"></a>Vedere anche 
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)
* [Vedere il video relativo alle nuove potenti funzionalità Premium della rete CDN di Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/)