---
title: Panoramica di Automation DSC per Azure | Documentazione Microsoft
description: Panoramica della piattaforma DSC (Desired State Configuration) di Automazione di Azure, dei termini a essa relativi e dei problemi noti
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 02/02/2017
ms.author: magoedte;eslesar
translationtype: Human Translation
ms.sourcegitcommit: f0d3a5c1929bb6dafef735ae3f4291e329861cc9
ms.openlocfilehash: b750878703baf143a2e8247bdd01f358e462e598

---
# <a name="azure-automation-dsc-overview"></a>Panoramica della piattaforma DSC di Automazione di Azure

## <a name="what-is-azure-automation-dsc"></a>Che cos'è la piattaforma DSC di Automazione di Azure?

La distribuzione e il mantenimento dello stato desiderato dei server e delle risorse dell'applicazione possono essere operazioni complesse e soggette a errori. Con Automation DSC (Desired State Configuration) per Azure è possibile distribuire in modo coerente, monitorare in modo affidabile e aggiornare automaticamente lo stato desiderato di tutte le risorse IT su vasta scala dal cloud. Basato sulla piattaforma PowerShell DSC, Automation DSC può allineare le configurazione dei computer con uno stato specifico tra macchine fisiche e virtuali (VM), usando Windows o Linux e nel cloud o in locale. È possibile abilitare la distribuzione continua di servizi IT con controllo della coerenza e gestire facilmente le modifiche rapide nell'ambiente IT ibrido eterogeneo.

La piattaforma DSC di Automazione di Azure si basa sugli elementi principali introdotti nella piattaforma DSC di PowerShell per garantire un'esperienza di gestione della configurazione ancora più semplice. Automation DSC per Azure introduce infatti nella [configurazione dello stato desiderato tramite PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) lo stesso livello di gestione attualmente offerto da Automazione di Azure per gli script di PowerShell.

