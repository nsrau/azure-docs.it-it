---
title: Applicare HTTPS usando il motore regole di rete CDN standard di Azure | Microsoft Docs
description: Il motore regole standard consente di personalizzare il modo in cui le richieste HTTP vengono gestite dalla rete CDN di Azure da Microsoft, ad esempio per bloccare la distribuzione di determinati tipi di contenuto, definire un criterio di memorizzazione nella cache e modificare le intestazioni HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615923"
---
# <a name="azure-cdn-standard-rules-engine"></a>Motore regole standard della rete CDN di Azure

> [!NOTE]
> Il motore regole standard è disponibile solo per la rete CDN di Azure di Microsoft. 

Il motore regole di rete CDN standard di Azure consente di personalizzare la modalità di gestione delle richieste HTTP. Ad esempio, l'applicazione della distribuzione di contenuti su protocolli specifici, la definizione di un criterio di memorizzazione nella cache o la modifica di un'intestazione HTTP. Questa esercitazione illustra come creare una regola che reindirizza automaticamente gli utenti a HTTPS. 


## <a name="tutorial"></a>Esercitazione

1. Dalla pagina del profilo della rete **CDN** in rete CDN di Azure da profili Microsoft selezionare l'endpoint per il quale si desidera configurare le regole.
  
2. Selezionare la scheda **motore regole** a sinistra.
   
    Viene visualizzato il pannello motore regole con la regola globale. 
   
    [pagina nuove regole della rete CDN ![](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > L'ordine in cui sono elencate più regole influisce sulla modalità di gestione. Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.
   >

3. Fare clic sul pulsante **Aggiungi regola** e specificare un nome per la regola. I nomi delle regole devono iniziare con una lettera e contenere solo numeri e lettere.

4. Identificare il tipo di richieste a cui verrà applicata la regola. Usare l'elenco a discesa per selezionare la condizione di corrispondenza del **protocollo di richiesta** e usare il valore **Equals** **http**.
   
   [condizione di corrispondenza della regola ![rete CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Nell'elenco a discesa sono disponibili più tipi di condizioni di corrispondenza. Per un elenco dettagliato delle condizioni di corrispondenza, vedere [Condizioni di corrispondenza del motore regole](cdn-standard-rules-engine-match-conditions.md).
   
5. Scegliere l'azione che verrà applicata alle richieste identificate. Usare l'elenco a discesa per selezionare l'azione di **Reindirizzamento URL** e usare il valore **trovato (302)** per tipo e **https** per protocollo. Lasciare vuoti tutti gli altri campi per usare i valori in ingresso.
   
   [![azione regola CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Nell'elenco a discesa sono disponibili più azioni. Per un elenco dettagliato delle azioni, vedere [azioni del motore regole](cdn-standard-rules-engine-actions.md).

6. Selezionare **Save (Salva** ) per salvare la nuova regola.  La nuova regola verrà ora distribuita.
   
   > [!IMPORTANT]
   > Le modifiche alle regole possono richiedere fino a 15 minuti per la propagazione attraverso la rete CDN di Azure.
   >
   

## <a name="see-also"></a>Vedere anche

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Riferimento al motore regole standard](cdn-standard-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole standard](cdn-standard-rules-engine-match-conditions.md)
- [Azioni del motore regole standard](cdn-standard-rules-engine-actions.md)
