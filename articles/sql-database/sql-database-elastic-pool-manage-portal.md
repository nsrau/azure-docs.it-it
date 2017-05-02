---
title: 'Portale di Azure: creare e gestire un pool elastico di database SQL | Microsoft Docs'
description: "Informazioni su come usare il portale di Azure e le funzionalità di intelligence predefinite del database SQL per gestire, monitorare e dimensionare in modo adeguato un pool elastico scalabile per ottimizzare le prestazioni del database e gestire i costi."
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: d5be10b4898002a693f617043b2e2b8a890035ca
ms.lasthandoff: 04/15/2017


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>Creare e gestire un pool elastico con il portale di Azure
Questo argomento illustra come creare e gestire [pool elastici](sql-database-elastic-pool.md) scalabili con il portale di Azure. È anche possibile creare e gestire un pool elastico di Azure con [PowerShell](sql-database-elastic-pool-manage-powershell.md), l'API REST o [C#](sql-database-elastic-pool-manage-csharp.md). È anche possibile creare e spostare database verso e dai pool elastici usando [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

## <a name="create-an-elastic-pool"></a>Creare un pool elastico 

Esistono due modi per creare un pool elastico. È possibile farlo da zero se si conosce la configurazione del pool da usare oppure iniziare con una raccomandazione fornita dal servizio. Il database SQL integra informazioni in base alle quali viene suggerita una configurazione del pool elastico nel caso risulti più conveniente secondo i dati di telemetria relativi all'uso precedente dei database.

È possibile creare più pool in un server, ma non aggiungere database da diversi server nello stesso pool. 

> [!NOTE]
> I pool elastici sono disponibili a livello generale in tutte le aree di Azure tranne India occidentale, dove sono attualmente in anteprima.  I pool elastici verranno resi disponibili a livello generale in quest'area non appena possibile.
>
>

### <a name="step-1-create-an-elastic-pool"></a>Passaggio 1: creare un pool elastico

La creazione di un pool elastico da un pannello **server** esistente nel portale è il modo più semplice per trasferire i database esistenti in un pool elastico.

