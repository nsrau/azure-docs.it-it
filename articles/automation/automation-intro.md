---
title: Informazioni su Automazione di Azure | Microsoft Docs
description: "Informazioni sui vantaggi offerti da Automazione di Azure e risposte alle domande più comuni per iniziare a creare e usare runbook e Automation DSC per Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "che cos'è l'automazione, Automazione di Azure, esempi di Automazione di Azure"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
ms.openlocfilehash: c9f5c346350a62540ba29444b1b00cc9dc398d56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-overview"></a>Panoramica di Automazione di Azure
Automazione di Microsoft Azure offre agli utenti la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud e aziendali. Consente di risparmiare tempo e aumenta l'affidabilità delle normali attività amministrative e le pianifica anche per essere eseguite automaticamente a intervalli regolari. È possibile automatizzare i processi utilizzando runbook o automatizzare la gestione della configurazione tramite Configurazione dello stato desiderato. Questo articolo fornisce una breve panoramica su Automazione di Azure e risposte ad alcune domande comuni. È possibile fare riferimento ad altri articoli in questa raccolta per informazioni più dettagliate su argomenti diversi.

## <a name="automating-processes-with-runbooks"></a>Automazione di processi con runbook
Un Runbook è un set di attività che eseguono un processo automatico in Automazione di Azure. Potrebbe trattarsi di un processo semplice, ad esempio l'avvio di una macchina virtuale e la creazione di una voce di log, o di un runbook complesso che combina altri runbook più piccoli per eseguire un processo complesso tra più risorse o anche più cloud e ambienti locali.  

Ad esempio, si potrebbe disporre di un processo manuale esistente per il troncamento di un database SQL, se esso sta per raggiungere le dimensioni massime, che include più passaggi tra cui la connessione al server, la connessione al database, l’ottenimento della dimensione corrente del database, il controllo del superamento della soglia e quindi il troncamento e la notifica all'utente. Invece di eseguire manualmente ognuno di questi passaggi, è possibile creare un Runbook che eseguirà tutte queste attività come un singolo processo. È sufficiente avviare il runbook, fornire le informazioni necessarie, ad esempio il nome del server SQL, il nome del database e l'indirizzo di posta elettronica del destinatario e quindi attendere il completamento del processo. 

## <a name="what-can-runbooks-automate"></a>Cosa consentono di automatizzare i Runbook?
I Runbook di Automazione di Azure si basano sul flusso di lavoro PowerShell di Windows o su PowerShell di Windows, pertanto consentono di effettuare tutto ciò che è possibile effettuare in PowerShell. Se un'applicazione o un servizio dispongono di un'API, un Runbook permette di utilizzarli. Se è disponibile un modulo PowerShell per l'applicazione, è possibile caricarlo in Automazione di Azure e includere i cmdlet nel runbook. I runbook di Automazione di Azure vengono eseguiti nel cloud di Azure e possono accedere a tutte le risorse interne al cloud o esterne a cui è possibile accedere dal cloud. Il [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md)permette di eseguire i runbook nel data center locale per la gestione delle risorse locali. 

