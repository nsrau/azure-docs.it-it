---
title: Informazioni dettagliate prestazioni query-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità Informazioni dettagliate prestazioni query di database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768385"
---
# <a name="query-performance-insight"></a>Analisi delle prestazioni di query 

**Si applica a:** Database di Azure per PostgreSQL: versioni a server singolo 9,6, 10, 11

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="permissions"></a>autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per visualizzare il testo delle query in Informazioni dettagliate prestazioni query. **Lettore** può visualizzare i grafici e tabelle ma non il testo della query.

## <a name="prerequisites"></a>Prerequisiti
Per il funzionamento di Informazioni dettagliate prestazioni query, in [Query Store](concepts-query-store.md) devono essere presenti i dati.

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni
La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store. 

Nella pagina del portale del database di Azure per il server PostgreSQL selezionare **informazioni dettagliate prestazioni query** nella sezione **prestazioni intelligenti** della barra dei menu.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

La scheda **query con esecuzione prolungata** Mostra le prime cinque query per durata media per esecuzione, aggregate in intervalli di 15 minuti. È possibile visualizzare altre query selezionando **Numero di query** dall'elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare le icone di ingrandimento e riduzione per visualizzare rispettivamente un intervallo di tempo superiore o inferiore.

Nella tabella sotto il grafico sono riportati altri dettagli sulle query con esecuzione prolungata in tale intervallo di tempo.

Selezionare la scheda **Statistiche di attesa** per visualizzare le corrispondenti visualizzazioni in attesa nel server.

![Informazioni dettagliate prestazioni query attese statistiche](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Considerazioni
* Informazioni dettagliate prestazioni query non è disponibile per le [repliche di lettura](concepts-read-replicas.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.


