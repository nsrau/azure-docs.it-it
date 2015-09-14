<properties
    pageTitle="Da Onboard a Operational Insights in minuti | Microsoft Azure"
	description="Scopri come è possibile impostare Azure Operational Insights in pochi minuti"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
    ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/27/2015"
	ms.author="banders"/>

# Da Onboard ad Azure Operational Insights in pochi minuti


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile ottenere e attivare Azure Operational Insights in pochi minuti. Ci sono due possibilità per scegliere come creare un'area di lavoro di Operational Insights, che è simile a un account:

- Microsoft Operations Management Suite
- iscriversi a Microsoft Azure

È possibile creare un'area di lavoro di Operations Management Suite tramite il sito web di Operations Management Suite. In alternativa, è possibile iscriversi a Microsoft Azure per creare un'area di lavoro per Operational Insights. Attualmente, entrambe le aree di lavoro sono funzionalmente equivalenti. L'unica differenza tra i due è il nome. Se si utilizza l’iscrizione ad Azure, è possibile utilizzare tale iscrizione anche per accedere ad altri servizi Azure. Indipendentemente dal metodo utilizzato per creare l'area di lavoro, questa verrà creata o con un account Microsoft o con un account aziendale.

Ecco il processo:

![processo di caricamento](./media/operational-insights-onboard-in-minutes/onboard-oms.png)

## Iscriversi in tre passaggi utilizzando la Operations Management Suite

