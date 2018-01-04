---
title: 'Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query: Premium | Documentazione Microsoft'
description: La memorizzazione nella cache della stringa di query della rete CDN controlla in che modo i file devono essere memorizzati nella cache quando contengono stringhe di query.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 2021b5b7602605a7c264e9cd575399077691da34
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Controllare il comportamento di memorizzazione nella cache della rete per la distribuzione di contenuti di Azure con stringhe di query: Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Rete CDN Premium di Azure fornita da Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Panoramica
La rete per la distribuzione di contenuti (CDN) di Azure consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non ha importanza. 

> [!IMPORTANT]
> I prodotti della rete CDN Standard e Premium forniscono la stessa funzionalità di memorizzazione nella cache delle stringhe di query, ma l'interfaccia utente è diversa.  Questo articolo descrive l'interfaccia della **rete CDN Premium di Azure fornita da Verizon**. Per informazioni sulla memorizzazione nella cache di stringhe di query con la **rete CDN Standard di Azure fornita da Akamai** e la **rete CDN Standard di Azure fornita da Verizon**, vedere l'articolo [Controllo del comportamento di memorizzazione nella cache delle richieste della rete CDN con le stringhe di query](cdn-query-string.md).
>

Sono disponibili tre modalità di stringa di query:

- **standard-cache**: si tratta della modalità predefinita. In questa modalità il nodo perimetrale della rete CDN passa la stringa di query dal richiedente all'origine alla prima richiesta ed esegue la memorizzazione nella cache dell'asset. Tutte le richieste successive per quell'asset che vengono presentate dal nodo perimetrale ignoreranno la stringa di query fino a quando l'asset memorizzato nella cache non sarà scaduto.
- **no-cache**: in questa modalità, le richieste con stringhe di query non vengono memorizzate nella cache in corrispondenza del nodo edge della rete CDN. Il nodo edge recupera l'asset direttamente dall'origine e lo passa al richiedente ad ogni richiesta.
- **unique-cache**: in questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta dall'origine per una richiesta di `example.ashx?q=test1` viene memorizzata nella cache in corrispondenza del nodo perimetrale e restituita per le successive memorizzazione nella cache con quella stessa stringa di query. Una richiesta di `example.ashx?q=test2` viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modifica delle impostazioni di memorizzazione nella cache della stringa di query per i profili premium della rete CDN
1. Aprire il profilo della rete CDN e quindi fare clic su **Manage** (Gestisci).
   
    ![Pulsante Manage (Gestisci) del profilo di rete CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **HTTP Large** (HTTP esteso) e quindi passare il puntatore sul menu a comparsa **Impostazioni cache**. Fare clic su **Comportamento di memorizzazione nella cache della stringa di query**.
   
    Vengono visualizzate le opzioni di memorizzazione nella cache della stringa di query.
   
    ![Opzioni della memorizzazione nella cache della stringa di query della rete CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selezionare una modalità della stringa di query e quindi fare clic su **Aggiorna**.

> [!IMPORTANT]
> Le modifiche delle impostazioni di memorizzazione nella cache delle stringhe non sono immediatamente visibili perché la propagazione della registrazione nella rete CDN richiede tempo. Per i profili Premium della **rete CDN di Azure fornita da Verizon**, la propagazione viene in genere completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.
 

