---
title: Esercitazione sul Monitoraggio e ottimizzazione nel Database di Azure per PostgreSQL
description: Questa esercitazione illustra il monitoraggio e l'ottimizzazione nel Database di Azure per PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 2e4d625cd123583baa30fde8e9826f5b57da49fe
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393589"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql"></a>Esercitazione: Monitorare e ottimizzare i Database di Azure per PostgreSQL

Il Database di Azure per PostgreSQL offre funzionalità che consentono di riconoscere e migliorare le prestazioni del server. In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Abilitare le query e la raccolta delle statistiche di attesa
> * Accedere e usare i dati raccolti
> * Visualizzare le prestazioni delle query e le statistiche di attesa nel corso del tempo
> * Analizzare il database per recuperare gli elementi consigliati per le prestazioni
> * Applicare le raccomandazioni per le prestazioni

## <a name="before-you-begin"></a>Prima di iniziare
È necessario un Database di Azure per il server PostgreSQL con la versione 9.6 o 10 di PostgreSQL. È possibile eseguire i passaggi in [Crea esercitazione](tutorial-design-database-using-azure-portal.md) per creare un server.

> [!IMPORTANT]
> **Query Store**, **Informazioni dettagliate prestazioni query**, e **Raccomandazione per le prestazioni** sono in anteprima pubblica in un numero limitato di aree.

## <a name="enabling-data-collection"></a>Abilitazione della raccolta dati
Il [Query Store](concepts-query-store.md) acquisisce una cronologia delle query e delle statistiche di attesa nel server e la archivia nel database **azure_sys** nel server. È una funzionalità che prevede il consenso esplicito. Per abilitarla:

1. Aprire il Portale di Azure.

2. Selezionare il server di Database di Azure per PostgreSQL.

3. Selezionare **Parametri del server** nella sezione **Impostazioni** del menu a sinistra.

4. Impostare **pg_qs.query_capture_mode** su**TOP** per iniziare a raccogliere i dati sulle prestazioni delle query. Impostare **pgms_wait_sampling.query_capture_mode** su**ALL**(Tutte) per iniziare a raccogliere le statistiche di attesa. Salvare.
   
   ![Parametri del server Query Store](./media/tutorial-performance-intelligence/query-store-parameters.png)

5. Per il salvataggio permanente del primo batch di dati nel database **azure_sys** possono essere necessari fino a 20 minuti.


## <a name="performance-insights"></a>Informazioni dettagliate sulle prestazioni
La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store. 

1. Nella pagina del portale del Database di Azure per il server PostgreSQL, selezionare **Informazioni dettagliate prestazioni query** sotto la sezione **Supporto + risoluzione dei problemi** del menu a sinistra.

2. La scheda**Query a esecuzione prolungata** mostra le prime 5 query in base alla durata media per esecuzione, aggregate in intervalli di 15 minuti. 
   
   ![Pagina di destinazione Informazioni dettagliate prestazioni query](./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png)

   È possibile visualizzare altre query selezionando **Numero di query** dall'elenco a discesa. I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

3. È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico.

4. Usare le icone di ingrandimento e riduzione per visualizzare rispettivamente un intervallo di tempo superiore o inferiore.

5. Visualizzare la tabella sotto il grafico per altri dettagli sulle query con esecuzione prolungata in tale intervallo di tempo.

6. Selezionare la scheda **Statistiche di attesa** per visualizzare le corrispondenti visualizzazioni in attesa nel server.
   
   ![Statistiche di attesa di Informazioni dettagliate prestazioni query](./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png)

### <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per visualizzare il testo delle query in Informazioni dettagliate prestazioni query. **Lettore** può visualizzare i grafici e tabelle ma non il testo della query.


## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) analizza i carichi di lavoro sul server per identificare gli indici con la possibilità di migliorare le prestazioni.

1. Aprire **Raccomandazioni per le prestazioni** dalla sezione **Supporto + risoluzione dei problemi** della barra dei menu nella pagina del portale di Azure per il server PostgreSQL.
   
   ![Pagina di destinazione Elementi consigliati per le prestazioni](./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png)

2. Selezionare **Analizza** e scegliere un database. Viene avviata l'analisi.

3. A seconda del carico di lavoro, l'operazione potrebbe richiedere alcuni minuti. Al termine dell'analisi, verrà inviata una notifica nel portale.

4. La finestra **Elementi consigliati per le prestazioni** illustrerà un elenco di raccomandazioni se sono state trovate. 

5. Un elemento consigliato illustrerà informazioni su **Database**, **Tabella**, **Colonna**, e **Dimensioni indice** pertinenti.

   ![Risultati Elementi consigliati per le prestazioni](./media/tutorial-performance-intelligence/performance-recommendations-result.png)

6. Per implementare l'elemento consigliato, copiare il testo della query ed eseguirlo dal client preferito.

### <a name="permissions"></a>Autorizzazioni
Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per eseguire l'analisi usando la funzionalità Raccomandazioni per gli elementi consigliati.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [monitoraggio e ottimizzazione](concepts-monitoring.md) nel Database di Azure per PostgreSQL.