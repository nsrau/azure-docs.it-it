---
title: Configurare report di Backup di Azure
description: Configurare e visualizzare i report per Backup di Azure usando Log Analytics e le cartelle di lavoro di AzureConfigure and view reports for Azure Backup by using Log Analytics and Azure workbooks
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617818"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

Un requisito comune per gli amministratori di backup consiste nell'ottenere informazioni dettagliate sui backup in base ai dati che si estendono su un lungo periodo di tempo. I casi d'uso per una soluzione di questo tipo includono:Use cases for such a solution include:

 - Allocazione e previsione dello storage cloud consumato.
 - Controllo dei backup e dei ripristini.
 - Identificazione delle tendenze chiave a diversi livelli di granularità.

Oggi, Backup di Azure offre una soluzione di creazione di report che usa i log di [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e le cartelle di lavoro di [Azure.Today,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)Azure Backup provides a reporting solution that uses Azure Monitor logs and Azure workbooks . Queste risorse consentono di ottenere informazioni dettagliate sui backup nell'intera proprietà di backup. Questo articolo illustra come configurare e visualizzare i report di Backup di Azure.This article explains how to configure and view Azure Backup reports.

## <a name="supported-scenarios"></a>Scenari supportati

* I report di backup sono supportati per le macchine virtuali di Azure, SQL nelle macchine virtuali di Azure, SAP HANA/ASE nelle macchine virtuali di Azure, agente di Microsoft Azure Recovery Services (MARS), Microsoft Azure Backup Server (MABS) e System Center Data Protection Manager (DPM).
* Per i carichi di lavoro DPM, i report di backup sono supportati per DPM versione 5.1.363.0 e successive e Agent versione 2.0.9127.0 e successive.
* Per i carichi di lavoro MABS, i report di backup sono supportati per MABS versione 13.0.415.0 e successive e Agent Versione 2.0.9170.0 e successive.
* I report di backup possono essere visualizzati in tutti gli elementi di backup, gli insiemi di credenziali, le sottoscrizioni e le aree, purché i dati vengano inviati a un'area di lavoro di Log Analytics a cui l'utente ha accesso. Per visualizzare i report per un set di vault, è sufficiente disporre dell'accesso in lettura all'area di lavoro di Log Analytics a cui i vault inviano i dati. Non è necessario avere accesso ai singoli vault.
* Se sei un utente di [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni dei tuoi clienti, puoi usare questi report con Azure Lighthouse per visualizzare i report in tutti i tenant.
* I dati per i processi di backup del log attualmente non vengono visualizzati nei report.

## <a name="get-started"></a>Introduzione

Seguire questi passaggi per iniziare a utilizzare i report.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Creare un'area di lavoro di Log Analytics o utilizzarne una esistente

Configurare una o più aree di lavoro di Log Analytics per archiviare i dati dei report di backup. La posizione e la sottoscrizione in cui è possibile creare questo workspace di Log Analytics sono indipendenti dalla posizione e dalla sottoscrizione in cui sono presenti gli archivi. 

Per configurare un'area di lavoro di Log Analytics, vedere [Creare un'area](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)di lavoro di Log Analytics nel portale di Azure.To set up a Log Analytics workspace, see Create a Log Analytics workspace in the Azure portal.

Per impostazione predefinita, i dati in un'area di lavoro di Log Analytics vengono conservati per 30 giorni. Per visualizzare i dati per un orizzonte temporale più lungo, modificare il periodo di conservazione dell'area di lavoro di Log Analytics. Per modificare il periodo di conservazione, vedere [Gestire l'utilizzo e i costi con i log](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)di Monitoraggio di Azure.To change the retention period, see Manage usage and costs with Azure Monitor logs.

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configurare le impostazioni di diagnostica per gli insiemi di credenziali

Le risorse di Azure Resource Manager, ad esempio gli insiemi di credenziali di Servizi di ripristino, registrano le informazioni sulle operazioni pianificate e sulle operazioni attivate dall'utente come dati di diagnostica. 

Nella sezione di monitoraggio dell'insieme di credenziali di Servizi di ripristino selezionare **Impostazioni di diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di Servizi di ripristino. Per ulteriori informazioni sull'utilizzo degli eventi di diagnostica, vedere Utilizzare le impostazioni di [diagnostica per gli insiemi](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)di credenziali di Servizi di ripristino .

![Riquadro Impostazioni di diagnostica](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Backup di Azure offre anche criteri di Azure predefiniti, che automatizzano la configurazione delle impostazioni di diagnostica per tutti gli insiemi di credenziali in un determinato ambito. Per informazioni su come utilizzare questo criterio, vedere Configurare le impostazioni di [diagnostica dell'insieme di credenziali su scala.](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Dopo aver configurato la diagnostica, il completamento del push dei dati iniziale potrebbe richiedere fino a 24 ore. Dopo l'avvio dei dati nell'area di lavoro di Log Analytics, è possibile che i dati non vengano visualizzati immediatamente nei rapporti perché i dati relativi al giorno parziale corrente non vengono visualizzati nei rapporti. Per ulteriori informazioni, vedere [Convenzioni utilizzate nei report di backup](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). È consigliabile iniziare a visualizzare i report due giorni dopo aver configurato gli insiemi di credenziali per l'invio dei dati a Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Visualizzare i report nel portale di Azure

Dopo aver configurato i vault per l'invio di dati a Log Analytics, visualizzare i report di backup accedendo a qualsiasi riquadro del vault e selezionando **Report di backup**.

![Dashboard dell'insieme di credenziali](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selezionare questo collegamento per aprire la cartella di lavoro Report di backup.

> [!NOTE]
> * Attualmente, il caricamento iniziale del report potrebbe richiedere fino a 1 minuto.
> * L'insieme di credenziali dei servizi di ripristino è semplicemente un punto di ingresso per i report di backup. Dopo aver aperto la cartella di lavoro del report Backup dal riquadro di un insieme di credenziali, selezionare il set appropriato di aree di lavoro di Log Analytics per visualizzare i dati aggregati in tutti gli insiemi di credenziali.

Il report contiene varie schede:

* **Riepilogo**: Utilizzare questa scheda per ottenere una panoramica generale della proprietà di backup. È possibile ottenere una rapida occhiata del numero totale di elementi di backup, dello spazio di archiviazione totale nel cloud utilizzato, del numero di istanze protette e della percentuale di successo dei processi per ogni tipo di carico di lavoro. Per informazioni più dettagliate su un tipo di elemento di backup specifico, passare alle rispettive schede.

   ![Scheda Summary (Riepilogo)](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementi di backup**: utilizzare questa scheda per visualizzare le informazioni e le tendenze sullo storage cloud utilizzato a livello di elemento di backup. Ad esempio, se si usa SQL in un backup della macchina virtuale di Azure, è possibile visualizzare lo spazio di archiviazione cloud usato per ogni database SQL di cui viene eseguito il backup. È inoltre possibile scegliere di visualizzare i dati per gli elementi di backup di un determinato stato di protezione. Ad esempio, selezionando il riquadro **Protezione interrotta** nella parte superiore della scheda vengono filtrati tutti i widget sottostanti per visualizzare i dati solo per gli elementi di backup nello stato Protezione interrotta.

   ![Scheda Elementi di backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilizzo**: utilizzare questa scheda per visualizzare i parametri di fatturazione chiave per i backup. Le informazioni visualizzate in questa scheda sono a livello di entità di fatturazione (contenitore protetto). Ad esempio, nel caso in cui il backup di un server DPM venga eseguito in Azure, è possibile visualizzare la tendenza delle istanze protette e dell'archiviazione cloud utilizzate per il server DPM. Analogamente, se si usa SQL in Backup di Azure o SAP HANA in Backup di Azure, questa scheda fornisce informazioni relative all'utilizzo a livello della macchina virtuale in cui sono contenuti questi database.

   ![Scheda Utilizzo](./media/backup-azure-configure-backup-reports/usage.png)

* **Processi:** utilizzare questa scheda per visualizzare le tendenze a esecuzione prolungata dei processi, ad esempio il numero di processi non riusciti al giorno e le principali cause di errore del processo. È possibile visualizzare queste informazioni sia a livello di aggregazione che a livello di elemento di backup. Selezionare un particolare elemento di backup in una griglia per visualizzare informazioni dettagliate su ogni processo attivato su tale elemento di backup nell'intervallo di tempo selezionato.

   ![Scheda Processi](./media/backup-azure-configure-backup-reports/jobs.png)

* **Criteri**: utilizzare questa scheda per visualizzare informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati e lo spazio di archiviazione cloud totale utilizzato dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. Selezionare un criterio specifico per visualizzare le informazioni su ciascuno degli elementi di backup associati.

   ![Scheda Criteri](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

Selezionare il pulsante freccia giù in alto a destra di qualsiasi widget, ad esempio una tabella o un grafico, per esportare il contenuto di tale widget come un foglio di Excel così com'è con i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina utilizzando la freccia a discesa **Righe per pagina** nella parte superiore di ogni griglia.

## <a name="pin-to-dashboard"></a>Aggiungi al dashboard

Selezionare il pulsante di creazione di segnaposto nella parte superiore di ogni widget per aggiungere il widget al dashboard del portale di Azure.Select the pin button at the top of each widget to pin the widget to your Azure portal dashboard. Questa funzionalità consente di creare dashboard personalizzati personalizzati per visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-reports"></a>Report cross-tenant

Se si usa [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro di sottoscrizione predefinito. Selezionare il pulsante del filtro nell'angolo superiore destro del portale di Azure per scegliere tutte le sottoscrizioni per cui si desidera visualizzare i dati. In questo modo è possibile selezionare le aree di lavoro di Log Analytics tra i tenant per visualizzare i report multi-tenant.

## <a name="conventions-used-in-backup-reports"></a>Convenzioni utilizzate nei rapporti di backup

* I filtri funzionano da sinistra a destra e dall'alto verso il basso in ogni scheda. Ovvero, qualsiasi filtro si applica solo a tutti i widget posizionati a destra di tale filtro o al di sotto di tale filtro. 
* La selezione di un riquadro colorato filtra i widget sotto il riquadro per i record relativi al valore di tale riquadro. Ad esempio, selezionando il riquadro **Protezione interrotta** nella scheda Elementi di **backup** vengono filtrate le griglie e i grafici sottostanti per visualizzare i dati per gli elementi di backup nello stato Protezione interrotta.
* I riquadri non colorati non sono selezionabili.
* I dati per il giorno parziale corrente non vengono visualizzati nei rapporti. Pertanto, quando il valore selezionato di **Intervallo** di tempo è **Ultimi 7 giorni**, il report mostra i record degli ultimi sette giorni completati. Il giorno corrente non è incluso.
* Il report mostra i dettagli dei processi (ad eccezione dei processi di log) *attivati* nell'intervallo di tempo selezionato. 
* I valori visualizzati per **Archiviazione cloud** e **Istanze protette** sono alla *fine* dell'intervallo di tempo selezionato.
* Gli elementi di backup visualizzati nei report sono gli elementi presenti alla *fine* dell'intervallo di tempo selezionato. Gli elementi di backup eliminati al centro dell'intervallo di tempo selezionato non vengono visualizzati. La stessa convenzione si applica anche ai criteri di backup.

## <a name="query-load-times"></a>Tempi di caricamento delle query

I widget nel report Backup sono alimentati da query Kusto, che vengono eseguite nelle aree di lavoro di Log Analytics dell'utente. Queste query implicano in genere l'elaborazione di grandi quantità di dati, con più join per abilitare informazioni più dettagliate. Di conseguenza, i widget potrebbero non essere caricati istantaneamente quando l'utente visualizza i report in un'area di backup di grandi dimensioni. Questa tabella fornisce una stima approssimativa del tempo che diversi widget possono richiedere per il caricamento, in base al numero di elementi di backup e all'intervallo di tempo per il quale viene visualizzato il report.

| **- Fonti di dati**                         | **Orizzonte temporale** | **Tempi di caricamento approssimativi**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| 5 K USD                       | 1 mese          | Piastrelle: 5-10 sec <br> Griglie: 5-10 sec <br> Grafici: 5-10 sec <br> Filtri a livello di report: 5-10 secondiReport-level filters: 5-10 secs|
| 5 K USD                       | 3 mesi          | Piastrelle: 5-10 sec <br> Griglie: 5-10 sec <br> Grafici: 5-10 sec <br> Filtri a livello di report: 5-10 secondiReport-level filters: 5-10 secs|
| 10 K USD                       | 3 mesi          | Piastrelle: 15-20 sec <br> Griglie: 15-20 sec <br> Grafici: 1-2 min <br> Filtri a livello di report: 25-30 secondiReport-level filters: 25-30 secs|
| 15 K USD                       | 1 mese          | Piastrelle: 15-20 sec <br> Griglie: 15-20 sec <br> Grafici: 50-60 sec <br> Filtri a livello di report: 20-25 secondi|
| 15 K USD                       | 3 mesi          | Piastrelle: 20-30 sec <br> Griglie: 20-30 sec <br> Grafici: 2-3 min <br> Filtri a livello di report: 50-60 secondiReport-level filters: 50-60 secs |

## <a name="what-happened-to-the-power-bi-reports"></a>Cosa è successo ai report di Power BI? 
* L'app modello di Power BI precedente per la creazione di report, ovvero i dati di origine da un account di archiviazione di Azure, si trova in un percorso di deprecazione. È consigliabile iniziare a inviare i dati di diagnostica del vault a Log Analytics per visualizzare i report.

* Inoltre, lo schema V1 per l'invio di dati di diagnostica a un account di archiviazione o a un'area di lavoro di Log Analytics si trova anche in un percorso di deprecazione. Se sono state scritte query o automazioni personalizzate basate sullo schema V1, si consiglia di aggiornarle per utilizzare lo schema V2 attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul monitoraggio e la creazione di report con Backup di AzureLearn more about monitoring and reporting with Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
