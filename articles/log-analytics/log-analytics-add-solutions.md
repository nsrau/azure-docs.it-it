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


>[AZURE.NOTE] Log Analytics include funzionalità di ricerca nei registri in modo che non sia necessario installare una soluzione per la relativa abilitazione. È tuttavia possibile ottenere visualizzazioni dei dati, ricerche suggerite e informazioni dettagliate aggiungendo soluzioni dalla Raccolta soluzioni.

Dopo aver aggiunto una soluzione, i dati vengono raccolti dai server nell'infrastruttura e inviati al servizio OMS. L’elaborazione da parte del servizio OMS richiede in genere da alcuni minuti a un’ora. Dopo che il servizio ha elaborato i dati, è possibile visualizzarli in OMS.

È possibile rimuovere facilmente una soluzione quando non è più necessaria. Quando si rimuove una soluzione, i relativi dati non vengono inviati a OMS, pertanto la quantità di dati usati per l'eventuale quota giornaliera si riduce.


## Soluzioni supportate da Microsoft Monitoring Agent

I server connessi a OMS tramite Microsoft Monitoring Agent possono attualmente usare la maggior parte delle soluzioni disponibili, tra cui:

- Active Directory Assessment
- Alert Management (senza avvisi SCOM)
- Antimalware
- Change Tracking
- Sicurezza
- SQL Assessment
- System Updates

Le soluzioni seguenti, tuttavia, *non* sono supportate con Microsoft Monitoring Agent e richiedono l'agente System Center Operations Manager (SCOM).

- Alert Management (inclusi gli avvisi SCOM)
- Capacity Management
- Configuration Assessment

Fare riferimento all'articolo relativo alla [connessione di Operations Manager a Log Analytics](log-analytics-om-agents.md) per informazioni su come connettere l'agente SCOM a Log Analytics.

### Per aggiungere una soluzione utilizzando la Raccolta soluzioni

1. Nella pagina di panoramica di OMS, fare clic su sul riquadro **Raccolta soluzioni**. ![raccolta soluzioni](./media/log-analytics-add-solutions/sol-gallery.png)
2. Nella pagina Raccolta soluzioni di OMS, è possibile ottenere informazioni su ogni soluzione disponibile. Fare clic sul nome della soluzione che si desidera aggiungere a OMS.
3. Nella pagina relativa alla soluzione scelta vengono visualizzate informazioni dettagliate sulla soluzione visualizzata. Fare clic su **Aggiungi**.
4. Nella pagina di panoramica di OMS viene visualizzato un nuovo riquadro per la soluzione aggiunta, che è possibile iniziare a usare dopo che il servizio OMS ha elaborato i dati.

## Per configurare le soluzioni
1. È necessario configurare alcune soluzioni. Ad esempio, è necessario configurare Automazione, Azure Site Recovery e Backup prima di poterli usare.
2. Per una di queste soluzioni, fare clic sul relativo riquadro nella pagina di panoramica. ![configurare la soluzione](./media/log-analytics-add-solutions/configure-additional.png)
3. Configurare quindi la soluzione con le dovute informazioni e fare clic su **Salva**. ![configurare la soluzione](./media/log-analytics-add-solutions/configure.png)

### Per rimuovere una soluzione utilizzando la raccolta di soluzioni

1. Nella pagina di panoramica di OMS fare clic sul riquadro **Impostazioni**.
2. Nella pagina Impostazioni, nella scheda Soluzioni fare clic su **Rimuovi** per la soluzione che si intende rimuovere.
3. Nella finestra di conferma fare clic su **Sì** per rimuovere la soluzione.

## Dettagli di raccolta dati per le funzionalità e le soluzioni OMS

La tabella seguente mostra i metodi di raccolta dati e altri dettagli sul modo in cui vengono raccolti i dati per le funzionalità e le soluzioni OMS.

|tipo di dati| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
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

## Soluzioni e funzionalità di anteprima di Log Analytics

Attraverso l’esecuzione di un servizio e seguendo procedure DevOps, Microsoft può collaborare con i clienti per sviluppare funzionalità e soluzioni.

Durante l’anteprima privata, a un piccolo gruppo di clienti viene fornito l’accesso a un’implementazione preliminare della funzionalità o della soluzione allo scopo di ottenere commenti e apportare miglioramenti. Questa implementazione preliminare è dotata di funzionalità e capacità operative minime.

L’obiettivo è eseguire una rapida prova per vedere cosa funziona e cosa non funziona. Questo processo viene iterato più volte fino a quando, sulla base dei commenti dei clienti dell’anteprima privata, non è possibile passare all’anteprima pubblica.

Durante l’anteprima pubblica, la funzionalità o la soluzione viene resa disponibile per tutti gli utenti per ottenere più commenti e convalidarne il ridimensionamento e l’efficienza. In questa fase:

