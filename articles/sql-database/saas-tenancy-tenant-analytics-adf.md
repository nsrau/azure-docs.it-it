---
title: Eseguire query di analisi sui database tenant con Azure SQL Data Warehouse | Microsoft Docs
description: Query di analisi su più tenant con dati estratti dal database SQL di Azure, SQL Data Warehouse, Azure Data Factory o Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485084"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Esplorare l'analisi basata su SaaS con il database SQL di Azure, SQL Data Warehouse, Data Factory e Power BI

Questa esercitazione illustra in dettaglio uno scenario di analisi end-to-end. Lo scenario dimostra come l'analisi sui dati dei tenant possa consentire ai fornitori di software di prendere decisioni oculate. Con i dati estratti dal database di ogni tenant, si usa l'analisi per ottenere informazioni dettagliate sul comportamento dei tenant, ad esempio sul rispettivo uso dell'applicazione SaaS Wingtip Tickets di esempio. Questo scenario include tre passaggi: 

1.  **Estrarre i dati** da ogni database tenant a un archivio di analisi, in questo caso un'istanza di SQL Data Warehouse.
2.  **Ottimizzare i dati estratti** per l'elaborazione dell'analisi.
3.  Usare strumenti di **business intelligence** per ottenere informazioni dettagliate utili su cui basare il processo decisionale. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Creare l'archivio di analisi dei tenant per il caricamento.
> - Usare Azure Data Factory per estrarre i dati da ogni database tenant al data warehouse di analisi.
> - Ottimizzare i dati estratti, riorganizzandoli in uno schema star.
> - Eseguire query sul data warehouse di analisi.
> - Usare Power BI per la visualizzazione dei dati per evidenziare le tendenze nei dati dei tenant e definire raccomandazioni per apportare miglioramenti.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analisi sui dati dei tenant estratti

Le applicazioni SaaS contengono una quantità potenzialmente elevata di dati dei tenant nel cloud. Questi dati possono costituire un'esauriente fonte di informazioni dettagliate sul funzionamento e l'utilizzo dell'applicazione e sul comportamento dei tenant. Tali informazioni dettagliate possono essere utili come base per lo sviluppo di funzionalità, miglioramenti dell'usabilità e altri investimenti per le app e la piattaforma.

L'accesso ai dati per tutti i tenant è semplice quando tutti i dati si trovano in un unico database multi-tenant. È invece più complesso quando sono distribuiti su larga scala in migliaia di database. Un modo per superare tale complessità consiste nell'estrarre i dati in un database o un data warehouse di analisi per l'esecuzione di query.

Questa esercitazione presenta uno scenario di analisi end-to-end per l'applicazione Wingtip Tickets. Per prima cosa viene usato [Azure Data Factory](../data-factory/introduction.md) come strumento di orchestrazione per estrarre i dati sulle vendite di biglietti e i dati correlati da ogni database tenant. Questi dati vengono caricati in tabelle di staging in un archivio di analisi, che può essere un database SQL o un'istanza di SQL Data Warehouse. In questa esercitazione, come archivio di analisi viene usato [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is).

