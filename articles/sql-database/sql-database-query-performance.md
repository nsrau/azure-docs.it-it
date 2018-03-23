---
title: Informazioni dettagliate sulle prestazioni delle query per il database SQL di Azure | Documentazione Microsoft
description: Il monitoraggio delle prestazioni delle query identifica le query principali a livello di utilizzo di CPU per un database SQL di Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 18d03ffcd586a809b37cbc1ca9a7843c25c7758d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-query-performance-insight"></a>Query Performance Insight del database SQL di Azure
La gestione e l'ottimizzazione delle prestazioni dei database relazionali è un'attività complessa che richiede un'esperienza significativa e un investimento elevato in termini di tempo. Le Informazioni dettagliate sulle prestazioni delle query consentono di dedicare meno tempo alla risoluzione dei problemi delle prestazioni del database, offrendo i vantaggi seguenti:​

* Informazioni più approfondite sull'utilizzo delle risorse del database (DTU). 
* Query principali a livello di CPU/durata/conteggio delle esecuzioni, che possono essere potenzialmente ottimizzate per migliorare le prestazioni.
* La possibilità di eseguire il drill-down nei dettagli di una query, visualizzarne il testo e la cronologia di utilizzo delle risorse. 
* Le annotazioni relative all'ottimizzazione delle prestazioni che descrivono le azioni eseguite da [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>prerequisiti
* Per Informazioni dettagliate sulle prestazioni delle query è necessario che l' [archivio query](https://msdn.microsoft.com/library/dn817826.aspx) sia attivo nel database. Se l'archivio query non è in esecuzione, il portale richiede di attivarlo.

## <a name="permissions"></a>Autorizzazioni
Le autorizzazioni di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) seguenti sono necessarie per usare Query Performance Insight: 

* Le autorizzazioni **Lettore**, **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare le query principali che usano le risorse e i grafici. 
* Le autorizzazioni **Proprietario**, **Collaboratore**, **Collaboratore database SQL** o **Collaboratore SQL Server** sono necessarie per visualizzare il testo della query.

## <a name="using-query-performance-insight"></a>Uso di Query Performance Insight
Query Performance Insight è facile da usare:

