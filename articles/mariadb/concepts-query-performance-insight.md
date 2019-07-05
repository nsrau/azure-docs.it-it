---
title: Informazioni dettagliate prestazioni query nel Database di Azure per MariaDB
description: Questo articolo descrive la funzionalità informazioni dettagliate prestazioni Query nel Database di Azure per MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48ff1fdc08e0df463ec48fd1415c7b67d5beb744
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462109"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Informazioni dettagliate prestazioni query nel Database di Azure per MariaDB

**Si applica a:**  per Database di Azure per MariaDB 10.2

> [!NOTE]
> Query Performance Insight è disponibile in anteprima.

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="common-scenarios"></a>Scenari comuni

### <a name="long-running-queries"></a>Query con esecuzione prolungata

- Identificazione delle query con il tempo di esecuzione più lungo nelle ultime X ore
- Identificazione delle prime N query in attesa delle risorse
 
### <a name="wait-statistics"></a>Le statistiche di attesa

- Natura di attesa comprensione per una query
- Informazioni sulle tendenze per attese risorse e in cui è presente contesa delle risorse

## <a name="permissions"></a>Autorizzazioni

**Proprietario** oppure **collaboratore** autorizzazioni necessarie per visualizzare il testo delle query in Query Performance Insight. ** Lettore** possono visualizzare i grafici e tabelle ma non testo della query.

## <a name="prerequisites"></a>Prerequisiti

Informazioni dettagliate prestazioni Query alla funzione, i dati in devono esistere il [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni

La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store.

Nella pagina del portale del Database di Azure per MariaDB server, selezionare **informazioni dettagliate prestazioni Query** sotto il **prestazioni intelligenti** sezione della barra dei menu.

### <a name="long-running-queries"></a>Query con esecuzione prolungata

Il **query a esecuzione prolungata** scheda Mostra le prime 5 query per durata media per l'esecuzione, aggregati in intervalli di 15 minuti. È possibile visualizzare altre query selezionando il **numero di query** elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare lo zoom e indietro le icone per visualizzare rispettivamente un periodo di tempo maggiori o minori.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Le statistiche di attesa 

> [!NOTE]
> Le statistiche di attesa sono pensate per risoluzione dei problemi di prestazioni di query. È consigliabile attivare solo per scopi diagnostici.

Le statistiche di attesa forniscono una visualizzazione degli eventi di attesa che si verificano durante l'esecuzione di una query specifica. Altre informazioni sui tipi di eventi di attesa nel [documentazione del motore di MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selezionare il **statistiche attesa** pressione di tab per visualizzare le visualizzazioni corrispondenti in attesa nel server.

Le query visualizzate nella visualizzazione di statistiche di attesa sono raggruppate per le query che presentano le attese più grande nell'intervallo di tempo specificato.

![Informazioni dettagliate prestazioni query rimane in attesa delle statistiche](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [di monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per MariaDB.