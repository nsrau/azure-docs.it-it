---
title: Informazioni dettagliate prestazioni query - Database di Azure per MySQL
description: Questo articolo descrive la funzionalità Informazioni dettagliate prestazioni query in Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 51b478e5184c79e11d95ff004a652b2e5298558f
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402603"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Informazioni dettagliate prestazioni query in Database di Azure per MySQL

**Si applica a:** Database di Azure per MySQL 5.7, 8.0

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="common-scenarios"></a>Scenari comuni

### <a name="long-running-queries"></a>Query a esecuzione prolungata

- Identificazione delle query con il tempo di esecuzione più lungo nelle ultime X ore
- Identificazione delle prime N query in attesa delle risorse
 
### <a name="wait-statistics"></a>Statistiche di attesa

- Informazioni sulla natura dell'attesa per le query
- Informazioni sulle tendenze per le attese di risorse e su dove esiste il conflitto di risorse

## <a name="permissions"></a>Autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per visualizzare il testo delle query in Informazioni dettagliate prestazioni query. **Lettore** può visualizzare i grafici e tabelle ma non il testo della query.

## <a name="prerequisites"></a>Prerequisiti

Per il funzionamento di Informazioni dettagliate prestazioni query, in [Query Store](concepts-query-store.md) devono essere presenti i dati.

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni

La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store.

Nella pagina del portale del server di Database di Azure per MySQL selezionare **Informazioni dettagliate prestazioni query** nella sezione **Prestazioni intelligenti** della barra dei menu.

### <a name="long-running-queries"></a>Query a esecuzione prolungata

La scheda**Query a esecuzione prolungata** mostra le prime 5 query in base alla durata media per esecuzione, aggregate in intervalli di 15 minuti. È possibile visualizzare altre query selezionando **Numero di query** nell'elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare le icone di ingrandimento e riduzione per visualizzare rispettivamente un intervallo di tempo superiore o inferiore.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Statistiche di attesa

> [!NOTE]
> Le statistiche di attesa sono destinate alla risoluzione dei problemi di prestazioni delle query. È consigliabile attivarle solo a scopo di risoluzione dei problemi. <br>Se nel portale di Azure viene visualizzato il messaggio di errore "*Si è verificato un errore per 'Microsoft.DBforMySQL', quindi non è possibile soddisfare la richiesta. Se il problema persiste o non è previsto, contattare il supporto tecnico specificando queste informazioni*" durante la visualizzazione delle statistiche di attesa, usare un periodo di tempo inferiore.

Le statistiche di attesa forniscono una visualizzazione degli eventi di attesa che si verificano durante l'esecuzione di una query specifica. Per altre informazioni sui tipi di evento di attesa, vedere la [documentazione del motore MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selezionare la scheda **Statistiche di attesa** per visualizzare le corrispondenti visualizzazioni in attesa nel server.

Le query visualizzate nelle statistiche di attesa sono raggruppate in base a quelle che mostrano le attese più lunghe durante l'intervallo di tempo specificato.

![Statistiche di attesa di Informazioni dettagliate prestazioni query](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) in Database di Azure per MySQL.