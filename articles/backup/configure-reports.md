---
title: Configurare report di Backup di Azure
description: Configurare e visualizzare i report per backup di Azure con Log Analytics e cartelle di lavoro di Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161202"
---
# <a name="configure-azure-backup-reports"></a>Configurare report di Backup di Azure

Un requisito comune per gli amministratori di backup è ottenere informazioni dettagliate sui backup, in base ai dati che si estendono lungo un lungo periodo di tempo. Potrebbero essere presenti più casi d'uso per una soluzione di questo tipo, ovvero l'allocazione e la previsione di memoria cloud utilizzata, il controllo di backup e ripristini e l'identificazione delle tendenze principali a diversi livelli di granularità.

Il servizio backup di Azure offre attualmente una soluzione per la creazione di report che sfrutta i [log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e le cartelle di lavoro di [Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), consentendo di ottenere informazioni dettagliate sui backup nell'intero patrimonio di backup. Questo articolo illustra come configurare e visualizzare i report di backup.

## <a name="supported-scenarios"></a>Scenari supportati

* I report di backup sono supportati per le macchine virtuali di Azure, SQL in macchine virtuali di Azure, SAP HANA/ASE nelle VM di Azure, l'agente di backup di Azure (MARS), server di Backup di Azure (MAB) e System Center DPM.
* Per i carichi di lavoro DPM, i report di backup sono supportati per DPM versione 5.1.363.0 e successive e per l'agente 2.0.9127.0 e versioni successive.
* Per i carichi di lavoro di MAB, i report di backup sono supportati per la versione 13.0.415.0 e successive e per l'agente 2.0.9170.0 e versioni successive.
* I report di backup possono essere visualizzati in tutti gli elementi di backup, gli insiemi di credenziali, le sottoscrizioni e le aree a condizione che i dati vengano inviati a un'area di lavoro di Log Analytics (LA) a cui l'utente ha accesso. 
* Se si è un utente di [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni dei clienti, è possibile usare questi report con Azure Lighthouse per visualizzare i report in tutti i tenant.
* I dati per i processi di backup del log non sono attualmente visualizzati nei report.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare i report, seguire i tre passaggi descritti di seguito:

1. **Creare un'area di lavoro di Log Analytics (LA) (o utilizzarne una esistente):**

Per archiviare i dati dei report di backup, è necessario configurare una o più aree di lavoro LA. Il percorso e la sottoscrizione in cui è possibile creare l'area di lavoro di è indipendente dalla posizione e dalla sottoscrizione in cui si trovano gli insiemi di credenziali. 

Vedere l'articolo seguente: [creare un'area di lavoro di log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) per configurare un'area di lavoro di la.

Per impostazione predefinita, i dati in un'area di lavoro LA vengono conservati per 30 giorni. Per visualizzare i dati per un orizzonte temporale più lungo, modificare il periodo di conservazione dell'area di lavoro. Per modificare il periodo di memorizzazione, vedere l'articolo relativo alla [gestione dell'utilizzo e dei costi con i log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Configurare le impostazioni di diagnostica per gli insiemi di credenziali:**

Azure Resource Manager risorse, ad esempio gli insiemi di credenziali dei servizi di ripristino, registrare le informazioni sulle operazioni pianificate e le operazioni attivate dall'utente come dati di diagnostica. 

Nella sezione monitoraggio dell'insieme di credenziali di servizi di ripristino selezionare **impostazioni di diagnostica** e specificare la destinazione per i dati di diagnostica dell'insieme di credenziali di servizi di ripristino. [Altre informazioni sull'uso degli eventi di diagnostica](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Pannello impostazioni di diagnostica](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Backup di Azure fornisce anche un criterio predefinito di Azure, che consente di automatizzare la configurazione delle impostazioni di diagnostica per tutti gli insiemi di credenziali in un ambito specifico. Vedere l'articolo seguente per informazioni su come usare questo criterio: [configurare le impostazioni di diagnostica dell'insieme di credenziali su larga scala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> Una volta configurata la diagnostica, potrebbero essere necessarie fino a 24 ore per il completamento del push di dati iniziale. Una volta che i dati iniziano a scorrere nell'area di lavoro di LA, potrebbe non essere possibile visualizzare immediatamente i dati nei report, poiché i dati per il giorno parziale corrente non vengono visualizzati nei report (altri dettagli [qui](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)). È quindi consigliabile iniziare a visualizzare i report 2 giorni dopo la configurazione degli insiemi di credenziali per l'invio di dati a Log Analytics.

3. **Visualizzare i report nel portale di Azure:**

Dopo aver configurato gli insiemi di credenziali per l'invio di dati a LA, visualizzare i report di backup passando al pannello qualsiasi insieme di credenziali e facendo clic sulla voce di menu **backup Reports** . 

![Dashboard dell'insieme di credenziali](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Facendo clic su questo collegamento viene aperta la cartella di lavoro report di backup.

> [!NOTE]
> * Attualmente, il carico iniziale del report può richiedere fino a 1 minuto.
> * L'insieme di credenziali di servizi di ripristino è semplicemente un punto di ingresso per i report di backup. Una volta aperta la cartella di lavoro report di backup dal pannello di un insieme di credenziali, sarà possibile visualizzare i dati aggregati in tutti gli insiemi di credenziali (selezionando il set appropriato di aree di lavoro di LA).

Di seguito è riportata una descrizione delle varie schede incluse nel report:

* **Riepilogo** : la scheda riepilogo fornisce una panoramica di alto livello della proprietà di backup. Nella scheda Riepilogo è possibile ottenere un rapido riepilogo del numero totale di elementi di backup, dell'archiviazione cloud totale utilizzata, del numero di istanze protette e della percentuale di esecuzioni riuscite del processo per tipo di carico di lavoro. Per informazioni più dettagliate su un tipo di artefatto di backup specifico, passare alle rispettive schede.

![Scheda Riepilogo](./media/backup-azure-configure-backup-reports/summary.png)

* **Elementi di backup** : la scheda elementi di backup consente di visualizzare le informazioni e le tendenze sull'archiviazione cloud usata a livello di elemento di backup. Ad esempio, se si usa SQL nel backup di macchine virtuali di Azure, è possibile visualizzare la memoria cloud usata per ogni database SQL di cui si esegue il backup. È anche possibile scegliere di visualizzare i dati per gli elementi di backup di uno specifico stato di protezione. Ad esempio, facendo clic sul riquadro **protezione arrestata** nella parte superiore della scheda, vengono filtrati tutti i widget seguenti per visualizzare solo i dati per gli elementi di backup in stato di protezione arrestata.

![Scheda elementi di backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilizzo** : la scheda utilizzo consente di visualizzare i parametri di fatturazione principali per i backup. Le informazioni visualizzate in questa scheda sono a livello di entità fatturazione (contenitore protetto). Ad esempio, nel caso di un server DPM di cui viene eseguito il backup in Azure, è possibile visualizzare la tendenza delle istanze protette e dello spazio di archiviazione cloud utilizzato per il server DPM. Analogamente, se si usa SQL in backup di Azure o SAP HANA in backup di Azure, questa scheda fornisce informazioni relative all'utilizzo a livello della macchina virtuale in cui sono contenuti questi database.

![Scheda utilizzo](./media/backup-azure-configure-backup-reports/usage.png)

* **Processi** : la scheda processi consente di visualizzare le tendenze a esecuzione prolungata nei processi, ad esempio il numero di processi non riusciti al giorno e le cause principali dell'errore del processo. È possibile visualizzare queste informazioni a livello di aggregazione e a livello di elemento di backup. Quando si fa clic su un particolare elemento di backup in una griglia, è possibile visualizzare informazioni dettagliate su ogni processo attivato su tale elemento di backup nell'intervallo di tempo selezionato.

![Scheda Processi](./media/backup-azure-configure-backup-reports/jobs.png)

* **Criteri** : la scheda criteri consente di visualizzare le informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati, e l'archiviazione cloud totale utilizzata dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. Se si fa clic su un determinato criterio, è possibile visualizzare le informazioni su ognuno degli elementi di backup associati.

![Scheda criteri](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Esportazione in Excel

Facendo clic sul pulsante freccia giù nella parte superiore destra di qualsiasi widget (tabella/grafico), il contenuto del widget viene esportato come foglio di Excel, così come è con i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina utilizzando l'elenco a discesa **righe per pagina** nella parte superiore di ogni griglia.

## <a name="pinning-to-dashboard"></a>Aggiunta al dashboard

Fare clic sull'icona Aggiungi nella parte superiore di ogni widget per aggiungere il widget al dashboard portale di Azure. Questo consente di creare dashboard personalizzati personalizzati per visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-reports"></a>Report tra tenant

Se si è un utente di [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro della sottoscrizione predefinito (facendo clic sull'icona del filtro in alto a destra della portale di Azure) per scegliere tutte le sottoscrizioni per cui si vogliono visualizzare i dati. In questo modo sarà possibile selezionare le aree di lavoro tra i tenant per visualizzare i report multi-tenant.

## <a name="conventions-used-in-backup-reports"></a>Convenzioni utilizzate nei report di backup

* Filtra il lavoro da sinistra verso destra e dall'alto verso il basso in ogni scheda, ovvero qualsiasi filtro si applica solo a tutti i widget posizionati a destra del filtro o sotto tale filtro. 
* Se si fa clic su un riquadro colorato, i widget sotto il riquadro vengono filtrati per i record relativi al valore di tale riquadro. Se ad esempio si fa clic sul riquadro *protezione arrestata* nella scheda elementi di backup, i grafici riportati di seguito vengono filtrati per visualizzare i dati per gli elementi di backup nello stato "protezione arrestata".
* I riquadri non colorati non sono selezionabili.
* I dati relativi alla giornata odierna parziale non vengono mostrati nei report. Pertanto, quando il valore selezionato dell' **intervallo di tempo** è, negli ***ultimi 7 giorni***, il report Mostra i record per gli ultimi 7 giorni completati (che non include il giorno corrente).
* Il report Mostra i dettagli dei processi (oltre ai processi di log) **attivati** nell'intervallo di tempo selezionato. 
* I valori visualizzati per l'archiviazione cloud e le istanze protette si trovano alla **fine** dell'intervallo di tempo selezionato.
* Gli elementi di backup visualizzati nei report sono quelli presenti alla **fine** dell'intervallo di tempo selezionato. Gli elementi di backup eliminati a metà dell'intervallo di tempo selezionato non vengono visualizzati. La stessa convenzione è valida anche per i criteri di backup.

## <a name="query-load-times"></a>Tempi di caricamento query

I widget del report di backup sono basati sulle query kusto, che vengono eseguite nelle aree di lavoro dell'utente. Poiché queste query comportano in genere l'elaborazione di grandi quantità di dati, con più join che consentono di ottenere informazioni più dettagliate, i widget potrebbero non essere caricati immediatamente quando l'utente Visualizza i report in una proprietà di backup di grandi dimensioni. Nella tabella seguente viene fornita una stima approssimativa del tempo che i diversi widget possono richiedere per il caricamento, in base al numero di elementi di backup e all'intervallo di tempo per cui il report viene visualizzato:

| **N. origini dati**                         | **Orizzonte temporale** | **Tempi di caricamento (circa)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 mese          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~ 5 K                       | 3 mesi          | Riquadri: 5-10 secondi <br> Griglie: 5-10 secondi <br> Grafici: 5-10 secondi <br> Filtri a livello di report: 5-10 secondi|
| ~ 10 K                       | 3 mesi          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 1-2 minuti <br> Filtri a livello di report: 25-30 secondi|
| ~ 15 K                       | 1 mese          | Riquadri: 15-20 secondi <br> Griglie: 15-20 secondi <br> Grafici: 50-60 secondi <br> Filtri a livello di report: 20-25 secondi|
| ~ 15 K                       | 3 mesi          | Riquadri: 20-30 secondi <br> Griglie: 20-30 secondi <br> Grafici: 2-3 minuti <br> Filtri a livello di report: 50-60 secondi |

## <a name="what-happened-to-the-power-bi-reports"></a>Che cosa è successo ai report Power BI?
* L'app modello di Power BI precedente per la creazione di report (i dati originati da un account di archiviazione di Azure) si trova in un percorso di deprecazione. È consigliabile iniziare a inviare i dati di diagnostica dell'insieme di credenziali a Log Analytics come descritto in precedenza, per visualizzare i report.

* Inoltre, lo schema V1 dell'invio dei dati di diagnostica a un account di archiviazione o a un'area di lavoro LA è in un percorso di deprecazione. Ciò significa che se sono state scritte query o automazione personalizzate basate sullo schema v1, si consiglia di aggiornare le query in modo che utilizzino lo schema V2 attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul monitoraggio e la creazione di report con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)