<properties
    pageTitle="Da Onboard a Operational Insights in minuti"
    description="Scopri come è possibile impostare Operational Insights in pochi minuti"
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
    ms.topic="get-started-article"
    ms.date="07/09/2015"
    ms.author="banders"/>

# Da Onboard a Operational Insights in minuti


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile ottenere e attivare Operational Insights in pochi minuti. Ci sono due possibilità per scegliere come creare un'area di lavoro di Operational Insights, che è simile a un account:

- Microsoft Operations Management Suite
- iscriversi a Microsoft Azure

È possibile creare un'area di lavoro di Microsoft Operations Management Suite tramite il sito web di Microsoft Operations Management Suite. In alternativa, è possibile iscriversi a Microsoft Azure per creare un'area di lavoro per Operational Insights. Attualmente, entrambe le aree di lavoro sono funzionalmente equivalenti. L'unica differenza tra i due è il nome. Se si utilizza l’iscrizione ad Azure, è possibile utilizzare tale iscrizione anche per accedere ad altri servizi Azure. Indipendentemente dal metodo utilizzato per creare l'area di lavoro, questa verrà creata o con un account Microsoft o con un account aziendale.

## Iscriversi in tre passaggi utilizzando la Operations Management Suite

1. Andare sul sito web di[Microsoft Operations Management Suite](http://microsoft.com/oms)e fare clic su**Prova gratuitamente**. Accedere con l'account Microsoft, ad esempio Outlook.com o con un account aziendale fornito dall'azienda o dall’istituzione accademica con cui è possibile utilizzare Office 365 o altri servizi Microsoft.
2. Dare un nome univoco dell'area di lavoro. Un'area di lavoro è un logical container in cui sono memorizzati i dati di gestione. Esso fornisce un modo per partizionare i dati tra diversi team all'interno della propria organizzazione, in quanto i dati sono esclusivi per l'area di lavoro. Specificare un indirizzo di posta elettronica e l'area in cui si desidera archiviare i dati.![Creare area di lavoro e collegare iscrizioni](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Successivamente, è possibile creare una nuova iscrizione ad Azure o un collegamento a una iscrizione Azure esistente. Se si desidera continuare a utilizzare la versione gratuita, fare clic su**non ora**.

Si è pronti per iniziare a utilizzare il portale di Operations Management Suite.

Ulteriori informazioni sull'impostazione dell'area di lavoro e sul collegamento di account Azure esistenti alle aree di lavoro creati con Operations Management Suite su [impostare l'area di lavoro e le impostazioni di gestione](operational-insights-setup-workspace.md).

## Iscriversi rapidamente utilizzando Microsoft Azure

1. Visitare il[portale di gestione di Azure](https://manage.windowsazure.com)effettuare l’accesso, quindi nell'elenco dei servizi, selezionare**Operational Insights**.![Portale di Azure](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Fare clic su**Crea un'area di lavoro**quindi fare clic su**Creazione rapida**e quindi su Account, digitare un nome dell'area di lavoro, scegliere un livello e quindi scegliere un percorso per archiviare i dati dell'area di lavoro. Se si dispone di più iscrizioni, è possibile scegliere quella da utilizzare e quindi fare clic su**Crea area di lavoro**.![Portale di Azure](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Selezionare l'area di lavoro creata quindi fare clic su**Visita il tuo account Operational Insights**per aprire il sito web di Operations Management Suite.![Visitare account](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. Nel sito web di Operations Management Suite, immettere il proprio indirizzo di posta elettronica e fare clic su**Conferma e continua**. Verrà inviato un messaggio di posta elettronica di conferma all'utente. Aprire il messaggio di posta elettronica e al suo interno fare clic su**confermare ora**.
5. Il sito web di Operations Management Suite consente di visualizzare la pagina Panoramica. Fare clic su**Inizio**per continuare.

Si è pronti per iniziare a utilizzare il portale di Operations Management Suite.

Ulteriori informazioni sull'impostazione dell'area di lavoro e sul collegamento di aree di lavoro già esistenti con le iscrizioni su Operations Management Suite di Azure su [Impostare l'area di lavoro e gestire le impostazioni](operational-insights-setup-workspace.md).

## Iniziare a usare il portale di Operations Management Suite
Per scegliere le soluzioni e collegare i server che si desidera gestire, fare clic sui**Inizio**Riquadri e attenersi alla procedura seguente:

![Creare area di lavoro e collegare iscrizioni](./media/operational-insights-onboard-in-minutes/get-started.png)

- Selezionare le soluzioni che si desidera utilizzare e quindi fare clic su**Aggiungi soluzioni selezionate**.![soluzioni](./media/operational-insights-onboard-in-minutes/solutions.png)
- Scegliere come si desidera connettersi all'ambiente server per raccogliere i dati:
    - Connettere qualsiasi Server di Windows o un client installando direttamente un agente.
    - Utilizzare System Center Operations Manager per collegare i gruppi di gestione o l'intera distribuzione di Operations Manager.
    - Utilizzare un account di archiviazione di Azure configurato con l'estensione VM diagnostica Windows o Linux Azure.
- Configurare almeno un registro per popolare i dati. È possibile selezionare i log IIS e/o aggiungere i registri eventi e selezionare**Salva**nella parte inferiore della pagina. Per i registri eventi, è possibile specificare il tipo di messaggi, inclusi quelli di errore, avvisi e informazioni per il monitoraggio.![soluzioni](./media/operational-insights-onboard-in-minutes/logs.png)

## Facoltativamente, collegare i server direttamente a Operations Management Suite installando un agente
1. Nella visualizzazione iniziale, fare clic su**Collegare un'origine dati**nodo e quindi fare clic su**Scarica agente Windows**. È possibile installare l'agente solo in Windows Server 2008 Service Pack 1 e superiore o in Windows 7 SP1 o superiore. I Server devono disporre di un'architettura x64.
2. Installare l'agente su uno o più server. È possibile installare gli agenti uno alla volta o utilizzando un metodo automatico con [uno script personalizzato](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional)oppure è possibile utilizzare un software di distribuzione già esistente.
3. Accettare il contratto di licenza e si è scelto la cartella di installazione, fare clic su**Connettere l'agente Microsoft Azure Operational Insights**.![Impostazione dell'agente](./media/operational-insights-onboard-in-minutes/agent.png)
4. Nella pagina successiva, viene richiesto l'ID dell'area di lavoro e la chiave dell'area di lavoro. L'ID dell'area di lavoro e la chiave vengono visualizzati sullo schermo in cui è stato scaricato il file dell'agente.![collegare i server](./media/operational-insights-onboard-in-minutes/key.png)
5. Durante l'installazione, è possibile fare clic su**Avanzate**per facoltativamente impostare il server proxy e fornire informazioni di autenticazione. Fare clic su**Avanti**per tornare alla schermata di informazioni dell'area di lavoro.
6. Fare clic su**Avanti**per convalidare l'ID dell'area di lavoro e la chiave. Se vengono rilevati errori, è possibile scegliere**Indietro**per apportare correzioni. Quando l'ID dell'area di lavoro e la chiave vengono convalidati, fare clic su**Installa**per completare l'installazione dell'agente.
7. Accedere al portale di Operations Management Suite e scegliere il riquadro **Impostazioni**nella pagina Panoramica. Quando gli agenti comunicano con il servizio Operations Management Suite, verrà visualizzata un'icona del segno di spunta verde. Inizialmente, questa operazione richiede circa 5-10 minuti.

> [AZURE.NOTE]Le soluzioni di gestione capacità e la configurazione delle valutazione non sono attualmente supportate dai server collegati direttamente al Operations Management Suite.

È inoltre possibile connettere l'agente al System Center Operations Manager 2012 SP1 e versioni successive. A tale scopo, è possibile selezionare**Connettere l'agente al System Center Operations Manager**. Quando viene scelta tale opzione, i dati vengono inviati al servizio senza che sia necessario l’utilizzo di un hardware aggiuntivo o caricarli sui gruppi di gestione.

Per ulteriori informazioni sulla connessione di agenti direttamente a Operations Management Suite in[Collegare il computer direttamente a Operational Insights](operational-insights-direct-agent.md).

## Facoltativamente, è possibile collegare i server utilizzando il System Center Operations Manager

1. Nella console di Operations Manager selezionare **Amministrazione**.
2. Espandere il nodo **Operational Insights**, quindi selezionare **Operational Insights Connection**.
3. Fare click sul link**Registra informazioni operative** in l'alto a destra e seguire le istruzioni illustrate.
4. Dopo il completamento della registrazione guidata, fare clic sul link **Aggiungi computer/gruppo**.
5. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Operational Insights, fare clic su **Aggiungi** e quindi su **OK**. È possibile verificare che il servizio Operational Insights riceva dati accedendo al riquadro **Utilizzo**riquadro nel portale di Operations Management Suite. I Dati dovrebbero essere visualizzati in circa 5-10 minuti.

Per ulteriori informazioni sulla connessione di Operations Manager al gruppo di gestione di operazioni in[Collegare a informazioni operative da System Center Operations Manager](operational-insights-connect-scom.md).

## Facoltativamente, analizzare i dati da servizi cloud di Microsoft Azure

Con Operations Management Suite è possibile eseguire ricerche rapide in log di eventi e IIS per servizi cloud e macchine virtuali abilitando la diagnostica nei servizi cloud di Azure È inoltre possibile ottenere informazioni aggiuntive per le macchine virtuali di Azure mediante l'installazione di Microsoft Monitoring Agent. Per ulteriori informazioni su come configurare l'ambiente Azure per utilizzare Operations Management Suite su[analizzare i dati dai server di Microsoft Azure](operational-insights-analyze-data-azure.md).


## Passaggi successivi
- Iniziare a utilizzare[soluzioni](operational-insights-solutions.md)
- Acquisire familiarità con[ricerca](operational-insights-search.md)
- Utilizzare il[dashboard](operational-insights-use-dashboards.md)per salvare e visualizzare le ricerche personalizzate

<!---HONumber=July15_HO5-->