Successivamente, i dati estratti vengono trasformati in un set di tabelle con [schema star](https://www.wikipedia.org/wiki/Star_schema). Le tabelle sono costituite da una tabella dei fatti centrale e dalle tabelle delle dimensioni correlate:

- La tabella dei fatti centrale dello schema star contiene i dati relativi ai biglietti.
- Le tabelle delle dimensioni contengono i dati relativi alle sedi, agli eventi, ai clienti e alle date di acquisto.

Insieme, la tabella centrale e quelle delle dimensioni supportano un'elaborazione analitica efficiente. L'immagine seguente illustra lo schema star usato in questa esercitazione:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Vengono infine eseguite query sulle tabelle dello schema star. I risultati delle query vengono visualizzati con Power BI in modo da evidenziare informazioni dettagliate sul comportamento dei tenant e sul rispettivo uso dell'applicazione. Con questo schema star, le query eseguite espongono quanto segue:

- Acquirenti dei biglietti e relativa sede.
- Modelli e tendenze nella vendite di biglietti.
- Popolarità relativa di ogni sede.

Questa esercitazione offre esempi di base delle informazioni dettagliate che è possibile ricavare dai dati di Wingtip Tickets. Comprendendo come il servizio viene usato da ogni sede, il fornitore di Wingtip Tickets può considerare diversi piani di servizio mirati, rivolti ad esempio alle sedi più o meno attive. 

## <a name="setup"></a>Configurazione

### <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> Questa esercitazione usa funzionalità di Azure Data Factory, per la parametrizzazione dei servizi collegati, che sono attualmente disponibili in anteprima limitata. Per eseguire questa esercitazione, specificare l'ID della sottoscrizione [qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Si riceverà una conferma non appena la sottoscrizione verrà abilitata.

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:
- È stata distribuita l'applicazione SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
- Gli script e il [codice sorgente](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) dell'applicazione SaaS di database per tenant Wingtip Tickets sono stati scaricati da GitHub. Vedere le istruzioni di download. Assicurarsi di *sbloccare il file ZIP* prima di estrarne il contenuto.
- Power BI Desktop è installato. [Scaricare Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- È stato effettuato il provisioning del batch di tenant aggiuntivi. Vedere l'[**esercitazione sul provisioning di tenant**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Creare dati per la dimostrazione

Questa esercitazione esplora l'analisi sui dati relativi alle vendite di biglietti. In questo passaggio si generano i dati relativi ai biglietti per tutti i tenant. In un passaggio successivo, questi dati verranno estratti per l'analisi. _Assicurarsi di aver effettuato il provisioning del batch di tenant_ come descritto in precedenza, in modo da avere dati sufficienti per esporre vari modelli diversi di acquisto dei biglietti.

1. In PowerShell ISE aprire *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e impostare il valore seguente:
    - **$DemoScenario** = **1** (acquisto di biglietti per gli eventi in tutte le sedi)
2. Premere **F5** per eseguire lo script e creare la cronologia di acquisto dei biglietti per tutte le sedi. Con 20 tenant, lo script genera decine di migliaia di biglietti e può impiegare 10 minuti o più.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Distribuire SQL Data Warehouse, Data Factory e l'archivio BLOB 
Nell'app Wingtip Tickets, i dati transazionali dei tenant sono distribuiti in più database. Azure Data Factory viene usato per orchestrare l'estrazione, il caricamento e la trasformazione di tali dati nel data warehouse. Per caricare i dati in SQL Data Warehouse nel modo più efficiente, Azure Data Factory estrae i dati in file BLOB intermedi e quindi usa [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) per caricare i dati nel data warehouse.   

In questo passaggio si distribuiscono le risorse aggiuntive usate nell'esercitazione: un'istanza di SQL Data Warehouse denominata _tenantanalytics_, un'istanza di Azure Data Factory denominata _dbtodwload-\<utente\>_ e un account di archiviazione di Azure denominato _wingtipstaging\<utente\>_. L'account di archiviazione viene usato per inserirvi temporaneamente i file di dati estratti come BLOB prima di caricarli nel data warehouse. Questo passaggio include anche la distribuzione dello schema del data warehouse e la definizione delle pipeline di Azure Data Factory che orchestrano il processo di estrazione, caricamento e trasformazione.
1. In PowerShell ISE aprire *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* e impostare:
    - **$DemoScenario** = **2** (distribuzione di data warehouse, archivio BLOB e data factory per l'analisi dei tenant) 
1. Premere **F5** per eseguire lo script dimostrativo e distribuire le risorse di Azure. 

Esaminare ora le risorse di Azure distribuite.
#### <a name="tenant-databases-and-analytics-store"></a>Database tenant e archivio di analisi
Usare [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) per connettersi ai server **tenants1-dpt-&lt;utente&gt;** e **catalog-dpt-&lt;utente&gt;**. Sostituire &lt;utente&gt; con il valore usato al momento della distribuzione dell'app. Usare account di accesso = *sviluppatore* e la Password = *P\@ssword1*. Per altre indicazioni, vedere l'[esercitazione introduttiva](saas-dbpertenant-wingtip-app-overview.md).

![Connettersi al server di database SQL da SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

In Esplora oggetti:

1. Espandere il server *tenants1-dpt-&lt;utente&gt;*.
1. Espandere il nodo Database per visualizzare l'elenco dei database dei tenant.
1. Espandere il server *catalog-dpt-&lt;utente&gt;*.
1. Verificare che venga visualizzato l'archivio di analisi contenente gli oggetti seguenti:
    1. Tabelle **raw_Tickets**, **raw_Customers**, **raw_Events** e **raw_Venues**, che contengono i dati non elaborati estratti dai database tenant.
    1. Le tabelle dello schema star, ossia **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates**.
    1. Stored procedure **sp_transformExtractedData**, usata per trasformare i dati e caricarli nelle tabelle dello schema star.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Archiviazione BLOB
1. Nel [portale di Azure](https://ms.portal.azure.com) passare al gruppo di risorse usato per la distribuzione dell'applicazione. Verificare che sia stato aggiunto un account di archiviazione denominato **wingtipstaging\<utente\>**.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Fare clic sull'account di archiviazione **wingtipstaging\<utente\>** per esaminare gli oggetti presenti.
1. Fare clic sul riquadro **BLOB**.
1. Fare clic sul contenitore **configfile**.
1. Verificare che **configfile** contenga un file JSON denominato **TableConfig.json**. Tale file contiene i nomi delle tabelle di origine e di destinazione, i nomi delle colonne e il nome della colonna di monitoraggio.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory
Nel [portale di Azure](https://ms.portal.azure.com) verificare nel gruppo di risorse che sia stata aggiunta un'istanza di Azure Data Factory denominata _dbtodwload-\<utente\>_. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Questa sezione esamina la data factory creata. Per avviare la data factory, seguire questa procedura:
1. Nel portale fare clic sulla data factory denominata **dbtodwload-\<utente\>**.
2. Fare clic sul riquadro **Crea e monitora** per avviare la finestra di progettazione di Data Factory in una scheda separata. 

## <a name="extract-load-and-transform-data"></a>Estrarre, caricare e trasformare i dati
Azure Data Factory viene usato per orchestrare l'estrazione, il caricamento e la trasformazione dei dati. In questa esercitazione si estraggono i dati da quattro diverse viste SQL di ognuno dei database tenant: **rawTickets**, **rawCustomers**, **rawEvents** e **rawVenues**. Tali viste includono l'ID della sede, per poter distinguere i dati di ogni sede nel data warehouse. I dati vengono caricati nelle tabelle di staging corrispondenti nel data warehouse: **raw_Tickets**, **raw_customers**, **raw_Events** e **raw_Venue**. Una stored procedure trasforma quindi i dati non elaborati e popola le tabelle dello schema star **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** e **dim_Dates**.

Nella sezione precedente sono state distribuite e inizializzate le risorse di Azure necessarie, inclusa la data factory. La data factory distribuita include le pipeline, i set di dati, i servizi collegati e così via che sono necessari per estrarre, caricare e trasformare i dati dei tenant. Si esamineranno ulteriormente questi oggetti e quindi si attiverà la pipeline per spostare i dati dai database tenant al data warehouse.

### <a name="data-factory-pipeline-overview"></a>Panoramica della pipeline della data factory
Questa sezione esamina gli oggetti creati nella data factory. La figura seguente illustra il flusso di lavoro complessivo della pipeline di Azure Data Factory usata in questa esercitazione. Se si preferisce esplorare la pipeline in un secondo momento ed esaminare per prima cosa i risultati, passare alla successiva sezione **Attivare l'esecuzione della pipeline**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Nella pagina di panoramica passare alla scheda di **creazione** nel pannello sinistro e verificare che siano stati creati tre [pipeline](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) e tre [set di dati](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services).
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Le tre pipeline annidate sono: SQLDBToDW, DBCopy e TableCopy.

La **pipeline 1, SQLDBToDW**, cerca i nomi dei database tenant archiviati nel database di catalogo, nella tabella denominata [__ShardManagement].[ShardsGlobal], ed esegue la pipeline **DBCopy** per ogni database tenant. Al termine viene eseguito lo schema della stored procedure specificata, **sp_TransformExtractedData**. Questa stored procedure trasforma i dati caricati nelle tabelle di staging e popola le tabelle dello schema star.

La **pipeline 2, DBCopy**, cerca i nomi delle tabelle e delle colonne di origine in un file di configurazione archiviato nell'archivio BLOB.  La pipeline **TableCopy** viene quindi eseguita per ognuna delle quattro tabelle: TicketFacts, CustomerFacts, EventFacts e VenueFacts. L'attività **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** viene eseguita in parallelo per tutti i 20 database. Azure Data Factory consente l'esecuzione in parallelo di un massimo di 20 iterazioni del ciclo. Per un maggior numero di database valutare la possibilità di creare più pipeline.    

La **pipeline 3, TableCopy**, usa i numeri di versione di riga nel database SQL (_rowversion_) per identificare le righe che sono state modificate o aggiornate. Questa attività cerca la versione di riga iniziale e finale per l'estrazione delle righe dalle tabelle di origine. La tabella **CopyTracker** archiviata in ogni database tenant tiene traccia dell'ultima riga estratta da ogni tabella di origine in ogni esecuzione. Le righe nuove o modificate vengono copiate nelle tabelle di staging corrispondenti nel data warehouse: **raw_Tickets**, **raw_Customers**, **raw_Venues** e **raw_Events**. L'ultima versione di riga viene infine salvata nella tabella **CopyTracker** e verrà usata come versione di riga iniziale per l'estrazione successiva. 

Sono anche presenti tre servizi collegati con parametri che collegano la data factory ai database SQL di origine, all'istanza di SQL Data Warehouse di destinazione e all'archivio BLOB intermedio. Nella scheda di **creazione** fare clic su **Connessioni** per esaminare i servizi collegati, come illustrato nell'immagine seguente:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Tre set di dati corrispondenti ai tre servizi collegati fanno riferimento ai dati usati nelle attività delle pipeline come input o output. Esaminare ogni set di dati per osservare le connessioni e i parametri usati. _AzureBlob_ punta al file di configurazione contenente le tabelle e le colonne di origine e di destinazione, nonché la colonna di monitoraggio in ogni origine.
  
### <a name="data-warehouse-pattern-overview"></a>Panoramica del modello del data warehouse
SQL Data Warehouse viene usato come archivio di analisi per eseguire l'aggregazione dei dati dei tenant. In questo esempio, i dati vengono caricati in SQL Data Warehouse usando PolyBase. I dati non elaborati vengono caricati in tabelle di staging che includono una colonna Identity per tenere traccia delle righe che sono state trasformate nelle tabelle dello schema star. L'immagine seguente illustra il modello di caricamento: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

In questo esempio vengono usate tabelle delle dimensioni a modifica lenta di tipo 1. Ogni dimensione ha una chiave sostitutiva definita con una colonna Identity. Come procedura consigliata, la tabella delle dimensioni di data viene prepopolata per risparmiare tempo. Per le altre tabelle delle dimensioni viene usata un'istruzione CREATE TABLE AS SELECT... (CTAS) per creare una tabella temporanea contenente le righe modificate e non modificate esistenti e le chiavi sostitutive. A questo scopo viene usato IDENTITY_INSERT=ON. Le nuove righe vengono quindi inserite nella tabella con IDENTITY_INSERT=OFF. Per facilitare il rollback, la tabella delle dimensioni esistente viene rinominata e la tabella temporanea viene a propria volta rinominata in modo da diventare la nuova tabella delle dimensioni. Prima di ogni esecuzione, la tabella delle dimensioni precedente viene eliminata.

Le tabelle delle dimensioni vengono caricate prima della tabella dei fatti. Questa sequenza garantisce che per ogni fatto in arrivo esistano già tutte le dimensioni a cui viene fatto riferimento. Quando i fatti vengono caricati, viene associata la chiave business per ogni dimensione corrispondente e a ogni fatto vengono aggiunte le chiavi sostitutive corrispondenti.

Il passaggio finale della trasformazione elimina i dati di staging pronti per l'esecuzione successiva della pipeline.
   
### <a name="trigger-the-pipeline-run"></a>Attivare l'esecuzione della pipeline
Per eseguire l'intera pipeline di estrazione, caricamento e trasformazione per tutti i database tenant, seguire questa procedura:
1. Nella scheda di **creazione** dell'interfaccia utente di Azure Data Factory selezionare la pipeline **SQLDBToDW** nel riquadro sinistro.
1. Fare clic su **Trigger** e quindi su **Trigger Now** (Attiva adesso) nel menu a discesa visualizzato. Questa azione determina l'esecuzione immediata della pipeline. In uno scenario di produzione si definirà un orario per l'esecuzione della pipeline, per aggiornare i dati in base a una pianificazione.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Nella pagina **Pipeline Run** (Esecuzione di pipeline) fare clic su **Fine**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline
1. Nell'interfaccia utente di Azure Data Factory passare alla scheda di **monitoraggio** dal menu a sinistra.
1. Fare clic su **Aggiorna** finché lo stato della pipeline SQLDBToDW non è **Operazione completata**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Connettersi al data warehouse con SSMS ed eseguire query sulle tabelle dello schema star per verificare che vi siano stati caricati i dati.

Al termine della pipeline, la tabella dei fatti contiene i dati relativi alle vendite di biglietti per tutte le sedi e le tabelle delle dimensioni sono popolate con le sedi, gli eventi e i clienti corrispondenti.

## <a name="data-exploration"></a>Esplorazione dei dati

### <a name="visualize-tenant-data"></a>Visualizzare i dati dei tenant

I dati nello schema star includono tutti i dati relativi alle vendite di biglietti necessari per l'analisi. La visualizzazione grafica dei dati facilita l'individuazione di tendenze in set di dati di grandi dimensioni. In questa sezione si usa **Power BI** per manipolare e visualizzare i dati dei tenant nel data warehouse.

Seguire questa procedura per connettersi a Power BI e importare le viste create in precedenza:

1. Avviare Power BI Desktop.
2. Nella barra multifunzione Home selezionare **Recupera dati** e scegliere **Altro**  dal menu.
3. Nella finestra **Recupera dati** selezionare **Database SQL di Azure**.
4. Nella finestra di accesso al database immettere il nome del server, ossia **catalog-dpt-&lt;utente&gt;.database.windows.net**. Selezionare **Importa** come **Modalità Connettività dati** e quindi fare clic su **OK**. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selezionare **Database** nel riquadro sinistro, quindi immettere nome utente = *developer*, quindi immettere la password = *P\@ssword1*. Fare clic su **Connetti**.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Nel riquadro **Strumento di navigazione** selezionare le tabelle dello schema star nel database di analisi: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** e **dim_Dates**. Selezionare quindi **Carica**. 

Congratulazioni! Il caricamento dei dati in Power BI è stato completato. È ora possibile esplorare visualizzazioni interessanti per ottenere informazioni dettagliate sui tenant. Verrà illustrato in dettaglio come l'analisi possa offrire al team aziendale di Wingtip Tickets alcune raccomandazioni basate sui dati che saranno utili per ottimizzare il modello aziendale e l'esperienza dei clienti.

Per iniziare, analizzare i dati relativi alle vendite di biglietti per individuare la variazione nell'utilizzo tra le diverse sedi. In Power BI selezionare le opzioni illustrate per tracciare un grafico a barre del numero totale di biglietti venduti da ogni sede. A causa della variazione casuale nel generatore di biglietti, i risultati effettivi potrebbero essere diversi.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Il tracciato precedente conferma la variazione nel numero di biglietti venduti da ogni sede. Le sedi che vendono una maggiore quantità di biglietti usano di più il servizio rispetto a quelle con vendite inferiori. Questo potrebbe offrire l'opportunità di adattare l'allocazione delle risorse alle esigenze dei diversi tenant.

È possibile analizzare ulteriormente i dati per individuare la variazione delle vendite di biglietti nel tempo. In Power BI selezionare le opzioni illustrate nell'immagine seguente per tracciare il numero totale di biglietti venduti ogni giorno per un periodo di 60 giorni.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Il grafico precedente mostra un picco nelle vendite di biglietti per alcune sedi. Questi picchi rafforzano l'idea che alcune sedi potrebbe utilizzare le risorse di sistema in modo sproporzionato. Per ora non è stato rilevato un modello ovvio nella ricorrenza dei picchi.

Si analizzerà quindi il significato dei giorni di picchi di vendite. Quando si verificano i picchi dopo che i biglietti vengono messi in vendita? Per tracciare i biglietti venduti in ogni singolo giorno, in Power BI selezionare le opzioni illustrate nell'immagine seguente.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Questo tracciato mostra che alcune sedi vendono un numero elevato di biglietti nel primo giorno di vendita. In queste sedi, non appena i biglietti vengono messi in vendita sembra verificarsi una corsa all'acquisto. Questo picco di attività di alcune sedi potrebbe influire sul servizio per gli altri tenant.

È possibile esaminare di nuovo i dati per verificare se questa corsa all'acquisto riguardi tutti gli eventi ospitati in queste sedi. Nei tracciati precedenti è stato rilevato che Contoso Concert Hall vende molti biglietti e che Contoso presenta anche un picco delle vendite di biglietti in determinati giorni. Modificare la selezione delle opzioni di Power BI per tracciare le vendite cumulative di biglietti di Contoso Concert Hall concentrandosi sulle tendenze di vendita per ogni evento, in modo da verificare se tutti gli eventi seguano lo stesso modello di vendita. Provare a generare un tracciato come quello di seguito.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Questo tracciato delle vendite cumulative di biglietti nel tempo di Contoso Concert Hall per ogni evento mostra che la corsa all'acquisto non si verifica per tutti gli eventi. Modificare la selezione delle opzioni di filtro per esaminare le tendenze di vendita per le altre sedi.

Le informazioni dettagliate sui modelli di vendita dei biglietti potrebbero consentire a Wingtip Tickets di ottimizzare il modello aziendale. Invece di applicare lo stesso addebito a tutti i tenant, Wingtip potrebbe introdurre livelli di servizio con diverse dimensioni di calcolo. Alle sedi più grandi che devono vendere un maggior numero di biglietti al giorno potrà essere offerto un livello più elevato con un contratto di servizio superiore. I database di tali sedi potranno essere inseriti in pool con limiti di risorse per database superiori. Ogni livello di servizio potrà avere un'allocazione di vendite per ogni ora, con addebito di tariffe aggiuntive in caso di superamento dell'allocazione. Le sedi più grandi con picchi periodici di vendite trarranno vantaggio dai livelli superiori e Wingtip Tickets potrà monetizzare il servizio in modo più efficiente.

Nel frattempo, alcuni clienti di Wingtip Tickets segnalano di avere difficoltà a vendere un numero di biglietti sufficiente a giustificare il costo del servizio. Queste informazioni dettagliate potrebbero offrire l'opportunità di incrementare le vendite di biglietti per le sedi con prestazioni inferiori. Vendite superiori aumenteranno il valore percepito del servizio. Fare clic con il pulsante destro del mouse su fact_Tickets e scegliere **Nuova misura**. Immettere l'espressione seguente per la nuova misura denominata **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selezionare le opzioni di visualizzazione seguenti per tracciare la percentuale di biglietti venduta da ogni sede e determinare così il successo relativo di ognuna.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Il tracciato precedente mostra che nonostante la maggior parte delle sedi venda oltre l'80% dei propri biglietti, alcune hanno difficoltà a riempire più della metà dei posti. Modificare la selezione nell'area dei valori per ottenere la percentuale minima o massima di biglietti venduta per ogni sede.

## <a name="embedding-analytics-in-your-apps"></a>Incorporamento dell'analisi nelle app 
Questa esercitazione è incentrata sull'analisi su più tenant allo scopo di garantire al fornitore di software una migliore comprensione dei propri tenant. L'analisi può offrire informazioni dettagliate anche ai _tenant_, affinché possano gestire in modo più efficace le proprie attività aziendali. 

Nell'esempio di Wingtip Tickets si è rilevato in precedenza che le vendite di biglietti tendono a seguire modelli prevedibili. Queste informazioni dettagliate potrebbero essere usate per consentire alle sedi con prestazioni inferiori di incrementare le vendite di biglietti ed eventualmente offrire l'opportunità di impiegare tecniche di apprendimento automatico per prevedere le vendite di biglietti per gli eventi. Si potrebbero anche modellare gli effetti delle variazioni di prezzo, per consentire la previsione dell'impatto dell'offerta di sconti. Power BI Embedded potrebbe essere integrato in un'applicazione di gestione degli eventi per visualizzare le vendite previste e l'impatto degli sconti sul totale dei posti venduti e sui ricavi per eventi con vendite inferiori. Con Power BI Embedded è anche possibile integrare l'effettiva applicazione dello sconto ai prezzi dei biglietti direttamente nell'esperienza di visualizzazione.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire un'istanza di SQL Data Warehouse popolata con uno schema star per l'analisi dei tenant.
> * Usare Azure Data Factory per estrarre i dati da ogni database tenant al data warehouse di analisi.
> * Ottimizzare i dati estratti, riorganizzandoli in uno schema star.
> * Eseguire query sul data warehouse di analisi. 
> * Usare Power BI per visualizzare le tendenze nei dati in tutti i tenant.

Congratulazioni!

## <a name="additional-resources"></a>Risorse aggiuntive

- Altre [esercitazioni basate sull'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
