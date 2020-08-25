---
title: Configurare report di Backup di Azure
description: Configurare e visualizzare i report di Backup di Azure usando Log Analytics e le cartelle di lavoro di Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4e5e9258540e5cdab14e438cde96cd89aad7498d
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826855"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

Un'esigenza comune degli amministratori del backup è quella di ottenere informazioni dettagliate sui backup in base a dati che abbracciano un lungo periodo di tempo. I casi d'uso per una soluzione di questo tipo includono:

- Allocazione e previsione dello spazio di archiviazione cloud utilizzato.
- Controllo delle attività dii backup e ripristino.
- Identificazione delle tendenze principali a diversi livelli di granularità.

Oggi Backup di Azure offre una soluzione per la creazione di report che usa i [log di Monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md) e le [cartelle di lavoro di Azure](../azure-monitor/platform/workbooks-overview.md). Queste risorse consentono di acquisire informazioni estremamente dettagliate sui backup nell'intera struttura di backup. Questo articolo spiega come configurare e visualizzare i report di Backup di Azure.

## <a name="supported-scenarios"></a>Scenari supportati

- I report di Backup sono supportati per Macchine virtuali di Azure, SQL in Macchine virtuali di Azure, SAP HANA in Macchine virtuali di Azure, l'agente di Servizi di ripristino di Microsoft Azure (MARS), il server di Backup di Microsoft Azure e System Center Data Protection Manager (DPM). Per il backup di condivisioni file di Azure, vengono visualizzati i dati per tutti i record creati il 1 ° giugno 2020.
- Per i carichi di lavoro DPM, i report di Backup sono supportati per DPM versione 5.1.363.0 e successive e per l'agente MARS versione 2.0.9127.0 e successive.
- Per i carichi di lavoro del server di Backup di Microsoft Azure, i report di Backup sono supportati per il server di Backup di Microsoft Azure versione 13.0.415.0 e successive e per l'agente MARS versione 2.0.9170.0 e successive.
- I report di Backup possono essere visualizzati per tutti gli elementi di backup, gli insiemi di credenziali, le sottoscrizioni e le aree, purché i relativi dati vengano inviati a un'area di lavoro Log Analytics a cui l'utente può accedere. Per visualizzare i report relativi a un set di insiemi di credenziali, è sufficiente avere accesso in lettura all'area di lavoro Log Analytics a cui gli insiemi di credenziali inviano i dati. Non è necessario avere accesso ai singoli insiemi di credenziali.
- Gli utenti di [Azure Lighthouse](../lighthouse/index.yml) con accesso delegato alle sottoscrizioni dei clienti possono usare questi report con Azure Lighthouse per visualizzare i report in tutti i tenant.
- Attualmente i dati possono essere visualizzati nei report di Backup in un massimo di 100 aree di lavoro Log Analytics (in diversi tenant).
- I dati dei processi di backup dei log non sono attualmente visualizzati nei report.

## <a name="get-started"></a>Introduzione

Seguire questi passaggi per iniziare a usare i report.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Creare un'area di lavoro Log Analytics o usarne una esistente

Configurare una o più aree di lavoro Log Analytics in cui archiviare i dati dei report di Backup. La località e la sottoscrizione in cui è possibile creare l'area di lavoro Log Analytics sono indipendenti dalla località e dalla sottoscrizione in cui si trovano gli insiemi di credenziali.

Per informazioni su come configurare un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

