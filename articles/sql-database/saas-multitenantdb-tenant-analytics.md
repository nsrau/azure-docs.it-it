---
title: Eseguire query di analisi su database SQL di Azure | Microsoft Docs
description: "Query di analisi su più tenant con dati estratti da diversi database SQL di Azure."
keywords: esercitazione su sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh; billgib; genemi
ms.openlocfilehash: c6c83f4ac93dd105b2f311250061b8880f5d2a5b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Analisi su più tenant con dati estratti

Questa esercitazione illustra in dettaglio uno scenario di analisi completo. Lo scenario dimostra come l'analisi possa consentire alle aziende di prendere decisioni oculate. Con i dati estratti dal database condiviso, si usa l'analisi per ottenere informazioni dettagliate sul comportamento dei tenant, ad esempio sul modo in cui usano l'applicazione di esempio SaaS Wingtip Tickets. Questo scenario include tre passaggi: 

1.  **Estrarre i dati** dal database di ogni tenant a un archivio di analisi.
2.  **Ottimizzare i dati estratti** per l'elaborazione dell'analisi.
3.  Usare strumenti di **business intelligence** per ottenere informazioni dettagliate utili su cui basare il processo decisionale. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare l'archivio di analisi dei tenant in cui estrarre i dati.
> - Usare processi elastici per estrarre i dati dal database di ogni tenant all'archivio di analisi.
> - Ottimizzare i dati estratti, riorganizzandoli in uno schema star.
> - Eseguire query sul database di analisi.
> - Usare Power BI per la visualizzazione dei dati per evidenziare le tendenze nei dati dei tenant e definire raccomandazioni per apportare miglioramenti.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Modello di analisi dei tenant offline

Le applicazioni SaaS sviluppate hanno accesso a un'elevata quantità di dati dei tenant archiviati nel cloud. I dati costituiscono un'esauriente fonte di informazioni dettagliate sul funzionamento e l'utilizzo dell'applicazione e sul comportamento dei tenant. Tali informazioni dettagliate possono essere utili come base per lo sviluppo di funzionalità, miglioramenti dell'usabilità e altri investimenti per l'app e la piattaforma.

L'accesso ai dati per tutti i tenant è semplice quando tutti i dati si trovano in un unico database multi-tenant. È invece più complesso quando sono distribuiti su larga scala in migliaia di database. Un modo per superare tale complessità consiste nell'estrarre i dati in un database o un data warehouse di analisi. Si eseguono quindi query sul data warehouse per raccogliere informazioni dettagliate dai dati relativi ai biglietti di tutti i tenant.

Questa esercitazione presenta uno scenario di analisi completo per questa applicazione SaaS di esempio. Per prima cosa, si usano processi elastici per pianificare l'estrazione dei dati dal database di ogni tenant. I dati vengono inviati a un archivio di analisi, che può essere un database SQL o un'istanza di SQL Data Warehouse. Per l'estrazione di dati su larga scala, è consigliabile usare [Azure Data Factory](../data-factory/introduction.md).

