<properties
    pageTitle="Panoramica delle metriche in Microsoft Azure | Microsoft Azure"
    description="Panoramica delle metriche e dei casi d'uso in Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>


# <a name="overview-of-metrics-in-microsoft-azure"></a>Panoramica delle metriche in Microsoft Azure 

L'articolo descrive le metriche disponibili in Microsoft Azure, i loro vantaggi e come iniziare a usarle.  

## <a name="what-are-metrics?"></a>Cosa sono le metriche?

Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il monitoraggio di Azure offre alcuni modi per configurare e usare queste metriche per il monitoraggio e la risoluzione dei problemi.


## <a name="what-can-you-do-with-metrics?"></a>A cosa servono le metriche?

Le metriche sono un'importante fonte di dati di telemetria e consentono di eseguire le operazioni seguenti:

- **Monitorare le prestazioni** della risorsa (ad esempio, una VM, un sito Web o un'app per la logica) tracciandone le metriche in un grafico del portale da aggiungere a un dashboard.
- **Ricevere la notifica di un problema** che influenza le prestazioni della risorsa quando una metrica supera una determinata soglia.
- **Configurare le azioni automatiche**, ad esempio la scalabilità automatica di una risorsa o la generazione di un runbook quando una metrica supera una determinata soglia.
- **Eseguire analisi avanzate** o generare report relativi alle tendenze delle prestazioni o di uso delle risorse.
- **Archiviare** la cronologia relativa alle prestazioni o all'integrità della risorsa a scopi di **conformità/verifica**.

##  <a name="metric-characteristics"></a>Caratteristiche delle metriche
Le metriche presentano le caratteristiche seguenti:

- Tutte le metriche hanno **la frequenza di 1 minuto**. L'utente riceve il valore della metrica generata dalla risorsa ogni minuto, così da ottenere una visibilità quasi in tempo reale sullo stato e sull'integrità della risorsa.
- Le metriche sono **subito disponibili senza dover fornire il consenso esplicito** o configurare ulteriori attività di diagnostica.
- Per ogni metrica è possibile accedere alla **cronologia degli ultimi 30 giorni** . È possibile vedere rapidamente le tendenze recenti e mensili riguardo alle prestazioni o all'integrità della risorsa.

È possibile:

- Rilevare, accedere e **visualizzare facilmente tutte le metriche** tramite il portale di Azure quando si seleziona una risorsa e la si traccia in un grafico. 
- Configurare una **regola di avviso per la metrica che invia una notifica o esegue un'azione automatica** quando la metrica supera la soglia definita. La scalabilità automatica è un'azione speciale automatica che consente di scalare orizzontalmente la risorsa per soddisfare le richieste in entrata o il carico sul sito web o sulle risorse di calcolo. È possibile configurare una regola di impostazione della scalabilità automatica per scalare orizzontalmente e verticalmente in base a una metrica che supera una soglia.
- **Archiviare** le metriche per un lungo periodo o usarle per creare report offline. È possibile reindirizzare le metriche all'archivio BLOB quando si configurano le impostazioni di diagnostica per la risorsa.
- **Trasmettere** le metriche a un hub eventi, per poi avere la possibilità di reindirizzarle ad Analisi di flusso di Azure o alle app personalizzate per effettuare un'analisi quasi in tempo reale. È possibile farlo usando le impostazioni di diagnostica.
- **Reindirizzare** tutte le metriche a Log Analytics di OMS per sbloccare le analisi istantanee, la funzione di ricerca e gli avvisi personalizzati sui dati delle metriche provenienti dalle risorse.
- **Usare** le metriche mediante le nuove API REST del monitoraggio di Azure.
- **Eseguire query** delle metriche usando i cmdlet di PowerShell o l'API REST multi-piattaforma.

 ![Reindirizzamento delle metriche nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Accedere alle metriche dal portale
Ecco una breve procedura dettagliata per creare un grafico delle metriche attraverso il portale di Azure

### <a name="view-metrics-after-creating-a-resource"></a>Visualizzare le metriche dopo aver creato una risorsa
1. Aprire il portale di Azure
2. Creare un nuovo servizio app - Sito Web.
3. Dopo aver creato un sito Web, accedere al pannello Panoramica del sito Web.
4. È possibile visualizzare nuove metriche come un riquadro "Monitoraggio". È possibile modificare il riquadro e selezionare altre metriche

 ![Metriche su una risorsa nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Accedere a tutte le metriche in un'unica posizione
1. Aprire il portale di Azure 
2. Accedere alla scheda Monitoraggio e selezionare l'opzione Metriche 
3. Dall'elenco a discesa è possibile selezionare la sottoscrizione, il gruppo di risorse e il nome della risorsa. 
4. È ora possibile visualizzare l'elenco delle metriche disponibili. 
5. Selezionare la metrica desiderata e tracciarla. 
6. È possibile aggiungerla al dashboard facendo clic sul pin nell'angolo superiore destro.

 ![Accedere a tutte le metriche in un'unica posizione nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] È possibile accedere alle metriche a livello di host dalle VM (basate su Azure Resource Manager) e dai set di scalabilità delle VM senza altre eventuali configurazioni di diagnostica. Queste nuove metriche a livello di host sono disponibili per le istanze di Windows e Linux. Queste metriche non vanno confuse con le metriche a livello di sistema operativo guest a cui si accede quando si attiva la diagnostica di Azure sulle VM o sui set di scalabilità delle VM. Per ulteriori informazioni sulla configurazione della diagnostica di Azure, vedere [Che cos'è la diagnostica di Microsoft Azure?](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Accedere alle metriche mediante l'API REST
Le metriche di Azure sono accessibili tramite le API del monitoraggio di Azure. Sono due le API che consentono di rilevare le metriche e accedervi. Usare: 

- [API REST relativa alle definizioni delle metriche del monitoraggio di Azure](https://msdn.microsoft.com/library/mt743621.aspx) per accedere all'elenco delle metriche disponibili per un servizio.
- [API REST delle metriche del monitoraggio di Azure](https://msdn.microsoft.com/library/mt743622.aspx) per accedere ai dati delle metriche effettive

>[AZURE.NOTE] Questo articolo è dedicato alle metriche accessibili tramite la [nuova API per le metriche](https://msdn.microsoft.com/library/dn931930.aspx) per le risorse di Azure. La versione della nuova API per le definizioni delle metriche è 2016-03-01 e la versione dell'API per le metriche è 2016-09-01. Le metriche e le definizioni delle metriche legacy sono accessibili con la versione API 2014-04-01.

Per una procedura ancor più dettagliata con le API REST del monitoraggio di Azure, vedere [Procedura dettagliata relativa alle API REST del monitoraggio di Azure](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opzioni di esportazione delle metriche
È possibile accedere al pannello Log di diagnostica nella scheda Monitoraggio e visualizzare le opzioni di esportazione delle metriche. È possibile selezionare le metriche (e i log di diagnostica) da reindirizzare nell'archivio BLOB, negli hub eventi o in Log Analytics di OMS per i casi di uso descritti in precedenza in questo articolo. 

 ![Opzioni di esportazione per le metriche nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

È configurabile tramite i modelli di Resource Manager, [PowerShell](insights-powershell-samples.md), l'[interfaccia della riga di comando](insights-cli-samples.md) o l'[API REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Eseguire operazioni sulle metriche
È possibile ricevere le notifiche o eseguire operazioni automatiche su dati delle metriche. A tal fine, è possibile configurare le regole di avviso o le impostazioni di scalabilità automatica.

### <a name="alert-rules"></a>Regole di avviso
È possibile configurare le regole di avviso sulle metriche. Queste regole di avviso permettono di controllare se una metrica ha superato una determinata soglia e inviare all'utente una notifica tramite posta elettronica o generare un webhook con cui eseguire qualsiasi script personalizzato. È anche possibile usare il webhook per configurare le integrazioni dei prodotti di terze parti.

 ![Metriche e regole di avviso nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
Alcune risorse di Azure supportano la scalabilità orizzontale o verticale di più istanze per gestire i carichi di lavoro. La scalabilità automatica si applica ai servizi app (app Web), ai set di scalabilità delle macchine virtuali (VMSS) e ai servizi Cloud classici. È possibile configurare le regole di scalabilità automatica per una scalabilità orizzontale o verticale quando una data metrica che influisce sul carico di lavoro supera una soglia specificata. Per ulteriori informazioni, vedere [Panoramica della scalabilità automatica](monitoring-overview-autoscale.md).

 ![Metriche e scalabilità automatica nel monitoraggio di Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Metriche e servizi supportati
Il monitoraggio di Azure è una nuova infrastruttura di metriche. Fornisce supporto per i seguenti servizi di Azure nel portale di Azure e la nuova versione dell'API del monitoraggio di Azure:

- VM (basate su Azure Resource Manager)
- Set di scalabilità delle VM
- Batch
- Spazio dei nomi dell'hub eventi 
- Spazio dei nomi del bus di servizio (solo SKU premium)
- SQL (versione 12)
- Pool SQL elastico
- Microsoft Azure
- Server farm Web
- App per la logica
- Hub IoT
- Cache Redis
- Rete: gateway di applicazione
- Search

È possibile visualizzare un elenco dettagliato di tutti i servizi supportati e delle relative metriche in [Metriche del monitoraggio di Azure: metriche supportate per tipo di risorsa](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Passaggi successivi

Fare riferimento ai collegamenti forniti all'interno dell'articolo. In più, ottenere informazioni su:  

- [metriche comuni per la scalabilità automatica](insights-autoscale-common-metrics.md)
- [come creare le regole di avviso](insights-alerts-portal.md)







<!--HONumber=Oct16_HO2-->


