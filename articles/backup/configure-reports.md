---
title: Configurare report di Backup di Azure
description: Configurare e visualizzare i report per backup di Azure usando Log Analytics e le cartelle di lavoro di Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: c1af9a532b390b428e74957c455988dfd4df3967
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184946"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

Un requisito comune per gli amministratori di backup è ottenere informazioni dettagliate sui backup in base ai dati che si estendono lungo un lungo periodo di tempo. I casi di utilizzo per una soluzione di questo tipo includono:

- L'allocazione e la previsione dello spazio di archiviazione cloud utilizzato.
- Controllo di backup e ripristini.
- Identificazione delle tendenze principali a diversi livelli di granularità.

Backup di Azure offre attualmente una soluzione per la creazione di report che usa i [log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e le cartelle di lavoro di [Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) Queste risorse consentono di ottenere informazioni dettagliate sui backup nell'intero patrimonio di backup. Questo articolo illustra come configurare e visualizzare i report di backup di Azure.

## <a name="supported-scenarios"></a>Scenari supportati

- I report di backup sono supportati per le macchine virtuali di Azure, SQL in macchine virtuali di Azure, SAP HANA/ASE in macchine virtuali di Azure, agente Servizi di ripristino di Microsoft Azure (MARS), Server Backup di Microsoft Azure (MAB) e System Center Data Protection Manager (DPM). I dati per il backup della condivisione file di Azure non sono attualmente visibili nei report di backup.
- Per i carichi di lavoro DPM, i report di backup sono supportati per DPM versione 5.1.363.0 e successive e per l'agente 2.0.9127.0 e versioni successive.
- Per i carichi di lavoro di MAB, i report di backup sono supportati per la versione 13.0.415.0 e successive e per l'agente 2.0.9170.0 e versioni successive.
- I report di backup possono essere visualizzati in tutti gli elementi di backup, gli insiemi di credenziali, le sottoscrizioni e le aree, purché i relativi dati vengano inviati a un'area di lavoro di Log Analytics a cui l'utente può accedere. Per visualizzare i report per un set di insiemi di credenziali, è sufficiente avere l'accesso in lettura all'area di lavoro Log Analytics a cui gli insiemi di credenziali inviano i dati. Non è necessario avere accesso ai singoli insiemi di credenziali.
- Se si è un utente di [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni dei clienti, è possibile usare questi report con Azure Lighthouse per visualizzare i report in tutti i tenant.
- I dati per i processi di backup del log non sono attualmente visualizzati nei report.

## <a name="get-started"></a>Introduzione

Per iniziare a usare i report, seguire questa procedura.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. creare un'area di lavoro Log Analytics o utilizzarne una esistente

Configurare una o più aree di lavoro Log Analytics per archiviare i dati dei report di backup. Il percorso e la sottoscrizione in cui è possibile creare l'area di lavoro Log Analytics sono indipendenti dalla posizione e dalla sottoscrizione in cui si trovano gli insiemi di credenziali.

Per configurare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Per impostazione predefinita, i dati in un'area di lavoro Log Analytics vengono conservati per 30 giorni. Per visualizzare i dati per un orizzonte temporale più lungo, modificare il periodo di conservazione dell'area di lavoro Log Analytics. Per modificare il periodo di memorizzazione, vedere [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. configurare le impostazioni di diagnostica per gli insiemi di credenziali

Azure Resource Manager risorse, ad esempio gli insiemi di credenziali dei servizi di ripristino, registrare le informazioni sulle operazioni pianificate e le operazioni attivate dall'utente come dati di diagnostica.

Nella sezione monitoraggio dell'insieme di credenziali di servizi di ripristino selezionare **impostazioni di diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di servizi di ripristino. Per altre informazioni sull'uso degli eventi di diagnostica, vedere [usare le impostazioni di diagnostica per gli insiemi di credenziali dei servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Riquadro Impostazioni di diagnostica](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Backup di Azure offre anche una definizione di criteri di Azure incorporata, che consente di automatizzare la configurazione delle impostazioni di diagnostica per tutti gli insiemi di credenziali in un ambito specifico. Per informazioni su come usare questi criteri, vedere [configurare le impostazioni di diagnostica dell'insieme di credenziali su larga scala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Dopo aver configurato la diagnostica, potrebbero essere necessarie fino a 24 ore per il completamento del push di dati iniziale. Dopo l'avvio del flusso dei dati nell'area di lavoro Log Analytics, è possibile che i dati dei report non vengano visualizzati immediatamente perché i dati relativi al giorno parziale corrente non vengono visualizzati nei report. Per ulteriori informazioni, vedere [convenzioni utilizzate nei report di backup](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Si consiglia di iniziare a visualizzare i report due giorni dopo la configurazione degli insiemi di credenziali per l'invio di dati a Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. visualizzare i report nel portale di Azure

Dopo aver configurato gli insiemi di credenziali per inviare i dati a Log Analytics, visualizzare i report di backup accedendo al riquadro di qualsiasi insieme di credenziali e selezionando **report di backup**.

![Dashboard dell'insieme di credenziali](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selezionare questo collegamento per aprire la cartella di lavoro report di backup.

> [!NOTE]
>
> - Attualmente, il carico iniziale del report potrebbe richiedere fino a 1 minuto.
> - L'insieme di credenziali di servizi di ripristino è semplicemente un punto di ingresso per i report di backup. Dopo aver aperto la cartella di lavoro dei report di backup dal riquadro dell'insieme di credenziali, selezionare il set di aree di lavoro Log Analytics appropriato per visualizzare i dati aggregati in tutti gli insiemi di credenziali.

Il report contiene diverse schede:

- **Riepilogo**: usare questa scheda per ottenere una panoramica di alto livello del patrimonio di backup. È possibile ottenere un rapido riepilogo del numero totale di elementi di backup, dell'archiviazione cloud totale utilizzata, del numero di istanze protette e della percentuale di esecuzioni dei processi per ogni tipo di carico di lavoro. Per informazioni più dettagliate su un tipo di artefatto di backup specifico, vedere le rispettive schede.

   ![Scheda Summary (Riepilogo)](./media/backup-azure-configure-backup-reports/summary.png)

- **Elementi di backup**: usare questa scheda per visualizzare le informazioni e le tendenze sull'archiviazione cloud usata a livello di elemento di backup. Ad esempio, se si usa SQL in un backup di VM di Azure, è possibile visualizzare la memoria cloud usata per ogni database SQL di cui viene eseguito il backup. È anche possibile scegliere di visualizzare i dati per gli elementi di backup di uno specifico stato di protezione. Se, ad esempio, si seleziona il riquadro **protezione arrestata** nella parte superiore della scheda, vengono filtrati tutti i widget sottostanti per visualizzare solo i dati per gli elementi di backup nello stato di protezione arrestata.

   ![Scheda elementi di backup](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Utilizzo**: usare questa scheda per visualizzare i parametri di fatturazione principali per i backup. Le informazioni visualizzate in questa scheda sono a livello di entità fatturazione (contenitore protetto). Ad esempio, nel caso di un server DPM di cui viene eseguito il backup in Azure, è possibile visualizzare la tendenza delle istanze protette e dello spazio di archiviazione cloud utilizzato per il server DPM. Analogamente, se si usa SQL in backup di Azure o SAP HANA in backup di Azure, questa scheda fornisce informazioni relative all'utilizzo a livello della macchina virtuale in cui sono contenuti questi database.

   ![Scheda utilizzo](./media/backup-azure-configure-backup-reports/usage.png)

- **Processi**: usare questa scheda per visualizzare le tendenze con esecuzione prolungata nei processi, ad esempio il numero di processi non riusciti al giorno e le cause principali dell'errore del processo. È possibile visualizzare queste informazioni a livello di aggregazione e a livello di elemento di backup. Selezionare un particolare elemento di backup in una griglia per visualizzare informazioni dettagliate su ogni processo attivato su tale elemento di backup nell'intervallo di tempo selezionato.

   ![Scheda Processi](./media/backup-azure-configure-backup-reports/jobs.png)

- **Criteri**: usare questa scheda per visualizzare le informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati e l'archiviazione cloud totale utilizzata dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. Selezionare un determinato criterio per visualizzare le informazioni su ogni elemento di backup associato.

   ![Scheda criteri](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

Selezionare il pulsante freccia in giù nell'angolo superiore destro di qualsiasi widget, ad esempio una tabella o un grafico, per esportare il contenuto di tale widget come foglio di Excel così come è con i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina utilizzando la freccia a discesa **righe per pagina** nella parte superiore di ogni griglia.

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard

Selezionare il pulsante Aggiungi nella parte superiore di ogni widget per aggiungere il widget al dashboard portale di Azure. Questa funzionalità consente di creare dashboard personalizzati personalizzati per visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-reports"></a>Report tra tenant

Se si usa [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro della sottoscrizione predefinito. Selezionare il pulsante filtro nell'angolo superiore destro del portale di Azure per scegliere tutte le sottoscrizioni per cui si desidera visualizzare i dati. In questo modo è possibile selezionare Log Analytics aree di lavoro nei tenant per visualizzare i report multitenant.

## <a name="conventions-used-in-backup-reports"></a>Convenzioni utilizzate nei report di backup

- Filtra il lavoro da sinistra verso destra e dall'alto verso il basso in ogni scheda. Ovvero, qualsiasi filtro si applica solo a tutti i widget posizionati a destra del filtro o sotto tale filtro.
- Selezionando un riquadro colorato vengono filtrati i widget sotto il riquadro per i record relativi al valore di tale riquadro. Se ad esempio si seleziona il riquadro **protezione arrestata** nella scheda **elementi di backup** , le griglie e i grafici riportati di seguito vengono filtrati per visualizzare i dati relativi agli elementi di backup nello stato di protezione arrestata.
- I riquadri non colorati non sono selezionabili.
- I dati relativi al giorno parziale corrente non vengono visualizzati nei report. Quindi, quando il valore selezionato dell' **intervallo di tempo** è degli **ultimi 7 giorni**, il report Mostra i record per gli ultimi sette giorni completati. Il giorno corrente non è incluso.
- Il report Mostra i dettagli dei processi (oltre ai processi di log) *attivati* nell'intervallo di tempo selezionato.
- I valori visualizzati per l' **archiviazione cloud** e le **istanze protette** si trovano alla *fine* dell'intervallo di tempo selezionato.
- Gli elementi di backup visualizzati nei report sono quelli presenti alla *fine* dell'intervallo di tempo selezionato. Gli elementi di backup eliminati a metà dell'intervallo di tempo selezionato non vengono visualizzati. La stessa convenzione è valida anche per i criteri di backup.

## <a name="query-load-times"></a>Tempi di caricamento query

I widget del report di backup sono basati sulle query kusto, che vengono eseguite nelle aree di lavoro Log Analytics dell'utente. Queste query comportano in genere l'elaborazione di grandi quantità di dati, con più join che consentono di ottenere informazioni più dettagliate. Di conseguenza, i widget potrebbero non essere caricati istantaneamente quando l'utente Visualizza i report in una proprietà di backup di grandi dimensioni. Questa tabella fornisce una stima approssimativa del tempo che possono essere importati da widget diversi per il caricamento, in base al numero di elementi di backup e all'intervallo di tempo per cui il report viene visualizzato.

| **N. origini dati**                         | **Orizzonte temporale** | **Tempi di caricamento approssimativi**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 mese          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~ 5 K                       | 3 mesi          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~ 10 K                       | 3 mesi          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 1-2 minuti <br> Filtri a livello di report: 25-30 secondi|
| ~ 15 K                       | 1 mese          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 50-60 secondi <br> Filtri a livello di report: 20-25 secondi|
| ~ 15 K                       | 3 mesi          | Riquadri: 20-30 secondi <br> Griglie: 20-30 secondi <br> Grafici: 2-3 minuti <br> Filtri a livello di report: 50-60 secondi |

## <a name="what-happened-to-the-power-bi-reports"></a>Cosa è successo ai report di Power BI? 

- L'app modello di Power BI precedente per la creazione di report, in cui i dati originati da un account di archiviazione di Azure, si trova in un percorso di deprecazione. Si consiglia di iniziare a inviare i dati di diagnostica dell'insieme di credenziali a Log Analytics per visualizzare i report.

- * Inoltre, lo [schema v1](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema) dell'invio dei dati di diagnostica a un account di archiviazione o a un'area di lavoro la è in un percorso di deprecazione. Ciò significa che se sono state scritte query o automazione personalizzate basate sullo schema v1, si consiglia di aggiornare le query in modo che utilizzino lo schema V2 attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sul monitoraggio e la creazione di report con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
