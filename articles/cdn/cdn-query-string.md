---
title: Controllare il comportamento di memorizzazione nella cache della rete per la distribuzione di contenuti di Azure con stringhe di query | Microsoft Docs
description: La memorizzazione nella cache della stringa di query della rete CDN controlla in che modo i file devono essere memorizzati nella cache quando contengono stringhe di query.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 04c9ad5e58af073204eb6a16df96f0517a0ee668
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Controllare il comportamento di memorizzazione nella cache della rete per la distribuzione di contenuti di Azure con stringhe di query
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Rete CDN Premium di Azure fornita da Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Panoramica
La rete per la distribuzione di contenuti (CDN) di Azure consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non ha importanza. 

> [!IMPORTANT]
> I prodotti della rete CDN Standard e Premium forniscono la stessa funzionalità di memorizzazione nella cache delle stringhe di query, ma l'interfaccia utente è diversa.  Questo articolo descrive l'interfaccia per la **rete CDN Standard di Azure fornita da Akamai** e della **rete CDN Standard di Azure fornita da Verizon**. Per informazioni sulla memorizzazione nella cache di stringhe di query con la **rete CDN Premium di Azure fornita da Verizon**, vedere l'articolo [Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query - Premium](cdn-query-string-premium.md).

Sono disponibili tre modalità di stringa di query:

- **Ignora stringhe di query**: modalità predefinita. In questa modalità il nodo perimetrale della rete CDN passa la stringa di query dal richiedente all'origine alla prima richiesta ed esegue la memorizzazione nella cache dell'asset. Tutte le richieste successive per quell'asset che vengono presentate dal nodo perimetrale ignoreranno la stringa di query fino a quando l'asset memorizzato nella cache non sarà scaduto.
- **Disabilita la memorizzazione nella cache per le stringhe di query**: in questa modalità le richieste con stringhe di query non vengono memorizzate nella cache in corrispondenza del nodo perimetrale della rete CDN. Il nodo edge recupera l'asset direttamente dall'origine e lo passa al richiedente ad ogni richiesta.
- **Memorizza nella cache tutti gli URL univoci**: in questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta dall'origine per una richiesta di `example.ashx?q=test1` viene memorizzata nella cache in corrispondenza del nodo perimetrale e restituita per le successive memorizzazione nella cache con quella stessa stringa di query. Una richiesta di `example.ashx?q=test2` viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modifica delle impostazioni di memorizzazione nella cache della stringa di query per i profili standard della rete CDN
1. Aprire un profilo di rete CDN e quindi fare clic sull'endpoint della rete CDN che si desidera gestire.
   
   ![Endpoint del profilo di rete CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. In Impostazioni fare clic su **Cache**.
   
    ![Pulsante Cache del profilo della rete CDN](./media/cdn-query-string/cdn-cache-btn.png)
   
3. Nell'elenco **Comportamento di memorizzazione nella cache della stringa di query** selezionare una modalità per la stringa di query e quindi fare clic su **Salva**.
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> Le modifiche delle impostazioni di memorizzazione nella cache delle stringhe non sono immediatamente visibili perché la propagazione della registrazione nella rete CDN richiede tempo. La propagazione dei profili della **rete CDN di Azure fornita da Akamai** di solito dura meno di un minuto. Per i profili della **rete CDN di Azure fornita da Verizon**, la propagazione viene in genere completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.