I dati aggregati vengono quindi suddivisi in un set di tabelle con [schema star](https://www.wikipedia.org/wiki/Star_schema). Le tabelle sono costituite da una tabella dei fatti centrale e dalle tabelle delle dimensioni correlate:

- La tabella dei fatti centrale dello schema star contiene i dati relativi ai biglietti.
- Le tabelle delle dimensioni contengono i dati relativi alle sedi, agli eventi, ai clienti e alle date di acquisto.

Insieme, la tabella centrale e quelle delle dimensioni supportano un'elaborazione analitica efficiente. L'immagine seguente illustra lo schema star usato in questa esercitazione:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Vengono infine eseguite query sulle tabelle dello schema star. I risultati delle query vengono visualizzati in modo da evidenziare informazioni dettagliate sul comportamento dei tenant e sul rispettivo uso dell'applicazione. Con questo schema star, è possibile eseguire query che consentono di individuare elementi come i seguenti:

- Acquirenti dei biglietti e relativa sede.
- Modelli e tendenze nascosti nelle aree seguenti:
    - Vendite di biglietti.
    - Popolarità relativa di ogni sede.

Comprendendo quanto viene usato il servizio da ogni tenant è possibile creare piani di servizio per soddisfarne le esigenze. Questa esercitazione offre esempi di base delle informazioni dettagliate che è possibile ricavare dai dati dei tenant.

## <a name="setup"></a>Configurazione

### <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Viene distribuita l'applicazione del database multi-tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
- Gli script e il [codice sorgente](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) dell'applicazione SaaS Wingtip vengono scaricati da GitHub. Assicurarsi di *sbloccare il file ZIP* prima di estrarne il contenuto. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.
- Power BI Desktop è installato. Scaricare [Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- È stato effettuato il provisioning del batch di tenant aggiuntivi. Vedere l'[**esercitazione sul provisioning di tenant**](saas-multitenantdb-provision-and-catalog.md).
- Sono stati creati un agente processo e un database agenti processo. Vedere la procedura appropriata nell'[**esercitazione sulla gestione dello schema**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Creare dati per la dimostrazione

In questa esercitazione viene eseguita un'analisi sui dati relativi alle vendite di biglietti. In questo passaggio si generano i dati relativi ai biglietti per tutti i tenant.  Successivamente, questi dati verranno estratti per l'analisi. *Assicurarsi di aver effettuato il provisioning del batch di tenant come descritto in precedenza, per avere una quantità significativa di dati*. Una quantità sufficientemente elevata di dati può esporre vari modelli diversi di acquisto dei biglietti.

1. In **PowerShell ISE** aprire *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* e impostare il valore seguente:
    - **$DemoScenario** = **1** (acquisto di biglietti per gli eventi in tutte le sedi)
2. Premere **F5** per eseguire lo script e creare la cronologia di acquisto dei biglietti per ogni evento in ogni sede.  Lo script viene eseguito per diversi minuti per generare decine di migliaia di biglietti.

### <a name="deploy-the-analytics-store"></a>Distribuire l'archivio di analisi
Spesso tutti i dati dei tenant sono contenuti in numerosi database partizionati transazionali. È necessario aggregare i dati dei tenant dei database partizionati in un archivio di analisi. L'aggregazione consente di eseguire query efficienti sui dati. In questa esercitazione, per archiviare i dati aggregati viene usato un database SQL di Azure.

Nei passaggi seguenti si distribuiscono l'archivio di analisi, denominato **tenantanalytics**, e le tabelle predefinite che verranno popolate più avanti nell'esercitazione:
1. In PowerShell ISE aprire *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*. 
2. Impostare la variabile $DemoScenario nello script in base all'archivio di analisi scelto. Ai fini dell'apprendimento, è consigliabile un database SQL senza columnstore.
    - Per usare un database SQL senza columnstore, impostare **$DemoScenario** = **2**
    - Per usare un database SQL con columnstore, impostare **$DemoScenario** = **3**  
3. Premere **F5** per eseguire lo script dimostrativo che chiama lo script *Deploy-TenantAnalytics<XX>.ps1* e crea l'archivio di analisi dei tenant. 

Dopo aver distribuito l'applicazione e avervi inserito i dati dei tenant a cui si è interessati, usare [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) per connettersi ai server **tenants1-mt-\<Utente\>** e **catalog-mt-\<Utente\>** con l'account di accesso *developer* e la password *P@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

In Esplora oggetti seguire questa procedura:

1. Espandere il server *tenants1-mt-\<Utente\>*.
2. Espandere il nodo database per vedere che il database *tenants1* contiene più tenant.
3. Espandere il server *catalog-mt-\<Utente\>*.
4. Verificare che vengano visualizzati l'archivio di analisi e il database jobaccount.

Visualizzare gli elementi di database seguenti in Esplora oggetti di SSMS espandendo il nodo dell'archivio di analisi:

- Le tabelle **TicketsRawData** ed **EventsRawData**, contenenti dati non elaborati estratti dai database dei tenant.
- Le tabelle dello schema star, ossia **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates**.
- La stored procedure **sp_ShredRawExtractedData** è usata per popolare le tabelle dello schema star dalle tabelle dei dati non elaborati.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Estrazione dei dati 

### <a name="create-target-groups"></a>Creare i gruppi di destinazione 

Prima di procedere, assicurarsi di aver distribuito l'account per i processi e il database jobaccount. Nel successivo set di passaggi verranno usati processi elastici per estrarre dati dal database dei tenant partizionati e archiviarli nell'archivio di analisi. Il secondo processo suddividerà quindi i dati e li archivierà nelle tabelle dello schema star. Questi due processi vengono eseguiti su due diversi gruppi di destinazione: **TenantGroup** e **AnalyticsGroup**. Il processo di estrazione viene eseguito su TenantGroup, che contiene tutti i database dei tenant. Il processo di suddivisione viene eseguito su AnalyticsGroup, che contiene solo l'archivio di analisi. Creare i gruppi di destinazione seguendo questa procedura:

1. In SSMS connettersi al database **jobaccount** in catalog-mt-\<Utente\>.
2. In SSMS aprire *…\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql*. 
3. Modificare la variabile @User all'inizio dello script, sostituendo <User> con il valore dell'utente usato per la distribuzione dell'applicazione di database multi-tenant SaaS Wingtip Tickets.
4. Premere **F5** per eseguire lo script che crea i due gruppi di destinazione.

### <a name="extract-raw-data-from-all-tenants"></a>Estrarre dati non elaborati da tutti i tenant

Le transazioni potrebbero verificarsi con maggiore frequenza per i dati relativi a *biglietti e clienti* rispetto ai dati relativi a *eventi e sedi*. Di conseguenza, valutare la possibilità di estrarre i dati relativi a biglietti e clienti separatamente e con maggiore frequenza rispetto ai dati relativi a eventi e sedi. In questa sezione si definiscono e si pianificano due processi separati:

- Estrazione dei dati relativi a biglietti e clienti.
- Estrazione dei dati relativi a eventi e sedi.

Ogni processo estrae i rispettivi dati e li inserisce nell'archivio di analisi. A questo punto, un processo separato suddivide i dati estratti nello schema star dell'analisi.

1. In SSMS connettersi al database **jobaccount** nel server catalog-mt-\<Utente\>.
2. In SSMS aprire *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Modificare @User all'inizio dello script e sostituire <User> con il nome utente usato per la distribuzione dell'applicazione di database multi-tenant SaaS Wingtip Tickets. 
4. Premere **F5** per eseguire lo script che crea ed esegue il processo che estrae i dati relativi ai biglietti e ai clienti dal database di ogni tenant. Il processo salva i dati nell'archivio di analisi.
5. Eseguire una query sulla tabella TicketsRawData del database tenantanalytics per verificare che la tabella sia stata popolata con le informazioni sui biglietti di tutti i tenant.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Ripetere i passaggi precedenti, sostituendo però **\ExtractTickets.sql** con **\ExtractVenuesEvents.sql** nel passaggio 2.

L'esecuzione del processo popola la tabella EventsRawData dell'archivio di analisi con le nuove informazioni su eventi e sedi di tutti i tenant. 

## <a name="data-reorganization"></a>Riorganizzazione dei dati

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Suddividere i dati estratti per popolare le tabelle dello schema star

Il passaggio successivo consiste nella suddivisione dei dati non elaborati estratti in un set di tabelle ottimizzate per query di analisi. Viene usato uno schema star. Una tabella dei fatti centrale contiene i singoli record delle vendite di biglietti. Le tabelle delle dimensioni sono popolate con i dati relativi alle sedi, agli eventi, ai clienti e alle date di acquisto. 

In questa sezione dell'esercitazione si definisce e si esegue un processo che unisce i dati non elaborati estratti ai dati delle tabelle dello schema star. Al termine del processo di unione, i dati non elaborati verranno eliminati in modo da lasciare le tabelle pronte per essere popolate dal successivo processo di estrazione dei dati dei tenant.

1. In SSMS connettersi al database **jobaccount** in catalog-mt-\<Utente\>.
2. In SSMS aprire *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Premere **F5** per eseguire lo script e definire così un processo che chiama la stored procedure sp_ShredRawExtractedData nell'archivio di analisi.
4. Attendere il tempo sufficiente per il completamento del processo.
    - Controllare lo stato del processo nella colonna **Lifecycle** della tabella jobs.jobs_execution. Prima di procedere, verificare che lo stato del processo sia **Succeeded**. Al completamento dell'esecuzione verranno visualizzati dati simili all'immagine seguente:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Esplorazione dei dati

### <a name="visualize-tenant-data"></a>Visualizzare i dati dei tenant

I dati nella tabella dello schema star includono tutti i dati relativi alle vendite di biglietti necessari per l'analisi. Per facilitare l'individuazione di tendenze in set di dati di grandi dimensioni, è necessario visualizzarli graficamente.  Questa sezione illustra come usare **Power BI** per manipolare e visualizzare i dati dei tenant che sono stati estratti e organizzati.

Seguire questa procedura per connettersi a Power BI e importare le viste create in precedenza:

1. Avviare Power BI Desktop.
2. Nella barra multifunzione Home selezionare **Recupera dati** e scegliere **Altro** dal menu.
3. Nella finestra **Recupera dati** selezionare il database SQL di Azure.
4. Nella finestra di accesso al database immettere il nome del server, ossia catalog-mt-\<Utente\>.database.windows.net. Selezionare **Importa** come **Modalità Connettività dati** e quindi fare clic su OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Selezionare **Database** nel riquadro sinistro e quindi immettere il nome utente *developer* e la password *P@ssword1*. Fare clic su **Connetti**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. Nel riquadro **Strumento di navigazione** selezionare le tabelle dello schema star fact_Tickets, dim_Events, dim_Venues, dim_Customers e dim_Dates nel database di analisi. Selezionare quindi **Carica**. 

Congratulazioni! Il caricamento dei dati in Power BI è stato completato. È ora possibile iniziare a esplorare visualizzazioni interessanti per ottenere informazioni dettagliate sui tenant. Successivamente verrà illustrato in dettaglio come l'analisi consenta al team aziendale di Wingtip Tickets di offrire raccomandazioni basate sui dati che saranno utili per ottimizzare il modello aziendale e l'esperienza dei clienti.

Per iniziare, si analizzano i dati relativi alle vendite di biglietti per individuare la variazione nell'utilizzo tra le diverse sedi. Selezionare le opzioni seguenti in Power BI per tracciare un grafico a barre del numero totale di biglietti venduti da ogni sede. A causa della variazione casuale nel generatore di biglietti, i risultati effettivi potrebbero essere diversi.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Il tracciato precedente conferma la variazione nel numero di biglietti venduti da ogni sede. Le sedi che vendono una maggiore quantità di biglietti usano di più il servizio rispetto a quelle con vendite inferiori. Questo potrebbe offrire l'opportunità di adattare l'allocazione delle risorse alle esigenze dei diversi tenant.

È possibile analizzare ulteriormente i dati per individuare la variazione delle vendite di biglietti nel tempo. Selezionare le opzioni seguenti in Power BI per tracciare il numero totale di biglietti venduti ogni giorno per un periodo di 60 giorni.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Il grafico precedente mostra un picco nelle vendite di biglietti per alcune sedi. Questi picchi rafforzano l'idea che alcune sedi potrebbe utilizzare le risorse di sistema in modo sproporzionato. Per ora non è stato rilevato un modello ovvio nella ricorrenza dei picchi.

Si vuole quindi analizzare ulteriormente il significato dei giorni di picchi di vendite. Quando si verificano i picchi dopo che i biglietti vengono messi in vendita? Per tracciare i biglietti venduti in ogni singolo giorno, selezionare le opzioni seguenti in Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Il tracciato precedente mostra che alcune sedi vendono molti biglietti nel primo giorno di vendita. In queste sedi, non appena i biglietti vengono messi in vendita sembra verificarsi una corsa all'acquisto. Questo picco di attività di alcune sedi potrebbe influire sul servizio per gli altri tenant.

È possibile esaminare di nuovo i dati per verificare se questa corsa all'acquisto riguardi tutti gli eventi ospitati in queste sedi. Nei tracciati precedenti è stato rilevato che Contoso Concert Hall vende molti biglietti e che Contoso presenta anche un picco delle vendite di biglietti in determinati giorni. Modificare la selezione delle opzioni di Power BI per tracciare le vendite cumulative di biglietti di Contoso Concert Hall concentrandosi sulle tendenze di vendita per ogni evento, in modo da verificare se tutti gli eventi seguano lo stesso modello di vendita.

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Il tracciato precedente per Contoso Concert Hall mostra che la corsa all'acquisto non si verifica per tutti gli eventi. Modificare la selezione delle opzioni di filtro per visualizzare le tendenze di vendita per le altre sedi.

Le informazioni dettagliate sui modelli di vendita dei biglietti potrebbero consentire a Wingtip Tickets di ottimizzare il modello aziendale. Invece di applicare lo stesso addebito a tutti i tenant, Wingtip introdurrà livelli di servizio con diversi livelli di prestazioni. Alle sedi più grandi che devono vendere un maggior numero di biglietti al giorno potrà essere offerto un livello più elevato con un contratto di servizio superiore. I database di tali sedi potranno essere inseriti in pool con limiti di risorse per database superiori. Ogni livello di servizio potrà avere un'allocazione di vendite per ogni ora, con addebito di tariffe aggiuntive in caso di superamento dell'allocazione. Le sedi più grandi con picchi periodici di vendite trarranno vantaggio dai livelli superiori e Wingtip Tickets potrà monetizzare il servizio in modo più efficiente.

Nel frattempo, alcuni clienti di Wingtip Tickets segnalano di avere difficoltà a vendere un numero di biglietti sufficiente a giustificare il costo del servizio. Queste informazioni dettagliate potrebbero offrire l'opportunità di incrementare le vendite di biglietti per le sedi con prestazioni inferiori. Vendite superiori aumenteranno il valore percepito del servizio. Fare clic con il pulsante destro del mouse su fact_Tickets e scegliere **Nuova misura**. Immettere l'espressione seguente per la nuova misura denominata **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selezionare le opzioni di visualizzazione seguenti per tracciare la percentuale di biglietti venduta da ogni sede e determinare così il successo relativo di ognuna.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Il tracciato precedente mostra che nonostante la maggior parte delle sedi venda oltre l'80% dei propri biglietti, alcune hanno difficoltà a riempire più della metà dei posti. Modificare la selezione nell'area dei valori per ottenere la percentuale minima o massima di biglietti venduta per ogni sede.

Il precedente approfondimento dell'analisi ha rivelato che le vendite di biglietti tendono a seguire modelli prevedibili. Grazie a questa scoperta, Wingtip Tickets potrebbe consentire alle sedi con prestazioni inferiori di incrementare le vendite di biglietti consigliando prezzi dinamici. Questa scoperta potrebbe offrire l'opportunità di impiegare tecniche di apprendimento automatico per prevedere le vendite di biglietti per ogni evento. Sarà anche possibile definire previsioni in merito all'impatto sui ricavi dell'offerta di sconti sulle vendite di biglietti. Power BI Embedded potrebbe essere integrato in un'applicazione di gestione degli eventi, per poter visualizzare le vendite previste e l'effetto di diversi sconti. L'applicazione potrebbe consentire la definizione dello sconto ottimale da applicare direttamente dalla visualizzazione dell'analisi.

Sono state osservate le tendenze nei dati dei tenant dell'applicazione di database multi-tenant SaaS Wingtip Tickets. Si possono prendere in considerazione altri modi per usare l'app come base per le decisioni aziendali dei fornitori di applicazioni SaaS. I fornitori possono soddisfare in modo più efficiente le esigenze dei propri tenant. Questa esercitazione dovrebbe aver offerto gli strumenti necessari per eseguire analisi sui dati dei tenant in modo da consentire alle aziende di prendere decisioni basate sui dati.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Distribuire un database di analisi dei tenant con tabelle predefinite di uno schema star
> - Usare processi elastici per estrarre dati da tutti i database dei tenant
> - Unire i dati estratti in tabelle in uno schema star progettato per l'analisi
> - Eseguire query su un database di analisi 
> - Usare Power BI per la visualizzazione dei dati per osservare le tendenze nei dati dei tenant 

Congratulazioni!

## <a name="additional-resources"></a>Risorse aggiuntive

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). -->
- [Processi elastici](sql-database-elastic-jobs-overview.md).
