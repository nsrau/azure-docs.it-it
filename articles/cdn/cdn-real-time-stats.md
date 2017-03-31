---
title: Statistiche in tempo reale nella rete CDN di Azure | Documentazione Microsoft
description: Le statistiche in tempo reale forniscono i dati in tempo reale sulle prestazioni della rete CDN durante la distribuzione di contenuto ai client.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd


---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statistiche in tempo reale nella rete CDN di Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overview
Questo documento illustra le statistiche in tempo reale nella rete CDN di Microsoft Azure.  Questa funzionalità fornisce dati in tempo reale, ad esempio la larghezza di banda, gli stati della cache e le connessioni simultanee al profilo della rete CDN per il recapito di contenuto ai client. In questo modo viene abilitato il monitoraggio continuo dell'integrità del servizio in qualsiasi momento, inclusi gli eventi di go-live.

Sono disponibili i grafici seguenti:

* [Larghezza di banda](#bandwidth)
* [Codici di stato](#status-codes)
* [Stati della cache](#cache-statuses)
* [Connessioni](#connections)

## <a name="accessing-real-time-stats"></a>Accesso alle statistiche in tempo reale
1. Nel [portale di Azure](https://portal.azure.com)passare al profilo della rete CDN.
   
    ![Pannello del profilo di rete CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
3. Passare il puntatore sulla scheda **Analisi**, quindi sul riquadro a comparsa **Statistiche in tempo reale**.  Fare clic su **Oggetto di grandi dimensioni HTTP**.
   
    ![Portale di gestione della rete CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Vengono visualizzati i grafici delle statistiche in tempo reale.

Ogni grafico visualizza le statistiche in tempo reale per l'intervallo di tempo selezionato, a partire dal momento del caricamento della pagina.  I grafici vengono aggiornati automaticamente a intervalli di pochi secondi.  Il pulsante **Aggiorna grafico** , se presente, consente di cancellare il grafico, dopodiché saranno visualizzati solo i dati selezionati.

## <a name="bandwidth"></a>Larghezza di banda
![Grafico Larghezza di banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

Il grafico **Larghezza di banda** visualizza la quantità di larghezza di banda usata per la piattaforma corrente nel periodo di tempo specificato. La parte ombreggiata del grafico indica l'utilizzo della larghezza di banda. La quantità esatta di larghezza di banda attualmente in uso viene visualizzata direttamente sotto il grafico a linee.

## <a name="status-codes"></a>Codici di stato
![Grafico Codici di stato](./media/cdn-real-time-stats/cdn-status-codes.png)

Il grafico **Codici di stato** indica la frequenza con cui si verificano alcuni codici di risposta HTTP durante l'intervallo di tempo selezionato.

> [!TIP]
> Per una descrizione di ogni opzione relativa ai codici di stato HTTP, vedere [Azure CDN HTTP Status Codes](https://msdn.microsoft.com/library/mt759238.aspx)(Codici di stato HTTP della rete CDN di Azure).
> 
> 

Direttamente sopra al grafico viene visualizzato un elenco dei codici di stato HTTP. Questo elenco indica ogni codice di stato che può essere incluso nel grafico a linee e il numero di occorrenze correnti al secondo per ogni codice di stato. Per impostazione predefinita, viene visualizzata una riga per ognuno di questi codici di stato nel grafico. Tuttavia, è possibile scegliere di monitorare solo i codici di stato che hanno un significato speciale per la configurazione della rete CDN. A tale scopo, selezionare i codici di stato desiderati e deselezionare tutte le altre opzioni, quindi fare clic su **Aggiorna grafico**. 

È possibile nascondere temporaneamente i dati registrati per un codice di stato specifico.  Nella legenda direttamente sotto il grafico selezionare il codice di stato che si vuole nascondere. Il codice di stato verrà nascosto immediatamente nel grafico. Se si seleziona di nuovo il codice di stato, l'opzione verrà visualizzata nuovamente.

## <a name="cache-statuses"></a>Stati della cache
![Grafico Stati della cache](./media/cdn-real-time-stats/cdn-cache-status.png)

Il grafico **Stati della cache** indica la frequenza con cui si verificano alcuni tipi di stati della cache durante l'intervallo di tempo selezionato. 

> [!TIP]
> Per una descrizione di ogni opzione relativa agli stati della cache, vedere [Azure CDN Cache Status Codes](https://msdn.microsoft.com/library/mt759237.aspx)(Codici di stato della cache della rete CDN di Azure).
> 
> 

Direttamente sopra al grafico viene visualizzato un elenco di codici di stato della cache. Questo elenco indica ogni codice di stato che può essere incluso nel grafico a linee e il numero di occorrenze correnti al secondo per ogni codice di stato. Per impostazione predefinita, viene visualizzata una riga per ognuno di questi codici di stato nel grafico. Tuttavia, è possibile scegliere di monitorare solo i codici di stato che hanno un significato speciale per la configurazione della rete CDN. A tale scopo, selezionare i codici di stato desiderati e deselezionare tutte le altre opzioni, quindi fare clic su **Aggiorna grafico**. 

È possibile nascondere temporaneamente i dati registrati per un codice di stato specifico.  Nella legenda direttamente sotto il grafico selezionare il codice di stato che si vuole nascondere. Il codice di stato verrà nascosto immediatamente nel grafico. Se si seleziona di nuovo il codice di stato, l'opzione verrà visualizzata nuovamente.

## <a name="connections"></a>Connessioni
![Grafico Connessioni](./media/cdn-real-time-stats/cdn-connections.png)

Questo grafico indica il numero di connessioni stabilite con i server perimetrali. Ogni richiesta per un asset che passa attraverso la rete CDN costituisce una connessione.

## <a name="next-steps"></a>Passaggi successivi
* Per impostare la ricezione di notifiche, vedere [Avvisi in tempo reale nella rete CDN di Microsoft Azure](cdn-real-time-alerts.md)
* Per un'analisi più approfondita, vedere [Report HTTP avanzati nella rete CDN di Microsoft Azure](cdn-advanced-http-reports.md)
* Vedere [Analizzare i modelli di utilizzo della rete CDN di Azure](cdn-analyze-usage-patterns.md)




<!--HONumber=Jan17_HO4-->


