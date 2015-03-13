<properties 
	pageTitle="Guida alle funzionalità di Operational Insights" 
	description="Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Guida alle funzionalità di Operational Insights</h1>

Questa Guida consente di comprendere quali problemi possono essere risolti con l'aiuto di Operational Insights, da cosa è costituito l'ambiente di Operational Insights e come creare un account di Operational Insights per accedere al servizio.

<h2 id="whatisaad">Informazioni su Operational Insights</h2>

Operational Insights è un servizio di analisi che consente agli amministratori IT di ottenere una visione approfondita di ambienti locali e nel cloud. Consente di interagire con i dati dei computer, sia quelli cronologici che quelli in tempo reale, per lo sviluppo rapido di informazioni personalizzate, oltre a fornire modelli sviluppati da Microsoft e dalla community per l'analisi dei dati.

Grazie a Operational Insights, è possibile trasformare i dati dei computer in intelligence operativa. In particolare, è possibile:

- Gestire la capacità dell'infrastruttura server
- Aggiornare i server con gli aggiornamenti del sistema
- Comprendere le relazioni tra dati nei file di log del server
- Proteggere i server con antimalware
- Gestire i rischi di protezione con la garanzia della sicurezza
- Tenere traccia delle modifiche di configurazione dei server
- Ottimizzare SQL Server
- Eseguire ricerche nei log eventi e in quelli di IIS
- Installare gli agenti in macchine virtuali IaaS di Microsoft Azure
- Usare Operational Insights senza Operations Manager  

<h2 id="">Ambiente di Operational Insights</h2>

L'ambiente di Operational Insights è costituito da:

- Aree di lavoro ospitate in Microsoft Azure che rappresentano i contenitori per gli account di Azure
- Il servizio Web di Operational Insights, che è ospitato nel cloud
- Agenti separati che si connettono direttamente al servizio Web, oppure
- Un servizio collegato a System Center Operations Manager, anche se non indispensabile




Se si usava la versione precedente di Operational Insights denominata System Center Advisor, nell'ambiente locale potrebbe essere installato del software Advisor. Tale software non è tuttavia supportato in Operational Insights.

L'uso di Operational Insights come servizio di Operations Manager presuppone l'uso di uno o più gruppi di gestione e di almeno un agente per gruppo di gestione. Gli agenti di Operations Manager raccolgono i dati dai server e li analizzano usando Intelligence Pack (simili ai Management Pack in System Center Operations Manager). I dati analizzati vengono inviati regolarmente al servizio Web di Operational Insights tramite un server proxy, ignorando altri server, in modo che su tali dati non vadano a gravare carichi aggiuntivi.

Analogamente, gli agenti installati nei singoli computer possono connettersi direttamente al servizio Web per inviare i dati raccolti per l'elaborazione.

I dati in ogni Intelligence Pack vengono analizzati e presentati nel portale di Operational Insights. È possibile visualizzare tutti gli avvisi e le relative indicazioni correttive, le valutazioni della configurazione, i problemi di capacità dell'infrastruttura, lo stato degli aggiornamenti del sistema, gli avvisi antimalware e i dati dei log. È anche possibile eseguire ricerche dettagliate.

<h2 id="">Creare un account di Operational Insights ed effettuare l'accesso</h2>

Usare le informazioni riportate di seguito per impostare un account da usare con Microsoft Azure Operational Insights e accedere a Operational Insights.



<h3>Ottenere un account dell'organizzazione o un account Microsoft</h3>

Se si vuole creare un account aziendale, accedere alla pagina relativa agli <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">account Microsoft per le aziende</a>. È quindi necessario concedere a Operational Insights l'accesso all'account aziendale in <a href="http://aka.ms/Mr1dtz" target="_blank">Microsoft Azure Active Directory.</a>.


Se si vuole ottenere un account Microsoft (precedentemente denominato "Windows Live ID"), iscriversi tramite la pagina relativa all' <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">iscrizione per un account Microsoft</a>. Con un account Microsoft, l'accesso a Operational Insights viene concesso automaticamente.


<h3>Accedere a Operational Insights</h3>

1. Passare alla pagina di <a href="preview.opinsights.azure.com" target="_blank">Microsoft Azure Operational Insights</a>  e scegliere l'opzione per l'accesso nella parte superiore della pagina.
2. Scegliere l'opzione preferita tra quella relativa all'**account Microsoft** o all'**account aziendale** e quindi **accedere** con le proprie credenziali.
3. Se richiesto, creare un account di Operational Insights da associare alle informazioni di accesso.
4. Selezionare **Try Preview** quando viene richiesta la selezione della versione di Operational Insights che si vuole usare.
5. Se richiesto, immettere il codice di invito e fare clic su **Apply** per accedere all'anteprima. Se non è disponibile un codice di invito, immettere le informazioni nella parte inferiore dello schermo per ottenerne uno.

<!--HONumber=45--> 
