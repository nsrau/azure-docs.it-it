---
title: Configurare report di Backup di Azure
description: Configurare e visualizzare i report per Backup di Azure usando Log Analytics e cartelle di lavoro di AzureConfigure and view reports for Azure Backup using Log Analytics and Azure Workbooks
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672655"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

Un requisito comune per gli amministratori di backup consiste nell'ottenere informazioni dettagliate sui backup, in base ai dati che coprono un lungo periodo di tempo. Potrebbero esserci più casi d'uso per una soluzione di questo tipo: allocazione e previsione dell'archiviazione cloud utilizzata, controllo di backup e ripristini e identificazione delle tendenze chiave a diversi livelli di granularità.

Oggi, Backup di Azure offre una soluzione di creazione di report che sfrutta i log di Monitoraggio di [Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e le cartelle di lavoro di [Azure,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)consentendo di ottenere informazioni dettagliate sui backup nell'intera proprietà di backup. In questo articolo viene illustrato come configurare e visualizzare i report di backup.

## <a name="supported-scenarios"></a>Scenari supportati

* I report di backup sono supportati per le macchine virtuali di Azure, SQL nelle macchine virtuali di Azure, SAP HANA/ASE nelle macchine virtuali di Azure, Azure Backup Agent (MARS), Azure Backup Server (MABS) e System Center DPM.
* Per i carichi di lavoro DPM, i report di backup sono supportati per DPM versione 5.1.363.0 e successive e Versione agente 2.0.9127.0 e successive.
* Per i carichi di lavoro MABS, i report di backup sono supportati per MABS versione 13.0.415.0 e successive e Agent Versione 2.0.9170.0 e successive.
* I report di backup possono essere visualizzati in tutti gli elementi di backup, gli insiemi di credenziali, le sottoscrizioni e le aree geografiche, purché i dati vengano inviati a un'area di lavoro di Log Analytics (LA) a cui l'utente ha accesso. Si noti che per visualizzare i report per un insieme di vault, è sufficiente disporre **dell'accesso del lettore all'area** di lavoro di LA a cui i vault inviano i dati. Non **è necessario** avere accesso ai singoli vault.
* Se si è un utente [di Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni dei clienti, è possibile usare questi report con Azure Lighthouse per visualizzare i report in tutti i tenant.
* I dati per i processi di backup del log non vengono attualmente visualizzati nei report.

## <a name="getting-started"></a>Introduzione

Per iniziare a utilizzare i report, attenersi alla procedura descritta di seguito:

1. **Creare un'area di lavoro di Log Analytics (LA) (o usarne una esistente):**

È necessario configurare una o più aree di lavoro LA per archiviare i dati dei report di backup. La posizione e la sottoscrizione in cui è possibile creare questo workspace di LA sono indipendenti dalla posizione e dalla sottoscrizione in cui sono presenti gli insiemi di credenziali. 

Fare riferimento all'articolo seguente: Creare un'area di lavoro di [Log Analytics nel portale](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) di Azure per configurare un'area di lavoro DI LA.

Per impostazione predefinita, i dati in un'area di lavoro LA vengono conservati per 30 giorni. Per visualizzare i dati per un orizzonte temporale più lungo, modificare il periodo di conservazione dell'area di lavoro di LA. Per modificare il periodo di conservazione, vedere l'articolo seguente: [Manage usage and costs with Azure Monitor Logs](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Configurare le impostazioni di diagnostica per gli insiemi di credenziali:**

Le risorse di Azure Resource Manager, ad esempio gli insiemi di credenziali di Servizi di ripristino, registrano le informazioni sulle operazioni pianificate e sulle operazioni attivate dall'utente come dati di diagnostica. 

Nella sezione di monitoraggio dell'insieme di credenziali di Servizi di ripristino selezionare **Impostazioni di diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di Servizi di ripristino. [Ulteriori informazioni sull'utilizzo degli eventi di diagnostica](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Pannello Impostazioni di diagnostica](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Backup di Azure offre anche un criterio di Azure predefinito, che automatizza la configurazione delle impostazioni di diagnostica per tutti gli insiemi di credenziali in un determinato ambito. Fare riferimento al seguente articolo per informazioni su come utilizzare questo criterio: [Configurare le impostazioni di diagnostica di Vault su larga scala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Dopo aver configurato la diagnostica, potrebbero essere decollate fino a 24 ore prima che il push dei dati iniziale venga completato. Una volta che i dati iniziano a fluire nell'area di lavoro di LA, potrebbe non essere possibile visualizzare immediatamente i dati nei report, poiché i dati relativi al giorno parziale corrente non vengono visualizzati nei report (ulteriori dettagli [qui](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). Pertanto, si consiglia di iniziare a visualizzare i report 2 giorni dopo aver configurato i vault per l'invio di dati a Log Analytics.

3. **Visualizzare i report nel portale di Azure:View reports on the Azure portal:**

Dopo aver configurato i vault per l'invio di dati a LA, visualizzare i report di backup passando al pannello di qualsiasi vault e facendo clic sulla voce di menu Report di **backup.** 

![Vault Dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Facendo clic su questo collegamento viene visualizzata la cartella di lavoro del rapporto di backup.

> [!NOTE]
> * Attualmente, il caricamento iniziale del rapporto può richiedere fino a 1 minuto.
> * L'insieme di credenziali dei servizi di ripristino è semplicemente un punto di ingresso per i report di backup. Una volta aperta la cartella di lavoro Report di backup dal pannello di un vault, sarà possibile visualizzare i dati aggregati in tutti i vault (selezionando il set appropriato di aree di lavoro DI LA).

Di seguito è riportata una descrizione delle varie schede contenute nel report:

* **Riepilogo:** la scheda Riepilogo fornisce una panoramica generale del valore di backup. Nella scheda Riepilogo è possibile ottenere una rapida panoramica del numero totale di elementi di backup, dello spazio di archiviazione cloud totale utilizzato, del numero di istanze protette e della percentuale di successo del processo per ogni tipo di carico di lavoro. Per informazioni più dettagliate su un tipo di elemento di backup specifico, passare alle rispettive schede.

![Scheda Summary (Riepilogo)](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementi di backup:** la scheda Elementi di backup consente di visualizzare le informazioni e le tendenze sullo spazio di archiviazione cloud utilizzato a livello di elemento di backup. Ad esempio, se si usa SQL nel backup della macchina virtuale di Azure, è possibile visualizzare lo spazio di archiviazione cloud usato per ogni database SQL di cui viene eseguito il backup. È inoltre possibile scegliere di visualizzare i dati per gli elementi di backup di un determinato stato di protezione. Ad esempio, facendo clic sul riquadro **Protezione interrotta** nella parte superiore della scheda, vengono filtrati tutti i widget seguenti per visualizzare i dati solo per gli elementi di backup nello stato Protezione interrotta.

![Scheda Elementi di backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilizzo:** la scheda Utilizzo consente di visualizzare i parametri di fatturazione principali per i backup. Le informazioni visualizzate in questa scheda sono a livello di entità di fatturazione (contenitore protetto). Ad esempio, nel caso in cui il backup di un server DPM venga eseguito in Azure, è possibile visualizzare la tendenza delle istanze protette e dell'archiviazione cloud utilizzate per il server DPM. Analogamente, se si usa SQL in Backup di Azure o SAP HANA in Backup di Azure, questa scheda fornisce informazioni relative all'utilizzo a livello della macchina virtuale in cui sono contenuti questi database.

![Scheda Utilizzo](./media/backup-azure-configure-backup-reports/usage.png)

* **Processi:** la scheda Processi consente di visualizzare le tendenze a esecuzione prolungata dei processi, ad esempio il numero di processi non riusciti al giorno e le principali cause di errore del processo. È possibile visualizzare queste informazioni sia a livello di aggregazione che a livello di elemento di backup. Facendo clic su un particolare elemento di backup in una griglia è possibile visualizzare informazioni dettagliate su ogni processo attivato su tale elemento di backup nell'intervallo di tempo selezionato.

![Scheda Processi](./media/backup-azure-configure-backup-reports/jobs.png)

* **Criteri:** la scheda Criteri consente di visualizzare informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati e lo spazio di archiviazione cloud totale utilizzato dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. Facendo clic su un criterio specifico è possibile visualizzare informazioni su ciascuno degli elementi di backup associati.

![Scheda Criteri](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Esportazione in Excel

Facendo clic sul pulsante freccia giù in alto a destra di qualsiasi widget (tabella/grafico) si esporta il contenuto di tale widget come foglio Excel, così come i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina utilizzando l'elenco a discesa **Righe per pagina** nella parte superiore di ogni griglia.

## <a name="pinning-to-dashboard"></a>Aggiunta al dashboard

Fare clic sull'icona Aggiungi nella parte superiore di ogni widget per aggiungere il widget al dashboard del portale di Azure. Ciò consente di creare dashboard personalizzati personalizzati personalizzati per visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-reports"></a>Report cross-tenant

Se si è un utente [di Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro di sottoscrizione predefinito (facendo clic sull'icona del filtro in alto a destra nel portale di Azure) per scegliere tutte le sottoscrizioni di cui si desidera visualizzare i dati. In questo modo sarà possibile selezionare le aree di lavoro LA tra i tenant per visualizzare i report multi-tenant.

## <a name="conventions-used-in-backup-reports"></a>Convenzioni utilizzate nei rapporti di backup

* I filtri funzionano da sinistra a destra e dall'alto verso il basso in ogni scheda, ovvero qualsiasi filtro si applica solo a tutti i widget posizionati a destra di tale filtro o sotto tale filtro. 
* Facendo clic su un riquadro colorato vengono filtrati i widget sotto il riquadro per i record relativi al valore di tale riquadro. Ad esempio, facendo clic sul riquadro *Protezione interrotta* nella scheda Elementi di backup vengono filtrate le griglie e i grafici sottostanti per visualizzare i dati per gli elementi di backup nello stato "Protezione interrotta".
* I riquadri non colorati non sono selezionabili.
* I dati relativi alla giornata odierna parziale non vengono mostrati nei report. Pertanto, quando il valore selezionato di **Intervallo** di tempo ***è, Ultimi 7 giorni***, il report mostra i record degli ultimi 7 giorni completati (che non include il giorno corrente).
* Il report mostra i dettagli dei processi (ad eccezione dei processi di log) **attivati** nell'intervallo di tempo selezionato. 
* I valori visualizzati per Archiviazione cloud e Istanze protette si trovano alla **fine** dell'intervallo di tempo selezionato.
* Gli elementi di backup visualizzati nei report sono gli elementi presenti alla **fine** dell'intervallo di tempo selezionato. Le voci di backup eliminate al centro dell'intervallo di tempo selezionato non vengono visualizzate. La stessa convenzione si applica anche ai criteri di backup.

## <a name="query-load-times"></a>Tempi di caricamento delle query

I widget nel Rapporto di backup sono alimentati da query Kusto, che vengono eseguite sulle aree di lavoro la dell'utente. Poiché queste query in genere implicano l'elaborazione di grandi quantità di dati, con più join per consentire informazioni più dettagliate, i widget potrebbero non essere caricati istantaneamente quando l'utente visualizza i report in una proprietà di backup di grandi dimensioni. La tabella seguente fornisce una stima approssimativa del tempo che diversi widget possono richiedere per il caricamento, in base al numero di elementi di backup e all'intervallo di tempo per cui viene visualizzato il report:

| **- Origini dati**                         | **Orizzonte temporale** | **Tempi di caricamento (circa)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| 5 K USD                       | 1 mese          | Piastrelle: 5-10 sec <br> Griglie: 5-10 sec <br> Grafici: 5-10 sec <br> Filtri a livello di report: 5-10 secondiReport-level filters: 5-10 secs|
| 5 K USD                       | 3 mesi          | Piastrelle: 5-10 sec <br> Griglie: 5-10 sec <br> Grafici: 5-10 sec <br> Filtri a livello di report: 5-10 secondiReport-level filters: 5-10 secs|
| 10 K USD                       | 3 mesi          | Piastrelle: 15-20 sec <br> Griglie: 15-20 sec <br> Grafici: 1-2 min <br> Filtri a livello di report: 25-30 secondiReport-level filters: 25-30 secs|
| 15 K USD                       | 1 mese          | Piastrelle: 15-20 sec <br> Griglie: 15-20 sec <br> Grafici: 50-60 sec <br> Filtri a livello di report: 20-25 secondi|
| 15 K USD                       | 3 mesi          | Piastrelle: 20-30 sec <br> Griglie: 20-30 sec <br> Grafici: 2-3 min <br> Filtri a livello di report: 50-60 secondiReport-level filters: 50-60 secs |

## <a name="what-happened-to-the-power-bi-reports"></a>Informazioni sui report di Power BI
* L'app modello di Power BI precedente per la creazione di report (quali dati di origine da un account di archiviazione di Azure) si trova in un percorso di deprecazione. Si consiglia di iniziare a inviare i dati di diagnostica dell'insieme di credenziali a Log Analytics come descritto in precedenza, per visualizzare i report.

* Inoltre, lo schema V1 dell'invio di dati di diagnostica a un account di archiviazione o a un'area di lavoro DI LA si trova anche in un percorso di deprecazione. Ciò significa che se sono state scritte query personalizzate o automazioni basate sullo schema V1, si consiglia di aggiornare queste query per utilizzare lo schema V2 attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul monitoraggio e la creazione di report con Backup di AzureLearn more about monitoring and reporting with Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)