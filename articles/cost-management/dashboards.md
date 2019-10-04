---
title: Visualizzare le metriche chiave con i dashboard di Cloudyn in Azure | Microsoft Docs
description: Questo articolo descrive come è possibile visualizzare le metriche chiave con i dashboard in Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: b83368b913bf1303b49e3a56e3a15248af222cbe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002059"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Visualizzare le metriche di costo chiave con i dashboard

I dashboard in Cloudyn forniscono una panoramica generale dei report. I dashboard consentono di visualizzare le metriche chiave di costo in una singola visualizzazione. Evidenziano anche gli aspetti più importanti dell'andamento aziendale, per poter prendere importanti decisioni.

I dashboard vengono anche usati per creare visualizzazioni per gli utenti dell'organizzazione con responsabilità diverse, tra cui:

- Controllore finanziario
- Proprietario di un'applicazione o di un progetto
- Tecnico DevOps
- Dirigenti

I dashboard sono costituiti da widget e ogni widget è essenzialmente un'anteprima del report. Fare clic su un widget per aprire il report. Quando si personalizzano i report, li si salva in Report personali e li si aggiunge al dashboard.

Le versioni dei dashboard sono diverse per gli utenti di Cloudyn Management (MSP), Enterprise e Premium. Le differenze sono determinate dai livelli di accesso alle entità. Per altre informazioni sui livello di accesso, vedere [Livelli di accesso alle entità](tutorial-user-access.md#entity-access-levels).

La disponibilità dei dashboard dipende dal tipo di account del provider di servizi cloud che viene usato quando si visualizzano i dashboard. Il tipo di informazioni disponibili e raccolte da Cloudyn influisce sui report nei dashboard. Se ad esempio non si ha un account AWS, il dashboard S3 Tracker (Rilevamento S3) non verrà visualizzato. Analogamente, se non si abilita l'accesso di Azure Resource Manager a Cloudyn, non verranno visualizzate informazioni specifiche di Azure nel widget del dashboard Optimizer.

È possibile usare qualsiasi dashboard preconfezionato o creare il proprio dashboard con report personalizzati. Se non si ha familiarità con i report di Cloudyn, vedere [Usare i report di Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Creare un dashboard personalizzato

Per iniziare rapidamente a usare un dashboard personalizzato, è possibile duplicarne uno esistente per usarne le proprietà. È quindi possibile modificarlo per adeguarlo alle proprie esigenze. Nel dashboard che si vuole copiare fare clic su**Save As** (Salva con nome). È possibile duplicare solo dashboard personalizzati e non i dashboard che sono inclusi in Cloudyn.

Per creare un dashboard personalizzato:

1. Nella home page fare clic su **Add New +** (Aggiungi nuovo +). Verrà visualizzata la pagina My Dashboard (Dashboard).  
    ![Pagina Dashboard in cui si aggiungono nuovi report](./media/dashboards/my-dashboard.png)
2. Fare clic su **Add New Report** (Aggiungi nuovo report). Viene visualizzata la finestra di dialogo Add Report (Aggiungi report).
3. Selezionare il report che si vuole aggiungere al widget del dashboard. Il widget viene aggiunto al dashboard.
4. Ripetere i passaggi precedenti fino al completamento del dashboard.
5. Per modificare il nome del dashboard, fare clic sul nome del dashboard nella home page del dashboard e digitare il nuovo nome.

## <a name="modify-a-custom-dashboard"></a>Modificare un dashboard personalizzato

Come per la creazione di un dashboard personalizzato, non è possibile modificare i dashboard inclusi in Cloudyn. Per modificare un report di un dashboard personalizzato:

1. Nel dashboard trovare il report che si vuole modificare e fare clic su **Edit** (Modifica). Il report viene visualizzato.
2. Apportare le modifiche desiderate al report e fare clic su **Save** (Salva). Il report viene aggiornato e visualizza le modifiche.

## <a name="share-a-custom-dashboard"></a>Condividere un dashboard personalizzato

È possibile condividere un dashboard personalizzato come _Public_ (Pubblico) o _My Entity_ (Entità personale). Quando si sceglie la condivisione pubblica, tutti gli utenti possono visualizzare il dashboard. Solo gli utenti con accesso all'entità corrente possono visualizzare il dashboard quando si sceglie la condivisione di tipo My Entity (Entità personale). I passaggi per condividere un dashboard personalizzato con Public (Pubblico) e My Entity (Entità personale) sono simili.

Per condividere un dashboard personalizzato come pubblico:

1. In un dashboard fare clic su **Dashboard Settings** (Impostazioni del dashboard). Viene visualizzata la finestra di dialogo Dashboard Settings (Impostazioni del dashboard).  
    ![impostazioni del dashboard per un dashboard personalizzato](./media/dashboards/dashboard-options.png)
2. Nella finestra di dialogo Dashboard Settings (Impostazioni del dashboard) fare clic sul simbolo della freccia e quindi su **Public** (Pubblico). Viene visualizzata la finestra di dialogo di conferma Public Dashboard (Dashboard pubblico).
3. Fare clic su **Yes** (Sì). Il dashboard è ora disponibile per gli altri utenti.

## <a name="delete-a-custom-dashboard-report"></a>Eliminare un report di un dashboard personalizzato

È possibile eliminare un componente report personalizzato dal dashboard. L'eliminazione del report dal dashboard non comporta l'eliminazione del report dall'elenco di report. Al contrario, l'eliminazione del report lo rimuove solo dal dashboard solo.

Per eliminare un componente del dashboard, nel componente del dashboard fare clic su **Delete** (Elimina). Facendo clic su **Delete** (Elimina), il componente del dashboard viene immediatamente eliminato.

## <a name="share-a-dashboard-enterprise"></a>Condividere un dashboard (Enterprise)

È possibile condividere i dashboard personalizzati con tutti gli utenti dell'organizzazione o con gli utenti dell'entità corrente. La condivisione di un dashboard può offrire ad altri utenti una rapida panoramica generale dell'indicatore KPI. Quando si condivide un dashboard, il dashboard viene automaticamente replicato per tutte le entità o i clienti di Cloudyn. Le modifiche apportate al dashboard condiviso vengono aggiornate automaticamente.

Per condividere un dashboard con tutti gli utenti, incluse le sottoentità:

1. Nella home page del dashboard fare clic su **Edit** (Modifica).
2. Fare clic su **Share** (Condividi) e quindi su **Public** (Pubblico).
3. Viene visualizzata la finestra di dialogo di conferma Global Public Dashboard (Dashboard pubblico globale).
4. Fare clic su **Yes** (Sì) per impostare il dashboard come dashboard pubblico globale.

Per condividere un dashboard con tutti gli utenti di un'entità corrente:

1. Nella home page del dashboard fare clic su **Edit** (Modifica).
2. Fare clic su **Share** (Condividi) e quindi su **My Entity** (Entità personale).
3. Fare clic su **Yes** (Sì) per impostare il dashboard come dashboard pubblico.

## <a name="duplicate-a-custom-dashboard"></a>Duplicare un dashboard personalizzato

Quando si crea un nuovo dashboard, potrebbe essere necessario usare proprietà simili a quelle di un dashboard esistente. È possibile duplicare il dashboard per crearne uno nuovo.

È possibile duplicare solo dashboard personalizzati. Non è possibile duplicare i dashboard standard.

Per duplicare (clonare) un dashboard personalizzato:

1. Nel dashboard che si vuole duplicare fare clic su **Save As** (Salva con nome). Viene aperto un nuovo dashboard con lo stesso nome e un numero.
2. Rinominare il dashboard duplicato e modificarlo nel modo desiderato.

-Oppure-

1. In Dashboard Settings (Impostazioni dashboard) fare clic su **Save As** (Salva con nome) nella riga del dashboard che si vuole duplicare.
2. Viene aperto il dashboard duplicato.
3. Rinominare il dashboard e modificarlo nel modo desiderato.

## <a name="set-a-default-dashboard"></a>Impostare un dashboard predefinito

È possibile impostare qualsiasi dashboard come predefinito. Impostandolo come predefinito, viene visualizzato come scheda all'estremità sinistra nell'elenco delle schede di dashboard. Quando si apre il portale di Cloudyn, viene visualizzato il dashboard predefinito.

- Fare clic sulla scheda del dashboard che si vuole impostare come predefinito, quindi fare clic su **Default** (Predefinito) a destra.

-Oppure-

1. Fare clic su **Dashboard Settings** (Impostazioni dashboard) per visualizzare l'elenco di dashboard disponibili e selezionare il dashboard che si vuole impostare come predefinito.  
    ![opzioni del dashboard per un dashboard predefinito](./media/dashboards/dashboard-options.png)
2. Fare clic su **Default** (Predefinito) nella riga del dashboard. Viene visualizzata la finestra di dialogo di conferma Default Dashboard (Dashboard predefinito).
3. Scegliere **Sì**. Il dashboard viene impostato come predefinito.

## <a name="management-dashboard"></a>Dashboard di gestione
Il dashboard Management (o dashboard MSP per gli utenti di MSP) include gli elementi di rilievo dei tipi di report principali.  
![Dashboard di gestione che mostra vari report](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Cost Entity Summary (Riepilogo entità di costo) (solo Enterprise)
Questo widget riepiloga le entità di costo gestite, inclusi il numero di entità e il numero di account.
- Fare clic sul widget per aprire il report Enterprise Details (Dettagli azienda).

### <a name="cost-over-time"></a>Cost Over Time (Costo nel tempo)
Questo widget consente di individuare le tendenze di costo. Evidenzia il costo dell'ultimo giorno, in base alla tendenza degli ultimi 30 giorni.
- Fare clic sul widget per aprire il report Actual Cost Over Time (Costo effettivo nel tempo) per visualizzare e filtrare i dettagli aggiuntivi.

### <a name="asset-controller"></a>Asset Controller (Controller degli asset)
Questo widget evidenzia il numero di istanze in esecuzione dal giorno precedente, sopra la tendenza di utilizzo negli ultimi 30 giorni.
- Fare clic sul widget per aprire il dashboard Asset Controller (Controller degli asset).

### <a name="unused-ri-detector"></a>Unused RI Detector (Funzionalità di rilevamento delle istanze riservate inutilizzate)
Questo widget evidenzia il numero di prenotazioni inutilizzate di Amazon EC2.
- Fare clic sul widget per aprire il report Currently Unused Reservations (Prenotazioni attualmente inutilizzate) in cui è possibile visualizzare le prenotazioni inutilizzate che è possibile modificare.

### <a name="cost-by-service"></a>Cost by Service (Costo per servizio)
Questo widget evidenzia i costi ammortizzati per servizio per gli ultimi 30 giorni. Passare il mouse sul grafico a torta per visualizzare i costi per ogni servizio.
- Fare clic sul widget per aprire il report Actual Cost Analysis (Analisi dei costi effettivi).

### <a name="potential-savings"></a>Potential savings (Risparmio potenziale)
Questo widget visualizza le indicazioni sui prezzi dei tipi di istanze per Amazon EC2 e Amazon RDS.
- Fare clic sul widget aprire il report Savings Analysis (Analisi del risparmio). Elenca i costi per tipi di istanze con il risparmio potenziale.

### <a name="compute-instances---daily-trend"></a>Compute Instances - Daily Trend (Istanze di calcolo - Tendenza giornaliera)
Questo widget visualizza le istanze attive per tipo, per gli ultimi 30 giorni.
- Fare clic sul widget per aprire il report Instances Over Time (Istanze nel tempo) in cui è possibile visualizzare una suddivisione di tutte le istanze in esecuzione negli ultimi 30 giorni.

### <a name="storage-by-department"></a>Storage by department (Spazio di archiviazione per reparto)
Questo widget visualizza i servizi di archiviazione usati dai reparti. Passare il puntatore sul grafico a torta per visualizzare il consumo dello spazio di archiviazione per reparto.
- Fare clic sul widget per aprire il dashboard S3 Tracker (Rilevamento S3).

## <a name="cost-controller-dashboard"></a>Dashboard Cost Controller (Controllo dei costi)
Il dashboard Cost Controller (Controllo dei costi) visualizza gli elementi di rilievo di allocazione costi preimpostati.  
![Dashboard Cost Controller (Controllo dei costi) che mostra vari report](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Cost Over Time (Costo nel tempo)
Questo widget consente di individuare le tendenze di costo. Evidenzia il costo dell'ultimo giorno, in base alla tendenza degli ultimi 30 giorni.
- Fare clic sul widget per aprire il report Actual Cost Over Time (Costo effettivo nel tempo) per visualizzare e filtrare i dettagli aggiuntivi.

### <a name="monthly-cost-trends"></a>Monthly Cost Trends (Tendenze costi mensili)
Questo widget evidenzia le spese ammortizzate previste e la spesa effettiva dall'inizio del mese.
- Fare clic sul widget per aprire il report Current Month Projected Cost (Previsione costi mese corrente), che fornisce un riepilogo dei costi da inizio mese.

Questo report mostra il costo dall'inizio del mese, il costo del mese precedente e il costo previsto per il mese corrente. Il costo previsto per il mese corrente viene calcolato sommando il costo mensile aggiornato e la previsione. La previsione è basata sul costo monitorato negli ultimi 30 giorni.

### <a name="12-month-planner"></a>12 Month Planner (Pianificazione 12 mesi)
Questo widget evidenzia i costi previsti per i prossimi 12 mesi e il risparmio potenziale.
- Fare clic sul widget per aprire il report Annual Projected Cost (Previsione costi annuali).

### <a name="cost-by-service"></a>Cost by Service (Costo per servizio)
Questo widget evidenzia i costi ammortizzati per servizio per gli ultimi 30 giorni.
- Passare il mouse sul grafico a torta per visualizzare i costi per ogni servizio.
- Fare clic sul widget per aprire il report Actual Cost Analysis (Analisi dei costi effettivi).

### <a name="cost-by-account"></a>Cost by Account (Costo per account)
Questo widget evidenzia i costi ammortizzati per account per gli ultimi 30 giorni.
- Passare il mouse sul grafico a torta per visualizzare i costi per ogni account.
- Fare clic sul widget per aprire il report Actual Cost Analysis (Analisi dei costi effettivi).

### <a name="cost-trend-by-day"></a>Cost Trend by Day (Tendenza costo per giorno)
Questo widget evidenzia la spesa negli ultimi 30 giorni.
- Passare il puntatore sopra il grafico a barre per visualizzare i costi al giorno.
- Fare clic sul widget per aprire il report Actual Cost Over Time (Costo effettivo nel tempo).

### <a name="cost-trend-by-month---last-6-months"></a>Cost Trend by Month - Last 6 months (Tendenza costi per mese - Ultimi 6 mesi)

Questo widget evidenzia la spesa negli ultimi sei mesi.
- Passare il puntatore sopra il grafico a barre per visualizzare i costi al mese.
- Fare clic sul widget per aprire il report Actual Cost Over Time (Costo effettivo nel tempo).

## <a name="asset-controller-dashboard"></a>Dashboard Asset Controller (Controller degli asset)

Questo dashboard visualizza il numero di istanze in esecuzione, i dischi disponibili e in uso, la distribuzione dei tipi di istanze e le informazioni sull'archiviazione.  
![Dashboard Asset Controller (Controller degli asset) che mostra vari report](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Compute Instances (Istanze di calcolo)
Questo widget visualizza il numero di istanze in esecuzione in base alla tendenza di utilizzo negli ultimi 30 giorni.
- Fare clic sul widget per aprire il report Instances Over Time (Istanze nel tempo).

### <a name="disks"></a>Dischi
Questo widget evidenzia il numero totale e il volume di dischi, in uso e disponibili.
- Fare clic sul widget per aprire il report Active Disks (Dischi attivi).

### <a name="instance-type-distribution"></a>Instance Type Distribution (Distribuzione del tipo di istanza)
Questo widget evidenzia i tipi di istanze in un grafico a torta.
- Fare clic sul widget per aprire il report Instance Distribution (Distribuzione delle istanze), che fornisce una suddivisione delle istanze attive in base all'aggregazione selezionata.

### <a name="compute-instances---daily-trend"></a>Compute Instances - Daily Trend (Istanze di calcolo - Tendenza giornaliera)
Questo widget evidenzia le istanze di calcolo (spot, riservate e on demand) al giorno per gli ultimi 30 giorni.
- Passare il puntatore sul grafico per visualizzare il numero di istanze di calcolo, per tipo al giorno.
- Fare clic sul widget per aprire il report Instances Over Time (Istanze nel tempo).

### <a name="all-buckets-s3"></a>All Buckets (S3) (Tutti i bucket - S3)
Questo widget evidenzia lo spazio di archiviazione S3 totale e il numero di oggetti archiviati.
- Fare clic sul widget per aprire il dashboard S3 Tracker (Rilevamento S3). Il dashboard consente di trovare, analizzare e visualizzare le tendenze e l'utilizzo dello spazio di archiviazione corrente.

### <a name="sql-db-instances-rds"></a>SQL DB Instances (RDS) (Istanze del database SQL - RDS)
Questo widget evidenzia il numero di istanze di Amazon RDS in esecuzione in base alla tendenza degli ultimi 30 giorni.
- Fare clic sul widget per aprire il report RDS Instance Over Time (Istanza RDS nel tempo).

## <a name="optimizer-dashboard"></a>Dashboard Optimizer
Questo dashboard visualizza le indicazioni sulla riduzione delle dimensioni, le risorse inutilizzate e il risparmio potenziale.  
![Dashboard Optimizer che mostra vari report](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI Calculator (Calcolatore istanze riservate)
Questo widget visualizza il numero di consigli per l'acquisto di istanze riservate ed evidenzia il potenziale risparmio annuale.
- Fare clic sul widget per aprire il calcolatore di istanze riservate in cui è possibile determinare quando usare i piani tariffari on demand o riservati.

### <a name="sizing"></a>Ridimensionamento
Questo widget evidenzia il ridimensionamento consigliato e il risparmio potenziale, se implementato.
- Fare clic sul widget per aprire il report EC2 Cost Effective Sizing Recommendations (Consigli per il ridimensionamento effettivo dei costi EC2).

### <a name="unused-ri-detector"></a>Unused RI Detector (Funzionalità di rilevamento delle istanze riservate inutilizzate)
Questo widget evidenzia il numero di prenotazioni inutilizzate di Amazon EC2.
- Fare clic sul widget per aprire il report Currently Unused Reservations (Prenotazioni attualmente inutilizzate) in cui è possibile visualizzare le prenotazioni inutilizzate che è possibile modificare.

###  <a name="available-disks"></a>Available Disks (Dischi disponibili)
Questo widget evidenzia il numero di dischi non collegati nella distribuzione.
- Fare clic sul widget per aprire il report Unattached Disks (Dischi non collegati).

### <a name="rds-ri-calculator"></a>RDS RI Calculator (Calcolatore istanze riservate RDS)
Questo widget evidenzia il numero di consigli per la prenotazione delle istanze di Amazon RDS e il risparmio potenziale.
- Fare clic sul widget per aprire il report RDS RI Buying Recommendations (Consigli per l'acquisto di istanze riservata RDS) in cui è possibile visualizzare i consigli di Cloudyn per usare le istanze riservate invece delle istanze on demand.

### <a name="rds-sizing"></a>RDS Sizing (Ridimensionamento RDS)
Questo widget mostra il numero di consigli sul ridimensionamento e il risparmio potenziale.
- Fare clic sul widget per aprire il report RDS Sizing Recommendations (Consigli per il ridimensionamento RDS), che visualizza consigli dettagliati sul ridimensionamento di Amazon RDS.

I consigli per l'ottimizzazione sono basati sui dati di utilizzo e delle prestazioni monitorati nell'ultimo mese.

## <a name="s3-tracker-dashboard"></a>Dashboard S3 Tracker (Rilevamento S3)
Il dashboard S3 Tracker (Rilevamento S3) consente di trovare, analizzare e visualizzare le tendenze e l'utilizzo dello spazio di archiviazione corrente.  
![Dashboard S3 Tracker (Rilevamento S3) che mostra vari report](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>All Buckets (Tutti i bucket)
Questo widget evidenzia le dimensioni totali di tutti i bucket, espresse in GB, e il numero totale di oggetti nei bucket.
- Fare clic sul widget per aprire il report Distribution of S3 Size (Distribuzione delle dimensioni S3). Il report consente di analizzare le dimensioni S3 per bucket, cartella di primo livello, classe di archiviazione e stato di controllo delle versioni.

### <a name="bucket-properties"></a>Bucket Properties (Proprietà bucket)
Questo widget evidenzia il numero totale di bucket di archiviazione.
- Fare clic sul widget per visualizzare il report S3 Bucket Properties (Proprietà bucket S3).

### <a name="scan-status"></a>Scan Status (Stato analisi)
Questo widget evidenzia quando è stata eseguita l'ultima analisi S3 e quando inizierà quella successiva.
- Fare clic sul widget per aprire il report S3 Scan Status (Stato analisi S3).

### <a name="storage-by-bucket"></a>Storage by Bucket (Spazio di archiviazione per bucket)
Questo widget evidenzia la percentuale usata da ogni classe di archiviazione bucket.
- Fare clic sul widget per aprire il report Distribution of S3 Size (Distribuzione delle dimensioni S3). Il report consente di analizzare le dimensioni S3 per bucket, cartella di primo livello, classe di archiviazione e stato di controllo delle versioni.

### <a name="number-of-objects-by-bucket"></a>Number of Objects by Bucket (Numero di oggetti per bucket)
Questo widget evidenzia il numero di oggetti per bucket nel numero effettivo e la percentuale. Passare il puntatore sul bucket per visualizzare gli oggetti totali.
- Fare clic sul widget per aprire il report Distribution of S3 Size (Distribuzione delle dimensioni S3), basato sull'analisi.

## <a name="cloud-comparison-dashboard"></a>Dashboard Cloud Comparison (Confronto cloud)
Il dashboard Cloud Comparison (Confronto cloud) consente di confrontare i costi di provider cloud diversi in base a prezzi, tipo di CPU e dimensioni della RAM.  
![Dashboard Cloud Comparison (Confronto cloud) che mostra vari report](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Cost in Azure by Instance Type (Costo EC2 in Azure per tipo di istanza)
Questo widget evidenzia gli ultimi 30 giorni di utilizzo nelle tariffe on demand. Confronta il costo corrente di Amazon EC2 rispetto al costo potenziale in Azure.
- Passare il puntatore sulle barre per confrontare i costi per tipo di istanza.
- Fare clic sul widget per aprire il report Porting Your Deployment – Cost Analysis (Portabilità della distribuzione - Analisi dei costi).

### <a name="ec2-cost-in-azure"></a>EC2 Cost in Azure(Costo EC2 in Azure)
Questo widget mostra i costi di Amazon EC2 correnti e li confronta con Azure. Il confronto si basa sugli ultimi 30 giorni di utilizzo nelle tariffe on demand.
- Fare clic sul widget per aprire il report Porting Your Deployment - Cost Analysis (Portabilità della distribuzione - Analisi dei costi).

### <a name="ec2azure-instance-type-mapping"></a>EC2/Azure Instance Type Mapping (Mapping del tipo di istanza EC2/Azure)
Questo widget evidenzia il mapping migliore delle unità di calcolo elastiche tra Amazon EC2 e Azure.
- Fare clic sul widget per aprire il report Instances Type Mapping (Mapping del tipo di istanza).

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui report, leggere l'articolo [Usare i report di Cloudyn](use-reports.md).
