<properties
	pageTitle="Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni | Microsoft Azure"
	description="Le soluzioni di Log Analytics sono una raccolta di regole per la logica, la visualizzazione e l'acquisizione dei dati che forniscono le metriche specifiche per una particolare area problematica."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni

Le soluzioni di Log Analytics sono una raccolta di regole per la **logica**, la **visualizzazione** e l'**acquisizione dei dati** che forniscono le metriche specifiche per una particolare area problematica. Questo articolo presenta un elenco delle soluzioni supportate da Log Analytics e illustra in che modo aggiungerle e rimuoverle con la Raccolta soluzioni.

Le soluzioni consentono di ottenere più informazioni per:
- individuare e risolvere i problemi operativi più rapidamente
- raccogliere e correlare diversi tipi di dati del computer
- essere proattivi rispetto ad attività come la pianificazione della capacità, la creazione di report sullo stato delle patch e il controllo della sicurezza.


>[AZURE.NOTE] OMS include funzionalità elementari di ricerca nei registri in modo che non sia necessario installare una soluzione per la relativa abilitazione. È tuttavia possibile ottenere altre funzionalità aggiungendovi soluzioni dalla pagina Raccolta soluzioni.

Dopo aver aggiunto una soluzione, i dati vengono raccolti dai server nell'infrastruttura e inviati al servizio OMS. L'elaborazione da parte del servizio OMS può richiedere da alcuni minuti a diverse ore. Dopo che il servizio ha elaborato i dati, è possibile visualizzarli in OMS.

È possibile rimuovere facilmente una soluzione quando non è più necessaria. Quando si rimuove una soluzione, i relativi dati non vengono inviati a OMS, pertanto la quantità di dati usati per l'eventuale quota giornaliera si riduce.


## Soluzioni supportate da Microsoft Monitoring Agent

I server connessi a OMS tramite Microsoft Monitoring Agent possono attualmente usare la maggior parte delle soluzioni disponibili, tra cui:

- System Updates
- Antimalware
- Change Tracking
- SQL Assessment
- Active Directory Assessment
- Alert Management (senza avvisi SCOM)

Le soluzioni seguenti, tuttavia, *non* sono supportate con Microsoft Monitoring Agent e richiedono l'agente System Center Operations Manager (SCOM).

- Capacity Management
- Alert Management (inclusi gli avvisi SCOM)
- Configuration Assessment

Fare riferimento all'articolo relativo alla [connessione di Operations Manager a Log Analytics](log-analytics-om-agents.md) per sapere come connettere l'agente SCOM a Log Analytics.

### Per aggiungere una soluzione utilizzando la Raccolta soluzioni

1. Nella pagina di panoramica di OMS, fare clic su sul riquadro **Raccolta soluzioni**. ![raccolta soluzioni](./media/log-analytics-add-solutions/sol-gallery.png)
2. Nella pagina Raccolta soluzioni di OMS, è possibile ottenere informazioni su ogni soluzione disponibile. Fare clic sul nome della soluzione che si desidera aggiungere a OMS.
3. Nella pagina relativa alla soluzione scelta vengono visualizzate informazioni dettagliate sulla soluzione visualizzata. Fare clic su **Aggiungi**.
4. Nella pagina di panoramica di OMS viene visualizzato un nuovo riquadro per la soluzione aggiunta, che è possibile iniziare a usare dopo che il servizio OMS ha elaborato i dati.

## Per configurare le soluzioni
1. È necessario configurare alcune soluzioni. Ad esempio, è necessario configurare Automazione, Azure Site Recovery e Backup prima di poterli usare.
2. Per una di queste soluzioni, fare clic sul relativo riquadro nella pagina di panoramica. ![configurare la soluzione](./media/log-analytics-add-solutions/configure-additional.png)
3. Quindi, configurare la soluzione con le dovute informazioni e fare clic su **Salva**. ![configurare la soluzione](./media/log-analytics-add-solutions/configure.png)

### Per rimuovere una soluzione utilizzando la raccolta di soluzioni

1. Nella pagina di panoramica di OMS, fare clic sul riquadro **Impostazioni**.
2. Nella pagina Impostazioni, nella scheda Soluzioni, fare clic su **Rimuovi** per la soluzione che si desidera rimuovere.
3. Nella finestra di conferma, fare clic su **Sì** per rimuovere la soluzione.

## Dettagli di raccolta dati per le funzionalità e le soluzioni OMS

La tabella seguente mostra i metodi di raccolta dati e altri dettagli sul modo in cui vengono raccolti i dati per le funzionalità e le soluzioni OMS.

|tipo di dati| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati tramite il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|---|
|AD Assessment|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 giorni|
|Stato della replica di AD|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 giorni|
|Avvisi (Nagios)|Linux|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|all'arrivo|
|Avvisi (Zabbix)|Linux|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Avvisi (Operations Manager)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minuti|
|Antimalware|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| ogni ora|
|Capacity Management|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| ogni ora|
|Change Tracking|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| ogni ora|
|Change Tracking|Linux|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|ogni ora|
|Valutazione della configurazione (Advisor legacy)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| due volte al giorno|
|ETW|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minuti|
|Log di IIS|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minuti|
|Gruppi di sicurezza di rete|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minuti|
|Office 365|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|su notifica|
|Contatori delle prestazioni|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|come pianificato, almeno 10 secondi|
|Contatori delle prestazioni|Linux|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|come pianificato, almeno 10 secondi|
|Service Fabric|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minuti|
|SQL Assessment|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 giorni|
|SurfaceHub|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|all'arrivo|
|Syslog|Linux|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|dall'Archiviazione di Azure: 10 minuti; dall'agente: all'arrivo|
|System Updates|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| almeno 2 volte al giorno e 15 minuti dopo l'installazione di un aggiornamento|
|Registri eventi della sicurezza di Windows|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| per l'Archiviazione di Azure: 10 min.; per l'agente: all'arrivo|
|Registri firewall di Windows|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| all'arrivo|
|Registri eventi di Windows|Windows|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)| per l'Archiviazione di Azure: 1 min.; per l'agente: all'arrivo|
|Trasferimento dati|Windows (2012 R2 / 8.1 o versioni successive)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sì](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| ogni minuto|


## Passaggi successivi

- [Eseguire ricerche nei registri](log-analytics-log-searches.md) per visualizzare le informazioni raccolte dalle soluzioni.

<!---HONumber=AcomDC_0504_2016-->