---
title: 'Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Standard | Microsoft Docs'
description: La funzionalità di memorizzazione nella cache con stringhe di query della rete CDN di Azure controlla il modo in cui i file vengono memorizzati nella cache quando una richiesta Web contiene una stringa di query. Questo articolo descrive la memorizzazione nella cache con stringhe di query nei prodotti di rete CDN Standard di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2b9e56f8a0a023c8423426fee081a5a48ebda330
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593467"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Standard
> [!div class="op_single_selector"]
> * [Livello Standard](cdn-query-string.md)
> * [Livello Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Panoramica
La rete per la distribuzione di contenuti (CDN) di Azure consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non ha importanza. 

> [!IMPORTANT]
> I prodotti di rete CDN Standard e Premium di Azure forniscono la stessa funzionalità di memorizzazione nella cache con stringhe di query, ma l'interfaccia utente è diversa. Questo articolo descrive l'interfaccia per la **rete CDN Standard di Azure con tecnologia Microsoft**, la **rete CDN Standard di Azure con tecnologia Akamai** e la **rete CDN Standard di Azure con tecnologia Verizon**. Per informazioni sulla memorizzazione nella cache con stringhe di query per la **rete CDN Premium di Azure con tecnologia Verizon**, vedere l'articolo [Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Premium](cdn-query-string-premium.md).

Sono disponibili tre modalità di stringa di query:

- **Ignora stringhe di query**: Modalità predefinita. In questa modalità il nodo POP (Point-Of-Presence) della rete CDN passa le stringhe di query dal richiedente al server di origine quando viene eseguita la prima richiesta e memorizza l'asset nella cache. Tutte le richieste successive dell'asset gestite dal POP ignoreranno le stringhe di query finché l'asset memorizzato nella cache non sarà scaduto.

- **Ignorare la memorizzazione nella cache per le stringhe di query**: In questa modalità, le richieste con stringhe di query non vengono memorizzate nel nodo POP della rete CDN. Il nodo POP recupera l'asset direttamente dal server di origine e lo passa al richiedente a ogni richiesta.

- **Memorizza nella cache tutti gli URL univoci**: In questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta inviata dal server di origine per una richiesta di example.ashx?q=test1 viene memorizzata nella cache nel nodo POP e restituita per le memorizzazioni nella cache successive con la stessa stringa di query. Una richiesta di example.ashx?q=test2 viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).
   
    >[!IMPORTANT] 
    > Non usare questa modalità quando la stringa di query contiene parametri che vengono modificati con ogni richiesta, ad esempio un ID di sessione o un nome utente, poiché si avrà come risultato una bassa percentuale di riscontri nella cache.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modifica delle impostazioni di memorizzazione nella cache della stringa di query per i profili standard della rete CDN
1. Aprire un profilo di rete CDN e quindi fare clic sull'endpoint della rete CDN che si desidera gestire.
   
   ![Endpoint del profilo di rete CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. Nel riquadro sinistro, in Impostazioni, fare clic su **Regole di memorizzazione nella cache**.
   
    ![Pulsante Regole di memorizzazione nella cache della rete CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Nell'elenco **Comportamento di memorizzazione nella cache della stringa di query** selezionare una modalità per la stringa di query e quindi fare clic su **Salva**.
   
   ![Opzioni della memorizzazione nella cache della stringa di query della rete CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Le modifiche alle impostazioni di memorizzazione nella cache delle stringhe possono non essere immediatamente visibili perché la propagazione della registrazione nella rete CDN di Azure richiede tempo:
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
> - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti. 