> [!NOTE]
> È anche possibile creare un pool elastico cercando **pool elastico SQL** nel **Marketplace** o facendo clic su **+Aggiungi** nel pannello **Pool elastici SQL**. È possibile specificare un server nuovo o esistente nel flusso di lavoro dei provisioning del pool.
>
>

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **Altri servizi** **>** **SQL Server** e selezionare il server che contiene i database da aggiungere a un pool elastico.
2. Fare clic su **Nuovo pool**.

    ![Aggiungere un pool a un server](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **-OPPURE-**

    Potrebbe essere visualizzato un messaggio che informa che sono disponibili pool elastici consigliati per il server. Fare clic sul messaggio per visualizzare i pool consigliati in base ai dati di telemetria cronologici relativi all'utilizzo del database e quindi fare clic sul livello per visualizzare altri dettagli e personalizzare il pool. Per informazioni su come vengono create le raccomandazioni, vedere [Informazioni sulle raccomandazioni per i pool](#understand-pool-recommendations) più avanti in questo argomento.

    ![pool consigliato](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. Verrà visualizzato il pannello **Pool elastico** in cui verranno specificate le impostazioni per il pool. Se è stata scelta l'opzione **Nuovo pool** nel passaggio precedente, il piano tariffario sarà **Standard** per impostazione predefinita e non sarà selezionato alcun database. È possibile creare un pool vuoto o specificare un set di database esistenti del server da spostare nel pool. Se si sta creando un pool consigliato, il piano tariffario, le impostazioni delle prestazioni e l'elenco di database consigliati appariranno prepopolati ma potranno essere modificati.

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. Specificare un nome per il pool elastico o lasciare il valore predefinito.

### <a name="step-2-choose-a-pricing-tier"></a>Passaggio 2: Scegliere un piano tariffario

Il piano tariffario del pool determina le funzionalità disponibili per i database elastici nel pool e il numero massimo di eDTU (MAX eDTU) e la memoria (GB) disponibili per ciascun database. Per altre informazioni, vedere Livelli di servizio.

Per modificare il piano tariffario per il pool, fare clic su **Piano tariffario**, scegliere il piano e quindi fare clic su **Seleziona**.

> [!IMPORTANT]
> Dopo aver scelto il piano tariffario e aver eseguito il commit delle modifiche facendo clic su **OK** nell'ultimo passaggio, non sarà più possibile modificare il piano tariffario del pool. Per modificare il piano tariffario per un pool di database elastici esistente, creare un pool elastico nel piano tariffario desiderato ed eseguire la migrazione dei database in questo nuovo pool.
>
>

![Selezione di un livello di prezzo](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>Passaggio 3: Configurare il pool

Dopo avere impostato il piano tariffario, fare clic su Configura pool dove è possibile aggiungere i database, impostare le eDTU e lo spazio di archiviazione (in GB) del pool, nonché il numero minimo e massimo di eDTU per i database elastici nel pool.

1. Fare clic su **Configura pool**
2. Selezionare i database da aggiungere al pool. Questo passaggio è facoltativo durante la creazione del pool. È possibile aggiungere i database dopo aver creato il pool.
    Per aggiungere i database, fare clic su **Aggiungi database**, selezionare i database da aggiungere e quindi fare clic sul pulsante **Seleziona**.

    ![Aggiungi database](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Se i dati di telemetria cronologici relativi all'utilizzo disponibili per i database correnti sono sufficienti, il grafico **Utilizzo di eDTU e GB** e il grafico a barre **Utilizzo di eDTU effettivo** vengono aggiornati per semplificare le decisioni relative alla configurazione. Il servizio potrebbe anche visualizzare un messaggio di raccomandazione per facilitare la scelta delle dimensioni corrette per il pool. Vedere la sezione [Indicazioni dinamiche](#dynamic-recommendations).

3. Usare i controlli nella pagina **Configura pool** per esaminare le impostazioni e configurare il pool. Vedere la sezione relativa ai [limiti dei pool elastici](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) per altri dettagli sui limiti per ogni livello di servizio e le [considerazioni su prezzi e prestazioni per i pool elastici](sql-database-elastic-pool-guidance.md) per istruzioni dettagliate sul corretto ridimensionamento di un pool elastico. Per altre informazioni sulle impostazioni del pool, vedere [Proprietà del pool elastico](sql-database-elastic-pool.md#elastic-pool-properties).

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Fare clic su **Seleziona** in the **Configure Pool** .
5. Fare clic su **OK** per creare il pool.


## <a name="understand-elastic-pool-recommendations"></a>Informazioni sulle indicazioni per i pool elastici

Il servizio di database SQL valuta la cronologia di utilizzo e suggerisce uno o più pool quando questo approccio è più conveniente rispetto all'uso di singoli database. Ogni raccomandazione viene configurata con un subset univoco di database del server che meglio si adatta al pool.

![pool consigliato](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

La raccomandazione per il pool include:

- Piano tariffario per il pool (Basic, Standard, Premium o Premium RS)
- **eDTU POOL** appropriato, detto anche eDTU max per pool.
- **MAX eDTU** e **Min eDTU** per ogni database.
- Elenco di database consigliati per il pool.

> ![IMPORTANTE] Il servizio prende in considerazione la telemetria degli ultimi 30 giorni per consigliare i pool. Per far sì che un database possa essere considerato un candidato per un pool elastico, deve esistere da almeno 7 giorni. I database che si trovano già in pool elastici non vengono considerati come possibili candidati, in linea con i consigli relativi ai pool elastici.
>

Il servizio valuta le risorse necessarie e la convenienza dello spostamento di singoli database in ogni livello di servizio nei pool dello stesso livello. Ad esempio, vengono valutati tutti i database Standard in un server per l’utilizzo in un pool elastico Standard. Ciò significa che il servizio non effettua consigli relativi a livelli diversi, ad esempio lo spostamento di un database Standard in un pool Premium.

Dopo aver aggiunto i database al pool, le indicazioni verranno generate dinamicamente in base all'uso storico dei database selezionati. Queste indicazioni vengono visualizzate nel grafico relativo all'uso di eDTU e GB, oltre che come banner nella parte superiore del pannello **Configura pool**. Queste indicazioni sono concepite per facilitare la creazione di un pool elastico ottimizzato per database specifici.

![Indicazioni dinamiche](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>Gestire e monitorare un pool elastico

È possibile usare il portale di Azure per monitorare e gestire un pool elastico e i database nel pool. Dal portale è possibile monitorare l'utilizzo di un pool elastico e dei database al suo interno. È anche possibile apportare un set di modifiche al pool elastico e inviare tutte le modifiche contemporaneamente. Le modifiche includono l'aggiunta o la rimozione di database, la modifica delle impostazioni del pool elastico o la modifica delle impostazioni del database.

L'immagine seguente illustra un esempio di pool elastico. La visualizzazione include:

*  Grafici per il monitoraggio dell'utilizzo delle risorse da parte del pool elastico e dei database al suo interno.
*  Il pulsante **Configura pool** per apportare modifiche al pool elastico.
*  Il pulsante **Crea database** per creare un database e aggiungerlo al pool elastico corrente.
*  Processi elastici che consentono di gestire un numero elevato di database tramite l'esecuzione di script Transact SQL in tutti i database in un elenco.

![Visualizzazione del pool][2]

È possibile passare a un pool specifico per visualizzarne l'utilizzo delle risorse. Per impostazione predefinita, il pool è configurato per mostrare l'utilizzo di eDTU e risorse di archiviazione relativo all'ultima ora. È possibile configurare il grafico per mostrare diverse metriche in diversi intervalli di tempo.

1. Selezionare un pool elastico da usare.
2. In **Monitoraggio pool elastico** è presente un grafico con l'etichetta **Utilizzo risorse**. Fare clic sul grafico.

    ![Monitoraggio di pool elastici][3]

    Verrà visualizzato il pannello **Metrica** con una visualizzazione dettagliata delle metriche specificate nell'intervallo di tempo indicato.   

    ![Blade delle metriche][9]

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico e il pannello Metrica per visualizzare altre metriche, ad esempio la percentuale di CPU, la percentuale di IO dei dati e la percentuale di IO del log usata.

1. Nel pannello Metrica fare clic su **Modifica**.

    ![Fare clic su Modifica][6]

2. Nel pannello **Modifica grafico** selezionare un intervallo di tempo, ad esempio ora precedente, oggi o settimana precedente, oppure fare clic su **personalizzato** per impostare un qualsiasi intervallo di tempo nelle due settimane precedenti. Selezionare il tipo di grafico (a barre o a linee), quindi selezionare le risorse da monitorare.

   > [!Note]
   > Solo le metriche con la stessa unità di misura possono essere visualizzate nel grafico nello stesso momento. Se ad esempio si seleziona "eDTU percentage" (Percentuale eDTU), sarà possibile selezionare solo altre metriche con percentuale come unità di misura.
   >

    ![Fare clic su Modifica](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

    

3. Fare quindi clic su **OK**.

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gestire e monitorare database in un pool elastico

È possibile monitorare anche i singoli database per potenziali problemi.

1. In **Monitoraggio database elastico**è disponibile un grafico che mostra le metriche relative a cinque database. Per impostazione predefinita, il grafico mostra i primi cinque database nel pool per utilizzo di eDTU medio nell'ora precedente. Fare clic sul grafico.

    ![Monitoraggio di pool elastici][4]

2. Verrà visualizzato il pannello **Utilizzo risorse database**, che fornisce una visualizzazione dettagliata dell'utilizzo del database nel pool. La griglia nella parte inferiore del pannello consente di selezionare fino a cinque database nel pool per visualizzarne l'uso nel grafico. È anche possibile personalizzare le metriche e l'intervallo di tempo visualizzati nel grafico facendo clic su **Modifica grafico**.

    ![Pannello Utilizzo risorse database][8]

### <a name="to-customize-the-view"></a>Per personalizzare la visualizzazione

1. Nel pannello **Utilizzo risorse database** fare clic su **Modifica grafico**.

    ![Fare clic su Modifica grafico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Nel pannello **Modifica grafico** selezionare un intervallo di tempo, ad esempio ora precedente o ultime 24 ore, oppure fare clic su **personalizzato** per selezionare un giorno diverso nelle 2 settimane precedenti.

    ![Fare clic su personalizzato](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Fare clic sull'elenco a discesa **Confronta database per** e selezionare una metrica diversa da usare per il confronto dei database.

    ![Modificare il grafico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Per selezionare i database da monitorare

Nell'elenco dei database del pannello **Utilizzo risorse database** è possibile trovare database specifici scorrendo le pagine dell'elenco o digitando il nome di un database. Usare la casella di controllo per selezionare il database.

![Cercare i database da monitorare][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Aggiungere un avviso a una risorsa di pool elastico

È possibile aggiungere regole a un pool elastico per l'invio di messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando il pool elastico raggiunge la soglia d'uso impostata.

> [!IMPORTANT]
> Il monitoraggio dell'uso delle risorse per i pool elastici viene eseguito con un ritardo di almeno 20 minuti. Al momento non è possibile impostare avvisi inferiori ai 30 minuti per i pool elastici. Tutti gli avvisi per i pool elastici impostati con una durata (parametro denominato "-WindowSize" nell'API di PowerShell) inferiore a 30 minuti non possono essere attivati. Verificare che gli avvisi definiti per i pool elastici usino un periodo (WindowSize) di almeno 30 minuti.
>
>

**Per aggiungere un avviso a una risorsa qualsiasi:**

1. Fare clic sul grafico **Utilizzo risorse** per aprire il pannello **Metrica**. Fare clic su **Aggiungi avviso** e inserire le informazioni nel pannello **Aggiungi una regola di avviso**. La **risorsa** viene impostata automaticamente come il pool corrente.
2. Inserire un **Nome** e una **Descrizione** che serviranno a identificare l'avviso per l'utente e i destinatari.
3. Scegliere una **Metrica** in base alla quale creare un avviso dall'elenco.

    Il grafico mostra in modo dinamico l'utilizzo delle risorse per la metrica selezionata in modo da scegliere una soglia.

4. Scegliere una **Condizione**, ad esempio maggiore di, minore di e così via, e una **Soglia**.
5. Fare clic su **OK**.

## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico

È possibile aggiungere o rimuovere i database da un pool esistente. I database possono trovarsi in altri pool. Tuttavia, è possibile aggiungere solo i database che sono nello stesso server logico.

1. Nel pannello del pool, in **Database elastici** fare clic su **Configura pool**.

    ![Fare clic su Configura pool][1]

2. Nel pannello **Configura pool** fare clic su **Aggiungi al pool**.

    ![Fare clic su Aggiungi al pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Nel pannello **Aggiungi database** selezionare uno o più database da aggiungere al pool. Quindi fare clic su **Seleziona**.

    ![Selezionare i database da aggiungere](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Il pannello **Configura pool** mostra il database selezionato per l'aggiunta, con lo stato impostato su **In sospeso**.

    ![Aggiunte di pool in sospeso](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Nel pannello **Configura pool** fare clic su **Salva**.

    ![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool elastico

1. Nel pannello **Configura pool** selezionare uno o più database da rimuovere.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Fare clic su **Rimuovi dal pool**.

    ![elenchi di database](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Il pannello **Configura pool** mostra il database selezionato per la rimozione, con lo stato impostato su **In sospeso**.

    ![anteprima aggiunta e rimozione database](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Nel pannello **Configura pool** fare clic su **Salva**.

    ![Fare clic su Salva.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-an-elastic-pool"></a>Modificare le impostazioni delle prestazioni di un pool elastico

Durante il monitoraggio dell'utilizzo delle risorse di un pool elastico possono rendersi necessarie alcune modifiche, ad esempio dei limiti di archiviazione o di prestazioni. Si potrebbe voler modificare le impostazioni del database nel pool. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior compromesso tra prestazioni e costi. Per altre informazioni, vedere [Quando usare un pool elastico](sql-database-elastic-pool-guidance.md).

Per modificare i limiti di archiviazione o eDTU per il pool e il numero di eDTU per il database:

1. Aprire il pannello **Configura pool** .

    In **Impostazioni pool elastico** usare i dispositivi di scorrimento per modificare le impostazioni del pool.

    ![Utilizzo delle risorse del pool elastico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Alla modifica dell'impostazione, viene visualizzato il relativo costo mensile stimato.

    ![Aggiornamento di un pool elastico e nuovi costi mensili](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>Latenza delle operazioni dei pool elastici
* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro 5 minuti.
* La modifica del numero di eDTU per pool dipende dallo spazio totale usato da tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui pool elastici, vedere [Pool elastico del database SQL](sql-database-elastic-pool.md).
- Per indicazioni sull'uso dei pool elastici, vedere le [considerazioni su prezzo e prestazioni per i pool elastici](sql-database-elastic-pool-guidance.md).
- Per usare i processi elastici per eseguire script Transact-SQL su qualsiasi numero di database nel pool, vedere la [panoramica dei processi elastici](sql-database-elastic-jobs-overview.md).
- Per eseguire query su un numero qualsiasi di database nel pool, vedere la [panoramica delle query elastiche](sql-database-elastic-query-overview.md).
- Per eseguire transazioni su un numero qualsiasi di database nel pool, vedere [Transazioni elastiche](sql-database-elastic-transactions-overview.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

