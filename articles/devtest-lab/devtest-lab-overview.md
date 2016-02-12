<properties 
	pageTitle="Che cos'è il servizio Lab di sviluppo/test | Microsoft Azure"
	description="Informazioni su come Lab di sviluppo/test può rendere più semplice creare, gestire e monitorare macchine virtuali di Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

#Che cos'è Lab di sviluppo e test?

Gli sviluppatori e i tester stanno cercando di risolvere i ritardi nella creazione e nella gestione dei propri ambienti accedendo al cloud. Azure consente di risolvere il problema dei ritardi di ambiente e consente il self-service all'interno di una nuova struttura conveniente. Tuttavia, gli sviluppatori e i tester necessitano ancora di molto tempo per la configurazione dei propri ambienti autonomi. Inoltre, i decision maker sono incerti su come sfruttare il cloud per ottimizzare i risparmi senza aggiungere una quantità eccessiva di overhead del processo.

Lab di sviluppo e test di Azure è un servizio che consente agli sviluppatori e ai tester di creare rapidamente ambienti in Azure riducendo al minimo gli sprechi e i costi di controllo. È possibile provare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux tramite modelli ed elementi riutilizzabili. Consente di integrare facilmente la pipeline di distribuzione in Lab di sviluppo e test per effettuare il provisioning di ambienti su richiesta. Aumentare i propri test di carico tramite il provisioning di più agenti di test e creare ambienti di pre-provisioning per training e demo.

##Perché Lab di sviluppo e test?

Lab di sviluppo e test offre i seguenti vantaggi nella creazione, configurazione e gestione di ambienti di sviluppo e test nel cloud

###Servizio autonomo senza preoccupazioni

Lab di sviluppo e test rende più semplice controllare i costi consentendo di impostare criteri nel lab, come numero di macchine virtuali (VM) per ogni utente e il numero di macchine virtuali per ogni lab. Lab di sviluppo e test consente inoltre di creare criteri per arrestare e avviare le macchine virtuali automaticamente.

###Accedere rapidamente a "Pronto per il Test"

Lab di sviluppo e test consente di creare ambienti di pre-provisioning con tutto ciò che è necessario per il team affinché possa iniziare lo sviluppo e il test delle applicazioni. È sufficiente richiedere gli ambienti in cui è stata installata l'ultima compilazione valida dell'applicazione e iniziare subito a lavorare. Oppure utilizzare i contenitori per creare gli ambienti in modo ancora più veloce ed efficiente.

###Creazione di un'unica soluzione per scenari di diverso tipo

E’ possibile acquisire e condividere elementi e modelli di ambienti all'interno del team o dell'organizzazione, il tutto nel controllo del codice sorgente, per creare in modo facile ambienti di sviluppo e test.

###Integrazione con la toolchain esistente

E’ possibile sfruttare plug-in già pronti oppure l’API per effettuare il provisioning di ambienti di sviluppo/test direttamente dallo strumento di integrazione continua (CI), dall'ambiente di sviluppo integrato (IDE, Integrated Development Environment) o dalla pipeline di rilascio automatizzata. E’ anche possibile utilizzare il nostro strumento da riga di comando completo.

##Concetti di Lab di sviluppo e test

L'elenco seguente contiene le definizioni e i concetti chiave di Lab di sviluppo e test:

Gli **Elementi** vengono utilizzati per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Gli elementi possono essere:

- Strumenti che si vuole installare nella VM, come agenti, Fiddler, Visual Studio.
- Azioni che si desidera eseguire sulla macchina virtuale, ad esempio la clonazione di un archivio.
- Applicazioni che si vuole testare.

Gli elementi sono file JSON basati su Gestione risorse di Azure (ARM) che contengono istruzioni per eseguire la distribuzione e applicare la configurazione. Ulteriori informazioni su ARM nella [Panoramica di Gestione risorse di Azure](/resource-group-overview.md).

Gli **Archivi di elementi** sono archivi git in cui vengono archiviati gli elementi. È possibile aggiungere gli stessi archivi elementi a più lab all'interno dell'organizzazione consentendone il riutilizzo e la condivisione.

**Base** è un'immagine di macchina virtuale con tutti gli strumenti e le impostazioni preinstallati e configurati per creare rapidamente una macchina virtuale. È possibile eseguire il provisioning di una macchina virtuale scegliendo una base esistente e aggiungendo un elemento per installare l'agente di test. È quindi possibile salvare la macchina virtuale di cui è stato effettuato il provisioning come base in modo che la base possa essere utilizzata senza la necessità di reinstallare l'agente di test per ogni processo di provisioning della macchina virtuale.

**Limiti** è un meccanismo per ridurre al minimo gli sprechi nel lab. Ad esempio, è possibile impostare un limite per limitare il numero di macchine virtuali che possono essere create per ogni utente o in un lab.

I **Criteri** consentono di controllare i costi nel lab. Ad esempio, è possibile creare un criterio per arrestare automaticamente le macchine virtuali in base a una pianificazione definita.

##Passaggi successivi

Per iniziare ad utilizzare Lab di sviluppo e test, seguire l’esercitazione dettagliata [Creare un Lab di sviluppo e test di Azure](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0204_2016-->