## <a name="getting-runbooks-from-the-community"></a>Recupero di runbook dalla community
La [raccolta di Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contiene Runbook di Microsoft e della community che è possibile usare senza modifiche nel proprio ambiente o personalizzare per esigenze specifiche. Sono inoltre utili come riferimenti per imparare a creare Runbook personalizzati. Nella raccolta è inoltre possibile inserire i propri Runbook personalizzati che si ritiene possano rivelarsi utili per gli altri utenti. 

## <a name="creating-runbooks-with-azure-automation"></a>Creazione di Runbook con Automazione di Azure
È possibile [creare Runbook personalizzati](automation-creating-importing-runbook.md) da zero o modificare quelli contenuti nella [raccolta di Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) in base alle proprie esigenze. Sono disponibili quattro diversi [tipi di runbook](automation-runbook-types.md) tra cui è possibile scegliere in base alle esigenze e all'uso di PowerShell. Se si preferisce lavorare direttamente con il codice di PowerShell, è possibile usare un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) o un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) da modificare offline o con l'[editor di testo](http://msdn.microsoft.com/library/azure/dn879137.aspx) nel portale di Azure. Se si preferisce modificare un runbook evitando l'interazione con il codice sottostante, è possibile creare un [runbook grafico](automation-runbook-types.md#graphical-runbooks) usando l'[editor grafico](automation-graphical-authoring-intro.md) nel portale di Azure. 

Si preferisce guardare che leggere? Il video seguente tratto dalla sessione di Microsoft Ignite di maggio 2015 contiene informazioni utili. Nota: le funzionalità e i concetti descritti in questo video sono corretti, ma Automazione di Azure si è evoluto molto da quando il video è stato registrato. Ora ha un'interfaccia utente più estesa nel portale di Azure e supporta funzionalità aggiuntive.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automazione della gestione della configurazione con Configurazione dello stato desiderato
[DSC (Desired State Configuration) di PowerShell](https://technet.microsoft.com/library/dn249912.aspx) è una piattaforma di gestione che permette di gestire, distribuire e potenziare la configurazione degli host fisici e delle macchine virtuali usando una sintassi dichiarativa di PowerShell. È possibile definire configurazioni su un server centrale DSC Pull che i computer di destinazione possono recuperare e applicare automaticamente. DSC fornisce un insieme di cmdlet PowerShell che è possibile utilizzare per gestire le configurazioni e le risorse.  

[Automation DSC per Azure](automation-dsc-overview.md) è una soluzione basata su cloud per DSC PowerShell che fornisce i servizi necessari per gli ambienti aziendali.  È possibile gestire le risorse DSC in Automazione di Azure e applicare le configurazioni alle macchine virtuali o fisiche che le recuperano da un Server Pull DSC nel cloud di Azure.  Fornisce inoltre servizi di creazione report che informano di eventi importanti, ad esempio quando i nodi hanno deviato dalla configurazione assegnata e quando una nuova configurazione è stata applicata. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Creazione di configurazioni personalizzate DSC con Automazione di Azure
[Configurazioni DSC](automation-dsc-overview.md) specificano lo stato richiesto di un nodo.  Più nodi possono applicare la stessa configurazione per garantire che essi mantengano uno stato identico.  È possibile creare una configurazione usando qualsiasi editor di testo sul computer locale e quindi importarla in Automazione di Azure dove è possibile compilarla e applicarvi nodi.

## <a name="getting-modules-and-configurations"></a>Recupero di moduli e configurazioni
È possibile ottenere [moduli PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contenenti i cmdlet da usare nei runbook e nelle configurazioni DSC dalla [PowerShell Gallery](http://www.powershellgallery.com/). È possibile avviare questa raccolta dal portale di Azure e importare direttamente i moduli in Automazione di Azure oppure è possibile scaricarli e importarli manualmente. Non è possibile installare i moduli direttamente dal portale di Azure, ma è possibile scaricarli e installarli come qualsiasi altro modulo. 

## <a name="example-practical-applications-of-azure-automation"></a>Applicazioni pratiche di esempio di Automazione di Azure
Di seguito sono riportati solo alcuni esempi dei tipi di scenari di automazione con Automazione di Azure. 

* Creare e copiare le macchine virtuali in diverse sottoscrizioni di Azure. 
* Pianificare le copie di file da un computer locale a un contenitore dell’archiviazione BLOB di Azure. 
* Automatizzare le funzioni di sicurezza, ad esempio negare le richieste da un client quando viene rilevato un attacco Denial of service. 
* Garantire che i computer si allineino continuamente con i criteri di sicurezza configurati.
* Gestire la distribuzione continua del codice dell'applicazione tra cloud e infrastruttura locale. 
* Creare una foresta di Active Directory in Azure per l'ambiente lab. 
* Troncare una tabella in un database SQL se il DB sta per raggiungere le dimensioni massime. 
* Aggiornare in remoto le impostazioni dell'ambiente per un sito Web di Azure. 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>In che modo Automazione di Azure è correlato ad altri strumenti di automazione?
[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) è stato progettato per automatizzare le attività di gestione nel cloud privato. Viene installato in locale nel data center come componente di [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA e Automazione di Azure usano lo stesso formato di runbook basato sul flusso di lavoro di Windows PowerShell e su Windows PowerShell, ma SMA non supporta i [runbook grafici](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) è stato progettato per consentire l'automazione delle risorse locali. Utilizza un formato diverso di Runbook rispetto ad Automazione di Azure e a Service Management Automation e presenta un'interfaccia grafica per creare Runbook senza che sia necessario creare degli script. I Runbook dello strumento sono composti da attività di Integration Pack scritti specificatamente per Orchestrator. 

## <a name="where-can-i-get-more-information"></a>Dove è possibile ottenere ulteriori informazioni?
Sono disponibili numerose risorse per approfondire la conoscenza di Automazione di Azure e creare Runbook personalizzati. 

* **Automazione di Azure di MSDN Library** è il punto in cui ci si trova in questo momento. Gli articoli inclusi in questa sezione forniscono la documentazione completa sulla configurazione e l'amministrazione di Automazione di Azure e sulla creazione di Runbook personalizzati. 
* [cmdlet di Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fornisce informazioni per l'automazione di operazioni di Azure con Windows PowerShell. Questi cmdlet vengono usati nei Runbook per lavorare con le risorse di Azure. 
* [blog sulla gestione](https://azure.microsoft.com/blog/tag/azure-automation/) fornisce informazioni aggiornate su Automazione di Azure e altre tecnologie di gestione di Microsoft. Effettuare la sottoscrizione a questo blog per essere aggiornati sulle ultime novità del team di Automazione di Azure. 
* [forum su Automazione](http://go.microsoft.com/fwlink/p/?LinkId=390561) consente di pubblicare domande su Automazione di Azure a cui verrà data risposta da Microsoft e dalla community di Automazione. 
* [cmdlet di Automazione di Azure](https://msdn.microsoft.com/library/mt244122.aspx) fornisce informazioni per l'automazione delle attività di amministrazione. Contiene i cmdlet per gestire account di automazione, asset, runbook, DSC.

## <a name="can-i-provide-feedback"></a>È possibile fornire commenti e suggerimenti?
**Gli utenti sono invitati a fornire commenti e suggerimenti.** Se si è in cerca di una soluzione Runbook o di un modulo di integrazione di Automazione di Azure, inviare una richiesta di script in Script Center. In caso di commenti o suggerimenti oppure di richieste di funzionalità per Automazione di Azure, è possibile pubblicarle nell'apposito [forum](http://feedback.windowsazure.com/forums/34192--general-feedback). Grazie. 

