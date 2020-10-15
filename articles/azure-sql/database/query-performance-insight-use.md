---
title: Informazioni dettagliate prestazioni query
description: Il monitoraggio delle prestazioni delle query identifica le query più dispendiose in termini di utilizzo della CPU e con esecuzione prolungata per database singoli e in pool nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 03/10/2020
ms.openlocfilehash: be7e4a641e5b5ac2ef755037142cfd8063d66b5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448893"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Informazioni dettagliate prestazioni query per il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Informazioni dettagliate prestazioni query fornisce un'analisi intelligente delle query per i database singoli e in pool. Consente di identificare le prime query per consumo di risorse e con esecuzione prolungata nel carico di lavoro. Questo consente di trovare le query da ottimizzare per migliorare le prestazioni complessive del carico di lavoro e usare in modo efficiente la risorsa a cui si sta effettuando il pagamento. Informazioni dettagliate prestazioni query consente di dedicare meno tempo alla risoluzione dei problemi relativi alle prestazioni del database fornendo:

* Informazioni più approfondite sull'utilizzo delle risorse dei database (DTU)
* Dettagli sulle query principali sul database in base alla CPU, alla durata e al numero di esecuzioni (potenziali candidati di ottimizzazione per miglioramenti delle prestazioni)
* Possibilità di eseguire il drill-down nei dettagli di una query per visualizzare il testo della query e la cronologia dell'utilizzo delle risorse
* Annotazioni che mostrano le raccomandazioni sulle prestazioni dagli [Advisor di database](database-advisor-implement-performance-recommendations.md)