* Aprire il [portale di Azure](https://portal.azure.com/) e individuare il database che si desidera esaminare. 
  * Dal menu a sinistra, sotto la sezione dedicata al supporto e alla risoluzione dei problemi, selezionare "Informazioni dettagliate sulle prestazioni delle query".
* Nella prima scheda, esaminare l'elenco delle query principali a livello di utilizzo delle risorse.
* Selezionare una singola query per visualizzarne i dettagli.
* Aprire [SQL Azure Database Advisor](sql-database-advisor.md) e verificare se sono disponibili raccomandazioni.
* Utilizzare i dispositivi di scorrimento o le icone dello zoom per modificare l'intervallo osservato.
  
    ![dashboard prestazioni](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Per consentire al database SQL di fornire informazioni dettagliate sulle prestazioni delle query, è necessario che l'archivio query acquisisca un paio di ore di dati. Se il database non ha alcuna attività o l'archivio query non è attivo in un determinato periodo di tempo, i grafici saranno vuoti quando viene visualizzato quel periodo di tempo. È possibile abilitare l'archivio query in qualsiasi momento, se non è in esecuzione.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Esaminare le query principali a livello di utilizzo di CPU
Eseguire le operazioni seguenti nel [portale](http://portal.azure.com) :

1. Passare a un database SQL e fare clic su **Tutte le impostazioni** > **Supporto e risoluzione dei problemi** > **Informazioni dettagliate prestazioni query**. 
   
    ![Informazioni dettagliate prestazioni query][1]
   
    Verrà aperta la visualizzazione relativa alle query principali e verrà mostrato l'elenco delle query principali a livello di utilizzo di CPU.
2. Per informazioni dettagliate, fare clic nei vari punti del grafico.<br>La prima riga visualizza la percentuale di uso di DTU complessiva per il database, mentre le barre visualizzano la percentuale di CPU usata dalle query selezionate durante l'intervallo selezionato (ad esempio, se si seleziona **Settimana precedente** ogni barra rappresenta un giorno).
   
    ![query principali][2]
   
    La griglia inferiore rappresenta informazioni aggregate per le query visibili.
   
   * ID query: identificatore univoco di query all'interno del database.
   * Utilizzo della CPU per query durante l'intervallo osservabile (dipende dalla funzione di aggregazione).
   * Durata per ogni query (dipende dalla funzione di aggregazione).
   * Numero totale di esecuzioni per una query specifica.
     
     Selezionare o deselezionare singole query per includerle o escluderle dal grafico utilizzando le caselle di spunta.
3. Se i dati non vengono più aggiornati, fare clic sul pulsante **Aggiorna** .
4. È possibile usare i dispositivi di scorrimento e i pulsanti dello zoom per modificare l'intervallo di osservazione ed esaminare i picchi: ![impostazioni](./media/sql-database-query-performance/zoom.png)
5. Facoltativamente, se si desidera un'altra visualizzazione, è possibile selezionare la scheda **Personalizzata** e impostare:
   
   * Metrica (CPU, durata, conteggio delle esecuzioni)
   * Intervallo di tempo (ultime 24 ore, settimana scorsa, mese scorso). 
   * Numero di query.
   * Funzione di aggregazione.
     
     ![Scheda Impostazioni](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Visualizzazione dei dettagli delle singole query
Per visualizzare i dettagli relativi alle query:

1. Fare clic su qualsiasi query nell'elenco delle query principali.
   
    ![informazioni dettagliate](./media/sql-database-query-performance/details.png)
2. Verrà aperta la visualizzazione dettagliata e i valori relativi a utilizzo CPU/durata/conteggio delle esecuzioni delle query verranno suddiviso nel tempo.
3. Per informazioni dettagliate, fare clic nei vari punti del grafico.
   
   * Il grafico in altomMostra una linea con la percentuale % DTU complessiva del database e le barre rappresentano la percentuale % della CPU utilizzata dalla query selezionata.
   * Nel secondo grafico viene mostrata la durata totale della query selezionata.
   * Nel grafico in fondo viene mostrato il numero totale delle esecuzioni effettuate dalla query selezionata.
     
     ![dettagli sulle query][3]
4. Facoltativamente, utilizzare i dispositivi di scorrimento, i pulsanti dello zoom oppure fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione dei dati della query o per mostrare un periodo di tempo diverso.

## <a name="review-top-queries-per-duration"></a>Esaminare le query principali in base alla durata
Nel recente aggiornamento di Informazioni dettagliate sulle prestazioni delle query, abbiamo introdotto due nuove metriche che aiutano a individuare potenziali colli di bottiglia: conteggio delle esecuzioni e durata.<br>

Le query con esecuzione prolungata hanno le maggiori probabilità di bloccare gli altri utenti e le risorse più a lungo, nonché di limitare la scalabilità. Sono anche i candidati ideali per l'ottimizzazione.<br>

Per identificare le query di lunga esecuzione :

1. Aprire la scheda **Personalizza** in Informazioni dettagliate sulle prestazioni delle query del database selezionato
2. Modificare le metriche su **Durata**
3. Selezionare il numero di query e l'intervallo di osservazione
4. Selezionare la funzione di aggregazione
   
   * **Somma** aggiunge il tempo di esecuzione di tutte le query durante l'intero intervallo di osservazione.
   * **Max** individua le query con il tempo di esecuzione massimo durante l'intero intervallo di osservazione.
   * **Media** rileva il tempo medio di esecuzione di tutte le query e mostra i valori medi più alti tra quelli rilevati. 
     
     ![durata query][4]

## <a name="review-top-queries-per-execution-count"></a>Esaminare le query principali in base al conteggio delle esecuzioni
Il numero elevato di esecuzioni potrebbe non influire sul database e l'utilizzo delle risorse potrebbe essere modesto, ma l'applicazione nel suo complesso potrebbe risultare rallentata.

In alcuni casi, il conteggio di esecuzioni molto elevato potrebbe causare l'aumento dei round trip di rete. I round trip hanno un forte impatto sulle prestazioni. Sono soggetti alla latenza di rete e alla latenza di server downstream. 

Ad esempio, molti siti Web basati sui dati accedono in maniera massiccia al database per tutte le richieste dell'utente. Mentre il pool di connessioni è di supporto, il traffico di rete aumentato e il carico di elaborazione sul server di database possono influire negativamente sulle prestazioni.  Il consiglio generico è di mantenere i round trip a un livello minimo assoluto.

Per identificare le query eseguite di frequente ("chatty"):

1. Aprire la scheda **Personalizza** in Informazioni dettagliate sulle prestazioni delle query del database selezionato
2. Modificare le metriche su **Conteggio delle esecuzioni**
3. Selezionare il numero di query e l'intervallo di osservazione
   
    ![conteggio delle esecuzioni query][5]

## <a name="understanding-performance-tuning-annotations"></a>Informazioni sulle annotazioni di ottimizzazione delle prestazioni
Durante l'esplorazione del carico di lavoro in Informazioni dettagliate sulle prestazioni delle query, è possibile notare la presenza di icone con linea verticale nella parte superiore del grafico.<br>

Queste icone sono annotazioni sulle prestazioni che influiscono sulle azioni eseguite da [SQL Azure Database Advisor](sql-database-advisor.md). Passando il cursore del mouse su un'annotazione, si ottengono le informazioni di base relative a tale azione:

![annotazione query][6]

Per saperne di più o per applicare il consiglio di SQL Azure Database Advisor, fare clic sull'icona, in modo da visualizzare i dettagli relativi all'azione.  Se si tratta di un consiglio attivo è possibile applicarlo direttamente tramite il comando.

![dettagli annotazione query][7]

### <a name="multiple-annotations"></a>Annotazioni multiple.
È possibile che a causa del livello di zoom le annotazioni vicine tra loro vengano compresse in una, che verrà rappresentato dall'icona speciale; facendo clic su tale icona si aprirà un nuovo pannello con l'elenco delle annotazioni raggruppate.
Correlare le query e le azioni di ottimizzazione delle prestazioni può servire ad avere una migliore comprensione del carico di lavoro. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Ottimizzare la configurazione dell'archivio query per Informazioni dettagliate prestazioni query
Durante l'uso di Informazioni dettagliate prestazioni query, possono essere visualizzati messaggi dell'archivio query simili ai seguenti:

* "Archivio query non è correttamente configurato in questo database. Per altre informazioni, fare clic qui."
* "Archivio query non è correttamente configurato in questo database. Fare clic qui per modificare le impostazioni." 

Questi messaggi in genere vengono visualizzati quando l'archivio query non è in grado di raccogliere nuovi dati. 

Il primo caso si verifica quando l'archivio query è in stato di sola lettura e i parametri sono impostati in modo ottimale. È possibile risolvere il problema aumentando le dimensioni dell'archivio query o svuotandolo del tutto.

![pulsante qds][8]

Il secondo caso si verifica quando l'archivio query è disattivato o se i parametri non sono impostati in modo ottimale. <br>È possibile modificare i criteri di conservazione e acquisizione e abilitare l'archivio query direttamente dal portale oppure eseguendo i comandi indicati sotto:

![pulsante qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Criteri di conservazione e acquisizione consigliati
Esistono due tipi di criteri di conservazione:

* Basati sulle dimensioni: se impostati su AUTOMATICO i dati verranno automaticamente cancellati al raggiungimento delle dimensioni massime.
* Basati sul tempo: per impostazione predefinita verranno impostati su 30 giorni in modo tale che, se verrà esaurito lo spazio, l'archivio query eliminerà le informazioni di query antecedenti a 30 giorni

I criteri di acquisizione possono essere impostati su:

* **Tutte**: acquisisce tutte le query.
* **Automatico**: le query poco frequenti e con durata di compilazione ed esecuzione trascurabile vengono ignorate. Le soglie per il conteggio delle esecuzioni e la durata di compilazione ed esecuzione vengono stabilite internamente. Questa è l'opzione predefinita.
* **Nessuna**: l'archivio query interrompe l'acquisizione di nuove query, ma continua a raccogliere le statistiche di runtime per le query già acquisite.

È consigliabile impostare tutti i criteri su AUTOMATICO e i criteri di pulizia dei dati su 30 giorni:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentare le dimensioni dell'archivio query. È possibile eseguire questa operazione connettendosi a un database ed eseguendo la query seguente:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

L'applicazione di queste impostazioni porterà l'archivio query a raccogliere nuove query; tuttavia, se non si desidera aspettare è possibile svuotare l'archivio query. 

> [!NOTE]
> L'esecuzione della query seguente comporta l'eliminazione di tutte le informazioni correnti nell'archivio query. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Summary
Query Performance Insight semplifica la comprensione dell'impatto del carico di lavoro della query e la relativa correlazione all'utilizzo delle risorse del database. Questa funzionalità consente di ottenere informazioni sulle query principali a livello di utilizzo di risorse e di identificare facilmente le query da correggere prima che si verifichino problemi.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori raccomandazioni sul miglioramento delle prestazioni del database SQL, fare clic su [Consigli](sql-database-advisor.md) nel pannello **Informazioni dettagliate sulle prestazioni delle query** .

![Performance Advisor](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

