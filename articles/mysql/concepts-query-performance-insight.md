---
title: Informazioni dettagliate prestazioni query nel Database di Azure per MySQL
description: Questo articolo descrive la funzionalità informazioni dettagliate prestazioni Query nel Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: MySQL
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 1243ae8ae20d08ea643661606639abedbc56ab9c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078781"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Informazioni dettagliate prestazioni query nel Database di Azure per MySQL

**Si applica a:**  per Database di Azure per MySQL 5.7

> [!NOTE]
> Query Performance Insight è disponibile in anteprima. Supporto per informazioni dettagliate prestazioni Query nel portale di Azure viene implementato e potrebbe non essere ancora disponibile nella propria area.

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="permissions"></a>Autorizzazioni

**Proprietario** oppure **collaboratore** autorizzazioni necessarie per visualizzare il testo delle query in Query Performance Insight. ** Lettore** possono visualizzare i grafici e tabelle ma non testo della query.

## <a name="prerequisites"></a>Prerequisiti

Informazioni dettagliate prestazioni Query alla funzione, i dati in devono esistere il [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni

La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store.

Nella pagina del portale del Database di Azure per il server MySQL, selezionare **informazioni dettagliate prestazioni Query** sotto il **prestazioni intelligenti** sezione della barra dei menu.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

Il **query a esecuzione prolungata** scheda Mostra le prime 5 query per durata media per l'esecuzione, aggregati in intervalli di 15 minuti. È possibile visualizzare altre query selezionando il **numero di query** elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare lo zoom e indietro le icone per visualizzare rispettivamente un periodo di tempo maggiori o minori.

Selezionare il **statistiche attesa** pressione di tab per visualizzare le visualizzazioni corrispondenti in attesa nel server.

Le query visualizzate nella visualizzazione di statistiche di attesa sono raggruppate per le query che presentano le attese più grande nell'intervallo di tempo specificato.

![Informazioni dettagliate prestazioni query rimane in attesa delle statistiche](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [di monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per MySQL.