![Informazioni dettagliate prestazioni query](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Prerequisiti

Per Informazioni dettagliate sulle prestazioni delle query è necessario che l' [archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia attivo nel database. Per impostazione predefinita, viene abilitato automaticamente per tutti i database nel database SQL di Azure. Se Query Store non è in esecuzione, il portale di Azure richiederà di abilitarlo.

> [!NOTE]
> Se nel portale viene visualizzato un messaggio che indica che Query Store non è configurato correttamente nel database, vedere [Ottimizzare la configurazione di Query Store](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Autorizzazioni

Sono necessarie le seguenti autorizzazioni di [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) per usare informazioni dettagliate prestazioni query:

* Le autorizzazioni **Lettore**, **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare le query principali a livello di uso delle risorse e i grafici.
* Le autorizzazioni **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare il testo della query.

## <a name="use-query-performance-insight"></a>Usare Informazioni dettagliate prestazioni query

Query Performance Insight è facile da usare:

1. Aprire il [portale di Azure](https://portal.azure.com/) e individuare il database da esaminare.
2. Dal menu a sinistra aprire **Intelligent Performance**  >  **informazioni dettagliate prestazioni query**.
  
   ![Informazioni dettagliate prestazioni query nel menu](./media/query-performance-insight-use/tile.png)

3. Nella prima scheda, esaminare l'elenco delle query principali a livello di utilizzo delle risorse.
4. Selezionare una singola query per visualizzarne i dettagli.
5. Aprire suggerimenti sulle prestazioni **intelligenti**  >  **Performance recommendations** e verificare se sono disponibili raccomandazioni per le prestazioni. Per ulteriori informazioni sulle raccomandazioni per le prestazioni predefinite, vedere [Advisor per database SQL di Azure](database-advisor-implement-performance-recommendations.md).
6. Usare i dispositivi di scorrimento o le icone dello zoom per modificare l'intervallo osservato.

   ![Dashboard delle prestazioni](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Affinché il database SQL di Azure esegua il rendering delle informazioni in Informazioni dettagliate prestazioni query, Query Store necessario acquisire un paio di ore di dati. Se il database non ha alcuna attività o Query Store non è attivo in un determinato periodo, i grafici saranno vuoti quando si visualizza tale intervallo di tempo in Informazioni dettagliate prestazioni query. È possibile abilitare Query Store in qualsiasi momento, se non è in esecuzione. Per altre informazioni, vedere [Best practices with Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store) (Procedure consigliate per Query Store).
>

Per altri suggerimenti sulle prestazioni del database, selezionare [Raccomandazioni](database-advisor-implement-performance-recommendations.md) nel pannello di spostamento di Informazioni dettagliate prestazioni query.

![Scheda Raccomandazioni](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Esaminare le query principali a livello di uso della CPU

Per impostazione predefinita, alla prima apertura Informazioni dettagliate prestazioni query mostra le cinque query principali a livello di uso della CPU.

1. Selezionare o deselezionare le singole query per includerle o escluderle dal grafico usando le caselle di controllo.

   La linea superiore visualizza la percentuale DTU complessiva per il database. Le barre mostrano la percentuale di uso della CPU per le query selezionate durante l'intervallo selezionato. Ad esempio, se è selezionata l'opzione **Settimana precedente**, ogni barra rappresenta un singolo giorno.

   ![Query principali](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > La linea DTU visualizzata viene aggregata in base a un valore massimo di consumo per periodi di un'ora. È pensata per consentire solo un confronto generale con le statistiche di esecuzione delle query. In alcuni casi, l'uso DTU può apparire troppo elevato rispetto alle query eseguite, ma la situazione potrebbe essere diversa.
   >
   > Ad esempio, se una query raggiunge il limite del 100% di DTU solo per pochi minuti, la linea DTU in Informazioni dettagliate prestazioni query mostrerà un'intera ora di consumo al 100% (come conseguenza del valore massimo aggregato).
   >
   > Per un confronto più preciso (fino a 1 minuto), è consigliabile creare un grafico dell'uso DTU personalizzato:
   >
   > 1. Nella portale di Azure selezionare monitoraggio del **database SQL di Azure**  >  **Monitoring**.
   > 2. Selezionare **Metriche**.
   > 3. Selezionare **+Aggiungi grafico**.
   > 4. Selezionare la percentuale DTU nel grafico.
   > 5. Inoltre, selezionare **Ultime 24 ore** nel menu superiore sinistro e impostare l'opzione su un minuto.
   >
   > Usare il grafico DTU personalizzato con un maggiore livello di dettaglio per eseguire confronti con il grafico di esecuzione delle query.

   La griglia inferiore mostra informazioni aggregate per le query visibili:

   * ID query, ovvero un identificatore univoco per la query nel database.
   * Uso della CPU per query durante un intervallo osservabile, che dipende dalla funzione di aggregazione.
   * Durata per ogni query, che anche in questo caso dipende dalla funzione di aggregazione.
   * Numero totale di esecuzioni per una query specifica.

2. Se i dati non sono aggiornati, selezionare il pulsante **Aggiorna**.

3. Usare i dispositivi di scorrimento e i pulsanti dello zoom per modificare l'intervallo di osservazione ed esaminare i picchi nel consumo:

   ![Dispositivi di scorrimento e pulsanti dello zoom per modificare l'intervallo](./media/query-performance-insight-use/zoom.png)

4. Facoltativamente, è possibile selezionare la scheda **Personalizzato** per personalizzare la visualizzazione per:

   * Metrica (CPU, durata, conteggio delle esecuzioni).
   * Intervallo di tempo (ultime 24 ore, ultima settimana, ultimo mese).
   * Numero di query.
   * Funzione di aggregazione.
  
   ![Scheda Personalizzato](./media/query-performance-insight-use/custom-tab.png)
  
5. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   > [!IMPORTANT]
   > Informazioni dettagliate prestazioni query supporta la visualizzazione solo delle prime 5-20 query a livello di uso, a seconda della selezione. Il database può eseguire molte altre query oltre a quelle principali visualizzate e tali query non verranno incluse nel grafico.
   >
   > Potrebbe essere presente un tipo di carico di lavoro del database in cui numerose query di piccole dimensioni, oltre a quelle principali visualizzate, vengono eseguite di frequente e usano la maggior parte delle DTU. Queste query non vengono mostrate nel grafico delle prestazioni.
   >
   > Ad esempio, una query potrebbe aver usato una notevole quantità di DTU per un certo periodo di tempo, anche se il consumo totale nel periodo osservato è minore di altre query principali. In tal caso, l'uso delle risorse di questa query non sarà visibile nel grafico.
   >
   > Se è necessario esaminare le esecuzioni delle query principali oltre i limiti di Informazioni dettagliate prestazioni query, è consigliabile usare [Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md) per funzionalità avanzate di monitoraggio e risoluzione dei problemi delle prestazioni dei database.
   >

## <a name="view-individual-query-details"></a>Visualizzare i dettagli delle singole query

Per visualizzare i dettagli relativi alle query:

1. Selezionare qualsiasi query nell'elenco delle query principali.

    ![Elenco delle query principali](./media/query-performance-insight-use/details.png)

   Verrà aperta una visualizzazione dettagliata. Tale visualizzazione mostra l'uso della CPU, la durata e il conteggio delle esecuzioni nel corso del tempo.

2. Selezionare le funzionalità del grafico per informazioni dettagliate.

   * Il grafico superiore mostra una linea con la percentuale DTU complessiva per il database. Le barre rappresentano la percentuale CPU usata dalla query selezionata.
   * Nel secondo grafico viene mostrata la durata totale della query selezionata.
   * Nel grafico in fondo viene mostrato il numero totale delle esecuzioni effettuate dalla query selezionata.

   ![Dettagli sulle query](./media/query-performance-insight-use/query-details.png)

3. Facoltativamente, usare i dispositivi di scorrimento, i pulsanti dello zoom oppure fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione dei dati della query o per mostrare un intervallo di tempo diverso.

   > [!IMPORTANT]
   > Informazioni dettagliate prestazioni query non acquisisce le query DDL. In alcuni casi, potrebbe non acquisire tutte le query ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Esaminare le query principali in base alla durata

Due metriche in Informazioni dettagliate prestazioni query consentono di individuare i potenziali colli di bottiglia: durata e conteggio delle esecuzioni.

Le query con esecuzione prolungata hanno le maggiori probabilità di bloccare gli altri utenti e le risorse più a lungo, nonché di limitare la scalabilità. Sono anche i candidati ideali per l'ottimizzazione.

Per identificare le query con esecuzione prolungata:

1. Aprire la scheda **Personalizzato** in Informazioni dettagliate prestazioni query per il database selezionato.
2. Impostare le metriche su **Durata**.
3. Selezionare il numero di query e l'intervallo di osservazione.
4. Selezionare la funzione di aggregazione:

   * **Somma** esegue la somma del tempo di esecuzione di tutte le query durante l'intero intervallo di osservazione.
   * **Max** individua le query con il tempo di esecuzione massimo durante l'intero intervallo di osservazione.
   * **Media** rileva il tempo medio di esecuzione di tutte le query e mostra i valori medi più alti tra quelli rilevati.

   ![Durata delle query](./media/query-performance-insight-use/top-duration.png)

5. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   > [!IMPORTANT]
   > Regolando la visualizzazione della query, la linea DTU non viene aggiornata. La linea DTU mostra sempre il valore massimo di consumo per l'intervallo.
   >
   > Per comprendere più in dettaglio il consumo di DTU del database (fino a 1 minuto), è consigliabile creare un grafico personalizzato nel portale di Azure:
   >
   > 1. Selezionare monitoraggio del **database SQL di Azure**  >  **Monitoring**.
   > 2. Selezionare **Metriche**.
   > 3. Selezionare **+Aggiungi grafico**.
   > 4. Selezionare la percentuale DTU nel grafico.
   > 5. Inoltre, selezionare **Ultime 24 ore** nel menu superiore sinistro e impostare l'opzione su un minuto.
   >
   > È consigliabile usare il grafico DTU personalizzato per eseguire un confronto con il grafico delle prestazioni delle query.
   >

## <a name="review-top-queries-per-execution-count"></a>Esaminare le query principali in base al conteggio delle esecuzioni

Un'applicazione utente che usa il database potrebbe rallentare, anche se un numero elevato di esecuzioni potrebbe non influire sul database stesso e l'uso delle risorse è limitato.

In alcuni casi, un conteggio di esecuzioni molto elevato potrebbe causare l'aumento dei round trip di rete. I round trip influiscono sulle prestazioni. Sono soggetti alla latenza di rete e alla latenza di server downstream.

Ad esempio, molti siti Web basati sui dati accedono in maniera massiccia al database per tutte le richieste dell'utente. Sebbene il pool di connessioni contribuisca, l'aumento del traffico di rete e del carico di elaborazione sul server può rallentare le prestazioni. In generale, mantenere i round trip al minimo.

Per identificare le query eseguite di frequente ("Chaty"):

1. Aprire la scheda **Personalizzato** in Informazioni dettagliate prestazioni query per il database selezionato.
2. Impostare le metriche su **Conteggio delle esecuzioni**.
3. Selezionare il numero di query e l'intervallo di osservazione.
4. Selezionare il pulsante **Vai >** per accedere alla visualizzazione personalizzata.

   ![Conteggio delle esecuzioni delle query](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Informazioni sulle annotazioni di ottimizzazione delle prestazioni

Durante l'esplorazione del carico di lavoro in Informazioni dettagliate prestazioni query, è possibile notare la presenza di icone con una linea verticale nella parte superiore del grafico.

Queste icone sono annotazioni. Mostrano le raccomandazioni sulle prestazioni da [Advisor per database SQL di Azure](database-advisor-implement-performance-recommendations.md). Passando il puntatore su un'annotazione, è possibile ottenere informazioni riepilogative sulle raccomandazioni per le prestazioni.

   ![Annotazione delle query](./media/query-performance-insight-use/annotation.png)

Per ottenere altre informazioni o applicare le raccomandazioni di Advisor, selezionare l'icona per aprire i dettagli dell'azione consigliata. Se si tratta di una raccomandazione attiva, è possibile applicarla direttamente dal portale.

   ![Dettagli delle annotazioni delle query](./media/query-performance-insight-use/annotation-details.png)

In alcuni casi, a causa del livello di zoom, è possibile che le annotazioni vicine tra loro vengano compresse in una singola annotazione. Informazioni dettagliate prestazioni query rappresenta gli elementi come un'icona di annotazione di gruppo. Selezionando l'icona di un'annotazione di gruppo, viene aperto un nuovo pannello che elenca le annotazioni.

Correlare le query e le azioni di ottimizzazione delle prestazioni può servire ad avere una migliore comprensione del carico di lavoro.

## <a name="optimize-the-query-store-configuration"></a>Ottimizzare la configurazione del Query Store

Durante l'uso di Informazioni dettagliate prestazioni query, possono essere visualizzati messaggi di errore di Query Store simili ai seguenti:

* "Archivio query non è correttamente configurato in questo database. Per altre informazioni, fare clic qui."
* "Archivio query non è correttamente configurato in questo database. Fare clic qui per modificare le impostazioni."

Questi messaggi in genere vengono visualizzati quando Query Store non è in grado di raccogliere nuovi dati.

Il primo caso si verifica quando Query Store è in stato di sola lettura e i parametri sono impostati in modo ottimale. È possibile risolvere il problema aumentando le dimensioni dell'archivio dati o svuotando Query Store. Se si svuota Query Store, tutti i dati di telemetria raccolti in precedenza andranno persi.

   ![Dettagli di Query Store](./media/query-performance-insight-use/qds-off.png)

Il secondo caso si verifica quando Query Store non è abilitato o se i parametri non sono impostati in modo ottimale. È possibile modificare i criteri di conservazione e acquisizione e anche abilitare Query Store eseguendo i comandi seguenti forniti da [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o dal portale di Azure.

### <a name="recommended-retention-and-capture-policy"></a>Criteri di conservazione e acquisizione consigliati

Esistono due tipi di criteri di conservazione:

* **Basato sulle dimensioni**: se il criterio è impostato su **automatico**, i dati verranno puliti automaticamente quando viene raggiunta la dimensione massima.
* **Basato su tempo**: per impostazione predefinita, questo criterio è impostato su 30 giorni. Se Query Store esaurisce lo spazio, verranno eliminate le informazioni sulle query più vecchie di 30 giorni.

È possibile impostare i criteri di acquisizione su:

* **All**: query Store acquisisce tutte le query.
* **Auto**: query Store ignora le query e le query non frequenti con una durata di compilazione e di esecuzione non significativa. Le soglie per il conteggio delle esecuzioni e la durata di compilazione ed esecuzione vengono stabilite internamente. Questa è l'opzione predefinita.
* **None**: query Store interrompe l'acquisizione di nuove query, ma vengono comunque raccolte le statistiche di runtime per le query già acquisite.

È consigliabile impostare tutti i criteri su **AUTOMATICO** e i criteri di pulizia su 30 giorni eseguendo i comandi seguenti da [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o dal portale di Azure. Sostituire `YourDB` con il nome del database.

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Per aumentare le dimensioni di Query Store, connettersi a un database tramite [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o il portale di Azure ed eseguire la query seguente. Sostituire `YourDB` con il nome del database.

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Applicando queste impostazioni, Query Store raccoglierà i dati di telemetria per le nuove query. Se è necessario che Query Store sia subito operativo, è possibile scegliere di svuotare Query Store eseguendo la query seguente tramite SSMS o il portale di Azure. Sostituire `YourDB` con il nome del database.

> [!NOTE]
> Eseguendo la query seguente, verranno eliminati tutti i dati di telemetria monitorati raccolti in precedenza in Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Passaggi successivi

Si consiglia di usare [analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md) per il monitoraggio avanzato delle prestazioni di una vasta gamma di database singoli e in pool, pool elastici, istanze gestite e database di istanza.