1. Andare sul sito Web di [Operations Management Suite](http://microsoft.com/oms) e fare clic su **Prova gratuitamente**. Accedere con l'account Microsoft, ad esempio Outlook.com o con un account aziendale fornito dall'azienda o dall’istituzione accademica con cui è possibile utilizzare Office 365 o altri servizi Microsoft.
2. Dare un nome univoco dell'area di lavoro. Un'area di lavoro è un logical container in cui sono memorizzati i dati di gestione. Esso fornisce un modo per partizionare i dati tra diversi team all'interno della propria organizzazione, in quanto i dati sono esclusivi per l'area di lavoro. Specificare un indirizzo di posta elettronica e l'area in cui si desidera archiviare i dati. ![Creare area di lavoro e collegare iscrizioni](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Successivamente, è possibile creare una nuova iscrizione ad Azure o un collegamento a una iscrizione Azure esistente. Se si desidera continuare a usare la versione gratuita, fare clic su **Non adesso**.

Si è pronti per iniziare a utilizzare il portale di Operations Management Suite.

Altre informazioni sull'impostazione dell'area di lavoro e sul collegamento di account Azure esistenti alle aree di lavoro create con Operations Management Suite su [Configurare l'area di lavoro e gestire le impostazioni](operational-insights-setup-workspace.md).

## Iscriversi rapidamente utilizzando Microsoft Azure

1. Visitare il [portale di Azure](https://manage.windowsazure.com) e accedere, quindi nell'elenco dei servizi, selezionare **Operational Insights**. ![Portale di Azure](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Fare clic su **Crea un'area di lavoro** quindi fare clic su **Creazione rapida** e su **Account**, digitare un nome dell'area di lavoro, scegliere un livello e quindi scegliere un percorso per archiviare i dati dell'area di lavoro. Se si dispone di più iscrizioni, è possibile scegliere quella da usare e quindi fare clic su **Crea area di lavoro**. ![Portale di Azure](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Selezionare l'area di lavoro creata e quindi fare clic su **Visitare l'account di Operational Insights** per aprire il sito Web di Operations Management Suite. ![Visitare account](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. Nel sito Web di Operations Management Suite immettere il proprio indirizzo di posta elettronica e fare clic su **Conferma e continua**. Verrà inviato un messaggio di posta elettronica di conferma all'utente. Aprire il messaggio di posta elettronica e al suo interno fare clic su **Conferma ora**.
5. Il sito web di Operations Management Suite consente di visualizzare la pagina Panoramica. Fare clic su **Inizio** per continuare.

Si è pronti per iniziare a utilizzare il portale di Operations Management Suite.

Altre informazioni sull'impostazione dell'area di lavoro e sul collegamento di aree di lavoro esistenti create con Operations Management Suite alle sottoscrizioni di Azure su [Configurare l'area di lavoro e gestire le impostazioni](operational-insights-setup-workspace.md).

## Introduzione al portale di Operations Management Suite
Per scegliere le soluzioni e collegare i server che si desidera gestire, fare clic sul riquadro **Inizio** e seguire i passaggi in questa sezione.

![Creare area di lavoro e collegare iscrizioni](./media/operational-insights-onboard-in-minutes/get-started.png)

- Selezionare le soluzioni che si desidera usare e quindi fare clic su **Aggiungi soluzioni selezionate**. ![soluzioni](./media/operational-insights-onboard-in-minutes/solutions.png)
- Scegliere come si desidera connettersi all'ambiente server per raccogliere i dati:
    - Connettere qualsiasi Server di Windows o un client installando direttamente un agente.
    - Utilizzare System Center Operations Manager per collegare i gruppi di gestione o l'intera distribuzione di Operations Manager.
    - Utilizzare un account di archiviazione di Azure configurato con l'estensione VM diagnostica Windows o Linux Azure.
- Configurare almeno un registro per popolare i dati. È possibile selezionare i log IIS e/o aggiungere i registri eventi e quindi selezionare **Salva** nella parte inferiore della pagina. Per i registri eventi, è possibile specificare il tipo di messaggi, inclusi quelli di errore, avviso e informazione per il monitoraggio. ![soluzioni](./media/operational-insights-onboard-in-minutes/logs.png)

## Facoltativamente, collegare i server direttamente a Operations Management Suite installando un agente
1. Nella visualizzazione iniziale fare clic sul nodo **Collegare un'origine dati** e quindi fare clic su **Scarica agente Windows**. È possibile installare l'agente solo in Windows Server 2008 Service Pack 1 e superiore o in Windows 7 SP1 o superiore. I Server devono disporre di un'architettura x64.
2. Installare l'agente su uno o più server. È possibile installare gli agenti uno alla volta o usando un metodo automatico con uno [script personalizzato](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional) oppure è possibile usare una soluzione di distribuzione software esistente.
3. Accettare il contratto di licenza e scegliere la cartella di installazione, fare clic su **Collegare l'agente a Microsoft Azure Operational Insights**. ![Impostazione dell'agente](./media/operational-insights-onboard-in-minutes/agent.png)
4. Nella pagina successiva, viene richiesto l'ID dell'area di lavoro e la chiave dell'area di lavoro. La chiave e l'ID dell'area di lavoro vengono visualizzati sullo schermo in cui è stato scaricato il file dell'agente. ![collegare i server](./media/operational-insights-onboard-in-minutes/key.png)
5. Durante l'installazione, è possibile fare clic su **Avanzate** per impostare facoltativamente il server proxy e fornire informazioni di autenticazione. Fare clic su **Avanti** per tornare alla schermata di informazioni dell'area di lavoro.
6. Fare clic su **Avanti** per convalidare la chiave e l'ID dell'area di lavoro. Se vengono rilevati errori, è possibile scegliere **Indietro** per apportare correzioni. Quando la chiave e l'ID dell'area di lavoro vengono convalidati, fare clic su **Installa** per completare l'installazione dell'agente.
7. Accedere al portale di Operations Management Suite e scegliere il riquadro **Impostazioni** nella pagina Panoramica. Quando gli agenti comunicano con il servizio Operations Management Suite, verrà visualizzata un'icona del segno di spunta verde. Inizialmente, questa operazione richiede circa 5-10 minuti.

> [AZURE.NOTE]Le soluzioni di gestione capacità e la configurazione delle valutazione non sono attualmente supportate dai server collegati direttamente al Operations Management Suite.

È inoltre possibile connettere l'agente al System Center Operations Manager 2012 SP1 e versioni successive. A tale scopo, selezionare **Collegare l'agente a System Center Operations Manager**. Quando viene scelta tale opzione, i dati vengono inviati al servizio senza che sia necessario l’utilizzo di un hardware aggiuntivo o caricarli sui gruppi di gestione.

Altre informazioni sulla connessione di agenti direttamente a Operations Management Suite sono disponibili in [Connettere i computer direttamente a Operational Insights](operational-insights-direct-agent.md).

## Facoltativamente, è possibile collegare i server utilizzando il System Center Operations Manager

1. Nella console di Operations Manager selezionare **Amministrazione**.
2. Espandere il nodo **Operational Insights** e selezionare **Connessione a Operational Insights**.
3. Fare clic sul collegamento **Registra con Operational Insights** in alto a destra e seguire le istruzioni.
4. Dopo il completamento della registrazione guidata, fare clic sul collegamento **Aggiungi computer/gruppo**.
5. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Operational Insights, fare clic su **Aggiungi** e quindi su **OK**. È possibile verificare che il servizio Operational Insights riceva dati accedendo al riquadro **Utilizzo** nel portale di Operations Management Suite. I Dati dovrebbero essere visualizzati in circa 5-10 minuti.

Altre informazioni sulla connessione di Operations Manager a Operations Management Suite sono disponibili in [Connettersi a Operational Insights da System Center Operations Manager](operational-insights-connect-scom.md).

## Facoltativamente, analizzare i dati da servizi cloud di Microsoft Azure

Con Operations Management Suite è possibile eseguire ricerche rapide in log di eventi e IIS per servizi cloud e macchine virtuali abilitando la diagnostica nei servizi cloud di Azure È inoltre possibile ottenere informazioni aggiuntive per le macchine virtuali di Azure mediante l'installazione di Microsoft Monitoring Agent. Altre informazioni su come configurare l'ambiente Azure per usare Operations Management Suite sono disponibili in [Analizzare i dati dai server in Microsoft Azure](operational-insights-analyze-data-azure.md).


## Passaggi successivi
- Iniziare a usare le [soluzioni](operational-insights-solutions.md).
- Acquisire familiarità con la [ricerca](operational-insights-search.md).
- Usare i [dashboard](operational-insights-use-dashboards.md) per salvare e visualizzare le ricerche personalizzate.

<!---HONumber=September15_HO1-->