Per impostazione predefinita, i dati contenuti in un'area di lavoro Log Analytics vengono conservati per 30 giorni. Per visualizzare i dati relativi a un periodo di tempo più lungo, modificare il periodo di conservazione dell'area di lavoro Log Analytics. Per informazioni su come cambiare il periodo di conservazione, vedere [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](../azure-monitor/platform/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configurare le impostazioni di diagnostica per gli insiemi di credenziali

Le risorse di Azure Resource Manager, come gli insiemi di credenziali di Servizi di ripristino, registrano le informazioni sulle operazioni pianificate e sulle operazioni attivate dall'utente come dati di diagnostica.

Nella sezione di monitoraggio dell'insieme di credenziali di Servizi di ripristino selezionare **Impostazioni di diagnostica** e specificare la destinazione dei dati di diagnostica dell'insieme di credenziali di Servizi di ripristino. Per altre informazioni sull'uso degli eventi di diagnostica, vedere [Usare le impostazioni di diagnostica per gli insiemi di credenziali di Servizi di ripristino](./backup-azure-diagnostic-events.md).

![Riquadro Impostazioni di diagnostica](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Backup di Azure offre anche una definizione di Criteri di Azure predefinita che consente di automatizzare la configurazione delle impostazioni di diagnostica per tutti gli insiemi di credenziali in un ambito specifico. Per informazioni su come usare questi criteri, vedere [Configurare le impostazioni di diagnostica dell'insieme di credenziali su larga scala](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Dopo la configurazione delle impostazioni di diagnostica, il completamento del push di dati iniziale può richiedere fino a 24 ore. Dopo l'avvio del flusso dei dati nell'area di lavoro Log Analytics, è possibile che i dati dei report non vengano visualizzati immediatamente in quanto i dati relativi al giorno parziale corrente non vengono inclusi nei report. Per altre informazioni, vedere [Convenzioni usate nei report di Backup](#conventions-used-in-backup-reports). È consigliabile iniziare a visualizzare i report due giorni dopo aver configurato gli insiemi di credenziali per l'invio di dati a Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Visualizzare i report nel portale di Azure

Dopo aver configurato gli insiemi di credenziali per l'invio dei dati a Log Analytics, visualizzare i report di Backup passando al riquadro di un insieme di credenziali e selezionando **Report di Backup**.

![Dashboard dell'insieme di credenziali](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selezionare questo collegamento per aprire la cartella di lavoro dei report di Backup.

> [!NOTE]
>
> - Attualmente il caricamento iniziale del report potrebbe richiedere fino a 1 minuto.
> - L'insieme di credenziali di Servizi di ripristino è semplicemente un punto di ingresso per i report di Backup. Dopo aver aperto la cartella di lavoro dei report di Backup dal riquadro di un insieme di credenziali, selezionare il set di aree di lavoro Log Analytics appropriato per visualizzare i dati aggregati di tutti gli insiemi di credenziali.

Il report contiene diverse schede:

##### <a name="summary"></a>Riepilogo

Usare questa scheda per ottenere una panoramica di alto livello del patrimonio di backup. È possibile vedere a colpo d'occhio il numero totale di elementi di backup, lo spazio di archiviazione cloud totale utilizzato, il numero di istanze protette e la percentuale di processi completati per tipo di carico di lavoro. Per informazioni più dettagliate su un tipo di elemento di backup specifico, vedere le rispettive schede.

   ![Scheda Riepilogo](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Elementi di backup

Usare questa scheda per visualizzare le informazioni e le tendenze sull'archiviazione cloud usata a livello di elemento di backup. Ad esempio, se si usa SQL in un backup di VM di Azure, è possibile visualizzare lo spazio di archiviazione cloud utilizzato per ogni database SQL di cui viene eseguito il backup. Si può anche scegliere di visualizzare i dati relativi agli elementi di backup di un determinato stato di protezione. Ad esempio, se si seleziona il riquadro **Protezione arrestata** nella parte superiore della scheda, vengono filtrati tutti i widget sottostanti per visualizzare solo i dati relativi agli elementi di backup nello stato di protezione arrestata.

   ![Scheda Elementi di backup](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Utilizzo

Usare questa scheda per visualizzare i parametri di fatturazione principali per i backup. Le informazioni visualizzate in questa scheda sono a livello di entità di fatturazione (contenitore protetto). Se, ad esempio, viene eseguito il backup di un server DPM in Azure, è possibile visualizzare la tendenza delle istanze protette e dello spazio di archiviazione cloud utilizzato per il server DPM. Analogamente, se si usa SQL in Backup di Azure o SAP HANA in Backup di Azure, questa scheda fornisce informazioni relative all'utilizzo a livello della macchina virtuale che contiene questi database.

   ![Scheda Utilizzo](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Per i carichi di lavoro DPM, è possibile che gli utenti visualizzino una lieve differenza (dell'ordine di 20 MB per ogni server DPM) tra i valori di utilizzo indicati nei report rispetto al valore di utilizzo aggregato, come illustrato nella scheda **Panoramica dell'insieme** di credenziali di servizi di ripristino. Questa differenza è rappresentata dal fatto che a ogni server DPM registrato per il backup è associata un'origine dati ' Metadata ' che non viene rilevata come artefatto per la creazione di report.

##### <a name="jobs"></a>Processi

Usare questa scheda per visualizzare le tendenze con esecuzione prolungata nei processi, ad esempio il numero di processi non riusciti al giorno e le cause principali dell'errore del processo. È possibile visualizzare queste informazioni sia a livello aggregato che a livello di elemento di backup. Selezionare uno specifico elemento di backup in una griglia per visualizzare informazioni dettagliate su ogni processo attivato su tale elemento di backup nell'intervallo di tempo selezionato.

   ![Scheda Processi](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Criteri

Usare questa scheda per visualizzare informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati e l'archiviazione cloud totale utilizzata dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. Selezionare un criterio specifico per visualizzare le informazioni su ogni elemento di backup associato.

   ![Scheda Criteri](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Ottimizzazione

Usare questa scheda per ottenere visibilità sulle possibili opportunità di ottimizzazione dei costi per i backup. Di seguito sono riportati gli scenari per i quali la scheda Ottimizza fornisce attualmente informazioni dettagliate:

###### <a name="inactive-resources"></a>Risorse inattive

Utilizzando questa visualizzazione, è possibile identificare gli elementi di backup per i quali non è stato eseguito il backup per un periodo di tempo significativo. Questo potrebbe significare che il computer sottostante di cui viene eseguito il backup non esiste più (e pertanto comporta backup non riusciti) oppure si è verificato un problema con il computer che impedisce l'esecuzione affidabile dei backup.

Per visualizzare le risorse inattive, passare alla scheda **ottimizza** e selezionare il riquadro **risorse inattive** . Selezionare questo riquadro per visualizzare una griglia contenente i dettagli di tutte le risorse inattive presenti nell'ambito selezionato. Per impostazione predefinita, la griglia Mostra gli elementi che non hanno un punto di ripristino negli ultimi sette giorni. Per trovare le risorse inattive per un intervallo di tempo diverso, è possibile modificare il filtro **intervallo di tempo** nella parte superiore della scheda.

Una volta identificata una risorsa inattiva, è possibile esaminare ulteriormente il problema passando al dashboard dell'elemento di backup o al riquadro delle risorse di Azure per tale risorsa, ove applicabile. A seconda dello scenario, è possibile scegliere di arrestare il backup per il computer (se non esiste più) ed eliminare i backup superflui, che consentono di risparmiare sui costi, oppure è possibile risolvere i problemi nel computer per assicurarsi che i backup vengano eseguiti in modo affidabile.

![Ottimizzare le risorse inattive nella scheda](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Elementi di backup con durata di conservazione estesa

Utilizzando questa visualizzazione, è possibile identificare gli elementi per i quali i backup sono conservati per un periodo di tempo più lungo rispetto a quello richiesto dall'organizzazione.

Selezionando il riquadro **ottimizzazioni dei criteri** seguito dal riquadro **ottimizzazioni di conservazione** viene visualizzata una griglia contenente tutti gli elementi di backup per i quali la conservazione del punto di conservazione giornaliero, settimanale, mensile o annuale è maggiore di un valore specificato. Per impostazione predefinita, nella griglia vengono visualizzati tutti gli elementi di backup nell'ambito selezionato. È possibile utilizzare i filtri per la conservazione giornaliera, settimanale, mensile e annuale di RP per filtrare ulteriormente la griglia e identificare gli elementi per i quali potrebbe essere possibile ridurre la conservazione per risparmiare sui costi di archiviazione di backup.

Per i carichi di lavoro di database come SQL e SAP HANA, i periodi di conservazione visualizzati nella griglia corrispondono ai periodi di conservazione dei punti di backup completi e non ai punti di backup differenziali. Lo stesso vale anche per i filtri di conservazione.  

![Ottimizzare le ottimizzazioni per la memorizzazione nella scheda](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Database configurati per il backup completo giornaliero 

Utilizzando questa visualizzazione, è possibile identificare i carichi di lavoro del database configurati per il backup completo giornaliero. Spesso, l'uso del backup differenziale giornaliero insieme al backup completo settimanale è più conveniente.

Selezionando il riquadro **ottimizzazioni dei criteri** seguito dal riquadro **ottimizzazioni pianificazione backup** , viene visualizzata una griglia contenente tutti i database con criteri di backup completi giornalieri. È possibile scegliere di passare a un particolare elemento di backup e modificare il criterio in modo da usare il backup differenziale giornaliero con backup completo settimanale.

Il filtro del **tipo di gestione di backup** nella parte superiore della scheda deve avere gli elementi **SQL in VM di Azure** e **SAP Hana nella macchina virtuale di Azure** selezionata per consentire alla griglia di visualizzare i carichi di lavoro del database come previsto.

![Ottimizza la scheda-ottimizzazioni della pianificazione dei backup](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

Selezionare il pulsante freccia in giù nell'angolo superiore destro di qualsiasi widget, come una tabella o un grafico, per esportare il contenuto del widget come foglio di Excel così com'è con i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina usando l'elenco a discesa **Righe per pagina** nella parte superiore di ogni scheda.

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard

Selezionare l'icona a forma di puntina nella parte superiore di ogni widget per aggiungere il widget al dashboard del portale di Azure. Questa funzionalità consente di creare dashboard personalizzati mirati a visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-reports"></a>Report tra tenant

Se si usa [Azure Lighthouse](../lighthouse/index.yml) con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro della sottoscrizione predefinito. Selezionare il pulsante di filtro nell'angolo superiore destro del portale di Azure per scegliere tutte le sottoscrizioni di cui si vogliono visualizzare i dati. In questo modo è possibile selezionare le aree di lavoro Log Analytics nei vari tenant per visualizzare i report multitenant.

## <a name="conventions-used-in-backup-reports"></a>Convenzioni usate nei report di Backup

- I filtri vengono applicati da sinistra a destra e dall'alto in basso in ogni scheda. In altre parole, qualsiasi filtro si applica solo a tutti i widget posizionati a destra del filtro o sotto di esso.
- Se si seleziona un riquadro colorato vengono filtrati i widget sotto il riquadro per i record relativi al valore di tale riquadro. Ad esempio, se si seleziona il riquadro **Protezione arrestata** nella scheda **Elementi di backup**, vengono filtrati le griglie e i grafici sottostanti per visualizzare i dati relativi agli elementi di backup nello stato di protezione arrestata.
- I riquadri non colorati non sono selezionabili.
- I dati relativi alla giornata odierna parziale non vengono visualizzati nei report. Quindi, quando il valore selezionato in **Intervallo di tempo** è **Ultimi 7 giorni**, il report mostra i record per gli ultimi sette giorni completati. Il giorno corrente non è incluso.
- Il report mostra i dettagli dei processi (a parte i processi di log) *attivati* nell'intervallo di tempo selezionato.
- I valori visualizzati per **Archiviazione cloud** e **Istanze protette** sono alla *fine* dell'intervallo di tempo selezionato.
- Gli elementi di backup visualizzati nei report sono gli elementi presenti alla *fine* dell'intervallo di tempo selezionato. Gli elementi di backup eliminati durante l'intervallo di tempo selezionato non vengono visualizzati. La stessa convenzione è valida anche per i criteri di backup.

## <a name="query-load-times"></a>Tempi di caricamento delle query

I widget del report di Backup sono basati sulle query kusto, che vengono eseguite nelle aree di lavoro Log Analytics dell'utente. Queste query comportano in genere l'elaborazione di grandi quantità di dati, con più join che consentono di ottenere informazioni ancora più dettagliate. Di conseguenza, i widget potrebbero non essere caricati istantaneamente quando l'utente visualizza i report in una struttura di backup di grandi dimensioni. Questa tabella fornisce una stima approssimativa del tempo di caricamento di diversi widget, in base al numero di elementi di Backup e all'intervallo di tempo per cui il report viene visualizzato.

| **N. origini dati**                         | **Orizzonte temporale** | **Tempi di caricamento approssimativi**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5.000                       | 1 mese          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~5.000                       | 3 mesi          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~10.000                       | 3 mesi          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 1-2 minuti <br> Filtri a livello di report: 25-30 secondi|
| ~15.000                       | 1 mese          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 50-60 secondi <br> Filtri a livello di report: 20-25 secondi|
| ~15.000                       | 3 mesi          | Riquadri: 20-30 secondi <br> Griglie: 20-30 secondi <br> Grafici: 2-3 minuti <br> Filtri a livello di report: 50-60 secondi |

## <a name="what-happened-to-the-power-bi-reports"></a>Cosa è successo ai report di Power BI?

- La precedente app modello di Power BI per la creazione di report, in cui i dati erano originati da un account di archiviazione di Azure, è in fase di deprecazione. È consigliabile iniziare a inviare i dati di diagnostica dell'insieme di credenziali a Log Analytics per visualizzare i report.

- Anche lo [schema V1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) di invio dei dati di diagnostica a un account di archiviazione o a un'area di lavoro Log Analytics è in fase di deprecazione. Ciò significa che se sono state scritte query o automazione personalizzate basate sullo schema v1, è consigliabile aggiornare queste query per usare lo schema V2 attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sul monitoraggio e la creazione di report con Backup di Azure](./backup-azure-monitor-alert-faq.md)
