<properties
	pageTitle="Informazioni su Automazione di Azure"
	description="Informazioni sui vantaggi offerti da Automazione di Azure e risposte alle domande più comuni sul modo in cui è possibile iniziare a creare e utilizzare i Runbook."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/06/2015"
	ms.author="bwren"/>

# Informazioni su Automazione di Azure

Automazione di Microsoft Azure offre agli utenti la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud e aziendali. In questo articolo vengono fornite brevi risposte alle domande comuni su Automazione di Azure. È possibile fare riferimento ad altri articoli in questa raccolta per informazioni più dettagliate su argomenti diversi.

## Che vantaggi offre Automazione di Azure?

Automazione di Azure consente di risparmiare tempo e aumenta l'affidabilità delle normali attività amministrative eseguite in ambienti aziendali e cloud. È possibile implementare questi processi come Runbook, che permettono di eseguire più attività senza intervento umano e persino programmarle per essere eseguite automaticamente a intervalli regolari.

## Cos'è un Runbook?

Un Runbook è un set di attività che eseguono un processo automatico in Automazione di Azure. È un processo semplice, ad esempio l'avvio di una macchina virtuale o la creazione di una voce di log o potrebbe essere un Runbook complesso che combina altri Runbook per eseguire un processo complesso tra più risorse o anche più aree più piccole.

Ad esempio, potrebbe essere un processo manuale esistente per il provisioning di una nuova macchina virtuale che include più passaggi, ad esempio creazione, connessione a una rete, assegnazione di un indirizzo IP e notifica a un utente in merito a quando è pronta. Invece di eseguire manualmente ognuno di questi passaggi, è possibile creare un Runbook che eseguirà tutte queste attività come un singolo processo. È sufficiente avviare il Runbook, fornire le informazioni necessarie, ad esempio il nome della macchina virtuale, l'indirizzo IP e di posta elettronica dei destinatari, quindi attendere il completamento del processo senza alcun intervento


## Cosa consentono di automatizzare i Runbook?

I Runbook di Automazione di Azure si basano sul flusso di lavoro PowerShell, pertanto consentono di effettuare tutto ciò che è possibile effettuare in PowerShell. Se un'applicazione o un servizio dispongono di un'API, un Runbook permette di utilizzarli. Se si dispone del relativo modulo di PowerShell, sarà possibile caricarlo in Automazione di Azure e includere i cmdlet nel Runbook. I Runbook di automazione Azure vengono eseguiti nel cloud di Azure in modo che possano accedere a tutte le risorse interne al cloud o esterne a cui è possibile accedere dal cloud. Mediante [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) i Runbook possono essere eseguiti nel data center locale per la gestione delle risorse locali.


## Dove ottenere i Runbook

La [Raccolta di Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) contiene Runbook di Microsoft e della community che è possibile utilizzare senza modifiche nell'ambiente in uso oppure personalizzarli per le proprie esigenze. Sono inoltre utili come riferimenti per imparare a creare Runbook personalizzati. Nella raccolta è inoltre possibile inserire i propri Runbook personalizzati che si ritiene possano rivelarsi utili per gli altri utenti.


## Come si creano Runbook personalizzati

È possibile [creare Runbook personalizzati](http://msdn.microsoft.com/library/azure/dn643637.aspx) completamente nuovi o modificare i Runbook contenuti nella relativa [Raccolta](http://msdn.microsoft.com/library/azure/dn781422.aspx) in base alle proprie esigenze. Se si preferisce utilizzare direttamente il codice di PowerShell, è possibile[ modificare il Runbook mediante l'editor di testo](http://msdn.microsoft.com/library/azure/dn879137.aspx) presente nel portale di Azure o modificarlo in modalità offline. Se si preferisce modificare un Runbook evitando l'interazione con il codice sottostante, è possibile utilizzare l'[editor grafico](automation-graphical-authoring-intro.md) nel portale di anteprima di Azure.


## In che modo Automazione di Azure è correlato ad altri strumenti di automazione?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) è stato progettato per automatizzare le attività di gestione nel cloud privato. Viene installato in locale nel data center come un componente di [Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx). SMA e Automazione di Azure utilizzano lo stesso formato di Runbook basato sul flusso di lavoro di Windows PowerShell, ma SMA non supporta i [Runbook grafici](automation-graphical-authoring-intro.md).

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) è stato progettato per consentire l'automazione delle risorse locali. Utilizza un formato diverso di Runbook rispetto ad Automazione di Azure e a Service Management Automation e presenta un'interfaccia grafica per creare Runbook senza che sia necessario creare degli script. I Runbook dello strumento sono composti da attività di Integration Pack scritti specificatamente per Orchestrator.

## Dove è possibile ottenere ulteriori informazioni?

Sono disponibili numerose risorse per approfondire la conoscenza di Automazione di Azure e creare Runbook personalizzati.

- La sezione dedicata ad **Automazione di Azure di MSDN Library** è il punto in cui ci si trova in questo momento. Gli articoli inclusi in questa sezione forniscono la documentazione completa sulla configurazione e l'amministrazione di Automazione di Azure e sulla creazione di Runbook personalizzati.
- In [Cmdlet di Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) vengono fornite informazioni per l'automazione di operazioni di Azure con Windows PowerShell. Questi cmdlet vengono usati nei Runbook per lavorare con le risorse di Azure.
- Nel [blog su Automazione di Azure](http://azure.microsoft.com/blog/tag/azure-automation) vengono fornite informazioni aggiornate su Automazione di Azure rilasciate da Microsoft. Effettuare la sottoscrizione a questo blog per essere aggiornati sulle ultime novità del team di Automazione di Azure.
- Il [forum su Automazione](http://go.microsoft.com/fwlink/p/?LinkId=390561) consente di pubblicare domande su Automazione di Azure a cui verrà data risposta da Microsoft e dalla community di Automazione.

## È possibile fornire commenti e suggerimenti?

**Gli utenti sono invitati a fornire commenti e suggerimenti.** Se si è in cerca di una soluzione Runbook o di un modulo di integrazione di Automazione di Azure, inviare una richiesta di script in Script Center. In caso di commenti o suggerimenti oppure di richieste di funzionalità per Automazione di Azure, è possibile pubblicarle nell'apposito [forum](http://feedback.windowsazure.com/forums/34192--general-feedback). Grazie.

<!---HONumber=July15_HO5-->