- Le funzionalità di anteprima vengono visualizzate nella scheda Impostazioni e possono essere abilitate da ogni utente
- Le soluzioni di anteprima possono essere aggiunte tramite la raccolta o uno script pubblicato

### Cosa occorre sapere sulle funzionalità e le soluzioni in anteprima

Microsoft è molto interessata alle nuove funzionalità e soluzioni ed è lieta di collaborare al loro sviluppo.

Le soluzioni e le funzionalità di anteprima non sono per tutti. Prima di chiedere di partecipare a un’anteprima privata o di passare a un’anteprima pubblica, è quindi necessario accertarsi di sentirsi a proprio agio nell’affrontare elementi ancora in fase di sviluppo.

Quando si abilita una funzionalità di anteprima tramite il portale, viene visualizzato un avviso che ricorda che si tratta di un’anteprima.

#### Per l’anteprima sia *privata* che *pubblica*

Quanto segue si applica alle anteprime sia private che pubbliche:

- Non sempre tutto potrebbe funzionare correttamente. 
  - I problemi vanno da aspetti di minor rilievo a elementi che non funzionano affatto
- È possibile che l’anteprima generi un impatto negativo sui sistemi o sull’ambiente
  - Microsoft cerca di evitare che accada qualcosa di negativo ai sistemi usati con OMS, ma a volte si verificano imprevisti
- Potrebbe verificarsi una perdita o un danneggiamento dei dati
- È possibile che venga richiesto di raccogliere registri di diagnostica o altri dati per agevolare la risoluzione dei problemi
- La funzionalità o la soluzione potrebbe essere rimossa (in modo temporaneo o definitivo)
  - In base a quanto appreso durante l’anteprima, Microsoft potrebbe decidere di non rilasciare la funzionalità o la soluzione
- Le anteprime potrebbero non funzionare o non essere testate con tutte le configurazioni e Microsoft potrebbe pertanto limitare:
  - I sistemi operativi utilizzabili (ad esempio una funzionalità potrebbe applicarsi solo a Linux in fase di anteprima)
  - Il tipo di agente (MMA, SCOM) utilizzabile (ad esempio una funzionalità potrebbe non funzionare con SCOM in fase di anteprima)  
- Le soluzioni e le funzionalità di anteprima non sono coperte dal Contratto di servizio
- L’uso di funzionalità di anteprima darà luogo ad addebiti
- Funzionalità o capacità necessarie per l’utilità della funzione o della soluzione potrebbero essere assenti o incomplete
- Le funzionalità/soluzioni potrebbero non essere disponibili in tutte le aree
- Le funzionalità/soluzioni potrebbero non essere localizzate
- Le funzionalità/soluzioni potrebbero presentare un limite al numero di clienti o dispositivi che possono usarle
- Potrebbe essere necessario usare script per eseguire la configurazione e per abilitare la soluzione o la funzionalità
- L’interfaccia utente sarà incompleta e potrebbe cambiare di giorno in giorno
- Le anteprime pubbliche potrebbero essere non appropriate per i sistemi di produzione o critici in uso

#### Per l’anteprima *privata*

Oltre alle considerazioni precedenti, per le anteprime private si applica quanto segue:

- Microsoft si aspetta commenti sull’esperienza avuta in modo da poter migliorare la funzionalità/soluzione
- É possibile essere contattati per fornire commenti tramite questionari, chiamate telefoniche e posta elettronica
- Non sempre tutto funziona correttamente
- Potrebbe essere richiesta la sottoscrizione di un accordo di riservatezza per la partecipazione o potrebbe essere incluso contenuto riservato
  - Prima comunicare informazioni a terze parti tramite blog, tweet o in altro modo, verificare con il program manager responsabile dell’anteprima la presenza di eventuali restrizioni alla divulgazione
- Non eseguire l’anteprima su sistemi di produzione o critici


### Come si ottiene l’accesso alle soluzioni e alle funzionalità di anteprima?

Microsoft invita i clienti alle anteprime private in diversi modi a seconda dell'anteprima.

- Rispondendo al sondaggio clienti mensile e autorizzando Microsoft a restare in contatto, aumentano le possibilità di essere invitati a un'anteprima privata.
- I partecipanti possono essere proposti dal team degli account Microsoft.
- È possibile effettuare l'iscrizione in base alle informazioni registrate su twitter [msopsmgmt](https://twitter.com/msopsmgmt) 
- È possibile effettuare l'iscrizione in base alle informazioni condivise in occasione di eventi della community, come raduni, conferenze e community online. 


## Passaggi successivi

- [Eseguire ricerche nei registri](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate raccolte dalle soluzioni.

<!---HONumber=AcomDC_0518_2016-->