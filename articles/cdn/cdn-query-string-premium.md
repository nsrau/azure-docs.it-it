---
title: 'Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Premium | Microsoft Docs'
description: La funzionalità di memorizzazione nella cache con stringhe di query della rete CDN di Azure controlla il modo in cui i file vengono memorizzati nella cache quando una richiesta Web contiene una stringa di query. Questo articolo descrive la memorizzazione nella cache con stringhe di query nel prodotto della rete CDN Premium di Azure con tecnologia Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2f0a361d53489e22ccc8e41406e5b86b423ea2f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324827"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Premium
> [!div class="op_single_selector"]
> * [Livello Standard](cdn-query-string.md)
> * [Livello Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Panoramica
La rete per la distribuzione di contenuti (CDN) di Azure consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non ha importanza. 

> [!IMPORTANT]
> I prodotti della rete CDN Standard e Premium forniscono la stessa funzionalità di memorizzazione nella cache delle stringhe di query, ma l'interfaccia utente è diversa. Questo articolo descrive l'interfaccia della **rete CDN Premium di Azure fornita da Verizon**. Per memorizzare nella cache stringhe di query con prodotti di rete CDN Standard di Azure, vedere [Controllare il comportamento di memorizzazione nella cache con stringhe di query della rete CDN di Azure: livello Standard](cdn-query-string.md).
>


Sono disponibili tre modalità di stringa di query:

- **standard-cache**: Modalità predefinita. In questa modalità il nodo POP (Point-Of-Presence) della rete CDN passa le stringhe di query dal richiedente al server di origine quando viene eseguita la prima richiesta e memorizza l'asset nella cache. Tutte le richieste successive dell'asset gestite dal server POP ignoreranno le stringhe di query finché l'asset memorizzato nella cache non sarà scaduto.

    >[!IMPORTANT] 
    > Se per un qualsiasi percorso in questo account è abilitata l'autorizzazione basata su token, è possibile usare solo la modalità standard-cache. 

- **no-cache**: In questa modalità, le richieste con stringhe di query non vengono memorizzate nel nodo POP della rete CDN. Il nodo POP recupera l'asset direttamente dal server di origine e lo passa al richiedente a ogni richiesta.

- **unique-cache**: In questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta inviata dal server di origine per una richiesta di example.ashx?q=test1 viene memorizzata nella cache nel nodo POP e restituita per le memorizzazioni nella cache successive con la stessa stringa di query. Una richiesta di example.ashx?q=test2 viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).
   
    >[!IMPORTANT] 
    > Non usare questa modalità quando la stringa di query contiene parametri che vengono modificati con ogni richiesta, ad esempio un ID di sessione o un nome utente, poiché si avrà come risultato una bassa percentuale di riscontri nella cache.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modifica delle impostazioni di memorizzazione nella cache della stringa di query per i profili premium della rete CDN
1. Aprire il profilo della rete CDN e quindi fare clic su **Manage** (Gestisci).
   
    ![Pulsante Manage (Gestisci) del profilo di rete CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **HTTP Large** (HTTP esteso) e quindi passare il puntatore sul menu a comparsa **Impostazioni cache**. Fare clic su **Comportamento di memorizzazione nella cache della stringa di query**.
   
    Vengono visualizzate le opzioni di memorizzazione nella cache della stringa di query.
   
    ![Opzioni della memorizzazione nella cache della stringa di query della rete CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selezionare una modalità della stringa di query e quindi fare clic su **Aggiorna**.

> [!IMPORTANT]
> Le modifiche delle impostazioni di memorizzazione nella cache delle stringhe non sono immediatamente visibili perché la propagazione della registrazione nella rete CDN richiede tempo. La propagazione viene in genere completata in 10 minuti.
 

