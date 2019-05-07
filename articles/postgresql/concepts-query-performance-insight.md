---
title: Informazioni dettagliate prestazioni query nel Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive la funzionalità informazioni dettagliate prestazioni Query nel Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d45b79e2ca3b3d478102bebdcff3c8892bef2cb5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067547"
---
# <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query 

**Si applica a:** Database di Azure per PostgreSQL - singolo Server 9.6 e 10

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per visualizzare il testo delle query in Informazioni dettagliate prestazioni query. **Lettore** può visualizzare i grafici e tabelle ma non il testo della query.

## <a name="prerequisites"></a>Prerequisiti
Per il funzionamento di Informazioni dettagliate prestazioni query, in [Query Store](concepts-query-store.md) devono essere presenti i dati.

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni
La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store. 

Nella pagina del portale del Database di Azure per il server PostgreSQL, selezionare **eseguire una Query performance Insight** sotto il **prestazioni intelligenti** sezione della barra dei menu.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Il **query a esecuzione prolungata** scheda Mostra le prime cinque query per durata media per l'esecuzione, aggregati in intervalli di 15 minuti. È possibile visualizzare altre query selezionando **Numero di query** dall'elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare le icone di ingrandimento e riduzione per visualizzare rispettivamente un intervallo di tempo superiore o inferiore.

Nella tabella sotto il grafico sono riportati altri dettagli sulle query con esecuzione prolungata in tale intervallo di tempo.

Selezionare la scheda **Statistiche di attesa** per visualizzare le corrispondenti visualizzazioni in attesa nel server.

![Informazioni dettagliate prestazioni query rimane in attesa delle statistiche](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.