Automation DSC per Azure consente di [creare e gestire configurazioni dello stato desiderato tramite PowerShell](https://technet.microsoft.com/library/dn249918.aspx), [importare risorse DSC](https://technet.microsoft.com/library/dn282125.aspx) e generare configurazioni di nodi DSC (documenti MOF), interamente nel cloud. Questi elementi DSC verranno posizionati sul [server di pull DSC](https://technet.microsoft.com/library/dn249913.aspx) di Automazione di Azure in modo che i nodi di destinazione ricevano automaticamente le configurazioni, conformemente allo stato desiderato, e segnalino la propria conformità. Automazione di Azure può avere come destinazione macchine virtuali o fisiche, su cloud o locali. 

Si preferisce guardare che leggere? Guardare il video di seguito del maggio 2015, quando Azure Automation DSC è stato annunciato per la prima volta. **Nota:** mentre i concetti e il ciclo di vita descritti in questo video sono corretti, Automazione di Azure DSC ha fatto molti progressi dalla registrazione del video. È ora disponibile a livello generale, ha un'interfaccia utente molto più estesa nel portale di Azure e supporta molte funzionalità aggiuntive.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="azure-automation-dsc-terms"></a>Termini relativi alla piattaforma DSC di Automazione di Azure

### <a name="configuration"></a>Configurazione

Nella piattaforma DSC di PowerShell è stato introdotto un nuovo concetto, ovvero le configurazioni. Esse consentono di definire, tramite la sintassi di PowerShell, lo stato desiderato per il proprio ambiente. Per usare la piattaforma DSC al fine di configurare l'ambiente, prima è necessario definire un blocco di script di Windows PowerShell con la parola chiave configuration, seguita da un identificatore, e infine inserire le parentesi graffe ({}) per delimitare il blocco.

![testo alternativo](./media/automation-dsc-overview/AADSC_1.png)

All'interno del blocco di configurazione è possibile definire configurazioni nodo che specifichino la configurazione desiderata per un set di nodi (computer) dell'ambiente da configurare nella stessa maniera. In questo modo una configurazione nodo rappresenta un "ruolo" che uno o più nodi devono assumere. Un blocco di configurazione nodo inizia con la parola chiave node, che deve essere seguita dal nome del ruolo, che può corrispondere a una variabile o a un'espressione. Dopo il nome del ruolo, usare le parentesi graffe {} per delimitare il blocco di configurazione nodo.

![testo alternativo](./media/automation-dsc-overview/AADSC_2.png)

All'interno del blocco di configurazione nodo è possibile definire blocchi risorsa per configurare risorse DSC specifiche. Un blocco risorsa inizia con il nome della risorsa, seguito dall'identificatore che si intende specificare per il blocco e quindi dalle parentesi graffe {} per delimitare il blocco.

![testo alternativo](./media/automation-dsc-overview/AADSC_3.png)

Per informazioni più dettagliate sulla parola chiave configuration, vedere [Configurazioni DSC](https://msdn.microsoft.com/en-us/powershell/dsc/configurations "Configurazioni DSC")

Eseguendo (compilando) una configurazione DSC, verranno prodotte una o più configurazioni nodo DSC (documenti MOF), ovvero le indicazioni applicate dai nodi DSC per diventare conformi allo stato desiderato.

La piattaforma DSC di Automazione di Azure consente di importare, creare e compilare configurazioni DSC in Automazione di Azure in modo analogo all'importazione, creazione e avvio dei Runbook in Automazione di Azure.

> [!IMPORTANT]
> Una configurazione dovrebbe includere un solo blocco di configurazione, con lo stesso nome della configurazione, nella piattaforma DSC di Automazione di Azure.

### <a name="node-configuration"></a>Configurazione nodo

Quando una configurazione DSC viene compilata, vengono prodotte una o più configurazioni nodo a seconda dei blocchi Node presenti nella configurazione. Una configurazione nodo equivale a un "MOF" o "documento di configurazione". Le configurazioni nodo rappresentano un "ruolo", come server Web o di lavoro, di cui uno o più nodi devono presupporre o verificare lo stato desiderato per la conformità. I nomi delle configurazioni di nodi in Automation DSC per Azure assumono la forma "Configuration Name.NodeConfigurationBlockName".

I nodi DSC di PowerShell riconoscono le configurazioni nodo da eseguire mediante metodi push o pull DSC. Automation DSC per Azure si basa sul metodo pull DSC, in cui i nodi richiedono al server di pull della piattaforma le configurazioni nodo da applicare. Poiché i nodi effettuano la richiesta alla piattaforma DSC di Automazione di Azure, possono essere protetti da firewall, avere tutte le porte in ingresso chiuse e così via. Devono solo avere accesso in uscita a Internet, in modo diretto o tramite proxy.

### <a name="node"></a>Nodo

Un nodo DSC è qualsiasi computer in cui la configurazione è gestita da DSC Può essere una macchina virtuale di Azure per Windows o Linux, una macchina virtuale locale, un host fisico o una macchina virtuale in un altro cloud pubblico. I nodi eseguono le configurazioni nodo per diventare conformi e mantenere la conformità con lo stato desiderato che definiscono. I nodi inoltre inviano segnalazioni a un server di report sul loro stato di configurazione e la loro conformità.

Azure Automation DSC rende l'onboarding dei nodi semplice e flessibile. Azure Automation DSC consente di modificare la configurazione nodo assegnata a ogni nodo. Quando si verifica una modifica della configurazione nodo, il nodo assume automaticamente un ruolo diverso modificando la sua configurazione e inizia a inviare segnalazioni rispetto al suo nuovo ruolo. 

### <a name="resource"></a>Risorsa

Le risorse DSC sono blocchi predefiniti utilizzabili per specificare una configurazione della piattaforma DSC (Desidered State Configuration) di Windows PowerShell. DSC include un set di risorse predefinite ad esempio per file e cartelle, funzionalità e ruoli del server, impostazioni del Registro di sistema, variabili di ambiente, servizi e processi. Per l'elenco completo delle risorse DSC predefinite e per informazioni su come usarle, vedere l'articolo relativo alle [risorse predefinite della piattaforma DSC di Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/builtinresource).

È anche possibile importare risorse DSC come parte dei moduli di PowerShell per estendere il set delle risorse DSC predefinite. Se una configurazione nodo che il nodo deve applicare contiene riferimenti a risorse non predefinite, quelle risorse vengono prese dal server di pull DSC tramite nodi DSC. Per informazioni su come creare risorse personalizzate, vedere l'articolo relativo alla [creazione di risorse personalizzate della piattaforma DSC di Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/authoringresource).

La piattaforma DSC di Automazione di Azure viene fornita con le stesse risorse predefinite della piattaforma DSC di PowerShell. È possibile aggiungere altre risorse ad Automazione di Azure importando moduli di PowerShell contenenti le risorse desiderate.

### <a name="compilation-job"></a>Processo di compilazione

Nella piattaforma DSC di Automazione di Azure un processo di compilazione è un'istanza di compilazione di una configurazione per creare una o più configurazioni nodo. Tali processi sono simili ai processi di Runbook di Automazione di Azure, con la differenza che non eseguono effettivamente alcuna attività tranne la creazione di configurazioni nodo. Qualsiasi configurazione nodo creata da un processo di compilazione viene inserita automaticamente nel server di pull della piattaforma DSC di Automazione di Azure e sovrascrive le rispettive versioni precedenti, se esistenti. Il nome di una configurazione di nodi creata da un processo di compilazione assume la forma "ConfigurationName.NodeConfigurationBlockName". Se ad esempio si compila la configurazione seguente, verrà creata una sola configurazione nodo denominata "MyConfiguration.webserver"

![testo alternativo](./media/automation-dsc-overview/AADSC_5.png)

> [!NOTE]
> Le configurazioni possono essere pubblicate come i Runbook, ma in questo caso non vengono inseriti elementi DSC nel server di pull della piattaforma DSC di Automazione di Azure. Sono i processi di compilazione a eseguire tale operazione. Per altre informazioni sulla "pubblicazione" in Automazione di Azure, vedere [Pubblicazione di un runbook](https://msdn.microsoft.com/library/dn903765.aspx).


## <a name="azure-automation-dsc-lifecycle"></a>Ciclo di vita della piattaforma DSC di Automazione di Azure

Passando da un account di automazione vuoto a un set gestito di nodi configurati correttamente si generano una serie di processi per la definizione delle configurazioni, l'attivazione di tali configurazioni in configurazioni di nodo e nodi di caricamento per DSC di automazione di Azure e le configurazioni di nodi. Nel diagramma seguente viene illustrato il ciclo di vita DSC di automazione di Azure:

![testo alternativo](./media/automation-dsc-overview/DSCLifecycle.png)

L'immagine seguente illustra in dettaglio il processo nel ciclo di vita di DSC. Include diverse modalità per importare e applicare una configurazione ai nodi in Automazione di Azure, ai componenti richiesti da un computer locale per supportare DSC e alle interazioni tra diversi componenti.

![Architettura DSC](./media/automation-dsc-overview/dsc-architecture.png)

## <a name="gotchas--known-issues"></a>Problemi noti:

* Perché l'agente DSC di PowerShell per Windows possa comunicare con Automazione di Azure, deve essere installata la versione più recente di WMF 5. È necessario installare l’ultima versione dell'agente DSC di PowerShell per Windows per poter comunicare con Automazione di Azure.
* Se una macchina è già registrata come nodo in Azure Automation DSC prima dell'aggiornamento a WMF5, eliminare la sua registrazione da Azure Automation DSC e registrarla dopo aver completato l'installazione di WMF 5. Eliminare il file $env:windir\system32\configuration\DSCEngineCache.mof prima della nuova registrazione
* Se viene installato WMF 5 RTM su WMF 5 Production Preview, DSC potrebbe non funzionare correttamente. Per risolvere il problema, eseguire il comando seguente in una sessione di PowerShell con privilegi elevati (come amministratore): `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
* In questo momento la piattaforma Azure Automation DSC non supporta configurazioni DSC parziali o composite. È tuttavia possibile importare e usare risorse DSC composite nelle configurazioni di Azure Automation DSC, come in PowerShell locale, consentendo il riutilizzo della configurazione.
* Il server di pull per PowerShell DSC tradizionale prevede che i file ZIP dei moduli siano inseriti con il formato **NomeModulo_Versione.zip**. Automazione di Azure prevede che i moduli di PowerShell vengano importati con nomi nel formato **NomeModulo.zip**. Per altre informazioni sul formato dei moduli di integrazione necessario per importare il modulo in Automazione di Azure, vedere [questo post di blog](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) .
* I moduli di PowerShell importati in Automazione di Azure non possono includere file con estensione doc o docx. Alcuni moduli di PowerShell contenenti risorse DSC includono file di questo tipo per rendere disponibile la Guida. È consigliabile rimuovere tali file dai moduli prima di eseguire l'importazione in Automazione di Azure.
* Quando un nodo viene inizialmente registrato con un account di Automazione di Azure o il nodo viene modificato per eseguire il mapping a un altro nodo configurazione lato server, il relativo stato è "Conforme". Ciò si verifica anche se lo stato del nodo non è conforme con la configurazione nodo del mapping. Dopo che il nodo invia il primo report dopo la registrazione o la modifica del mapping a una configurazione nodo, il relativo stato può essere considerato attendibile.
* Quando si carica una VM Windows in Azure per la gestione con Automation DSC per Azure con uno dei metodi di caricamento diretto, potrebbe essere necessaria fino a un'ora prima che la VM venga visualizzata come nodo DSC in Automazione di Azure. Questo comportamento è causato dall'installazione di Windows Management Framework 5.0 nella macchina virtuale da parte dell'estensione DSC per le VM di Azure, necessario per caricare la VM in Automation DSC per Azure.
* Dopo la registrazione, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Attualmente il protocollo di registrazione di PowerShell DSC non può rinnovare automaticamente i certificati quando si avvicina la scadenza, quindi è necessario registrare di nuovo i nodi dopo un anno. Prima di registrare di nuovo, verificare che ogni nodo esegua Windows Management Framework 5.0 RTM. Se il certificato di autenticazione di un nodo scade e se il nodo non è registrato, il nodo non sarà in grado di comunicare con Automazione di Azure e sarà indicato che "Non risponde". La registrazione viene eseguita nello stesso modo con cui il nodo è stato registrato inizialmente. Una registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l’ora di scadenza del certificato. comporterà un nuovo certificato che viene generato e utilizzato.


## <a name="related-articles"></a>Articoli correlati

* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)
* [Compilazione di configurazioni in Automation DSC per Azure](automation-dsc-compile.md)
* [Cmdlet di Automation DSC per Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Prezzi di Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
* [La distribuzione continua nelle macchine virtuali IaaS tramite Azure Automation DSC e Chocolatey](automation-dsc-cd-chocolatey.md)




<!--HONumber=Feb17_HO1-->


