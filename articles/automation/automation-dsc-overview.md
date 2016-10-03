<properties 
   pageTitle="Panoramica di Automazione di Azure DSC | Microsoft Azure" 
   description="Panoramica della piattaforma DSC (Desired State Configuration) di Automazione di Azure, dei termini a essa relativi e dei problemi noti" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"
   keywords="powershell dsc, configurazione dello stato desiderato, powershell dsc azure"/>  

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="05/10/2016"
   ms.author="magoedte;coreyp"/>

# Panoramica della piattaforma DSC di Automazione di Azure #

##Che cos'è la piattaforma DSC di Automazione di Azure?##
La distribuzione e il mantenimento dello stato desiderato dei server e delle risorse dell'applicazione possono essere operazioni complesse e soggette a errori. Con Automation DSC (Desired State Configuration) per Azure è possibile distribuire in modo coerente, monitorare in modo affidabile e aggiornare automaticamente lo stato desiderato di tutte le risorse IT su vasta scala dal cloud. Basato sulla piattaforma PowerShell DSC, Automation DSC può allineare le configurazione dei computer con uno stato specifico tra macchine fisiche e virtuali (VM), usando Windows o Linux e nel cloud o in locale. È possibile abilitare la distribuzione continua di servizi IT con controllo della coerenza e gestire facilmente le modifiche rapide nell'ambiente IT ibrido eterogeneo.

La piattaforma DSC di Automazione di Azure si basa sugli elementi principali introdotti nella piattaforma DSC di PowerShell per garantire un'esperienza di gestione della configurazione ancora più semplice. Automation DSC per Azure introduce infatti nella [configurazione dello stato desiderato tramite PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) lo stesso livello di gestione attualmente offerto da Automazione di Azure per gli script di PowerShell.

La piattaforma DSC di Automazione di Azure consente di [creare e gestire configurazioni dello stato desiderato di PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importare [risorse DSC](https://technet.microsoft.com/library/dn282125.aspx) e generare configurazioni di nodi DSC (documenti MOF), interamente nel cloud. Questi elementi DSC verranno inseriti nel [server di pull DSC](https://technet.microsoft.com/library/dn249913.aspx) di Automazione di Azure in modo che i nodi di destinazione (ad esempio, computer fisici e macchine virtuali) nel cloud o in locale possano selezionarli, conformarsi allo stato desiderato specificato e segnalare ad Automazione di Azure la propria conformità allo stato desiderato.

Si preferisce guardare che leggere? Esaminare il video di seguito del maggio 2015, quando Automazione di Azure DSC è stato annunciato per la prima volta. **Nota:** mentre i concetti e il ciclo di vita descritti in questo video sono corretti, Automazione di Azure DSC ha fatto molti progressi dalla registrazione del video. È ora disponibile a livello generale, ha un'interfaccia utente molto più estesa nel portale di Azure e supporta molte funzionalità aggiuntive.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]



## Termini relativi alla piattaforma DSC di Automazione di Azure ##
### Configurazione ###
Nella piattaforma DSC di PowerShell è stato introdotto un nuovo concetto, ovvero le configurazioni. Esse consentono di definire, tramite la sintassi di PowerShell, lo stato desiderato per il proprio ambiente. Per usare la piattaforma DSC per configurare l'ambiente, prima è necessario definire un blocco di script di Windows PowerShell con la parola chiave configuration, quindi specificare un identificatore e infine inserire le parentesi graffe ({})per delimitare il blocco.

![testo alternativo](./media/automation-dsc-overview/AADSC_1.png)

All'interno del blocco di configurazione è possibile definire blocchi di configurazione nodo che specifichino la configurazione desiderata per un set di nodi (computer) dell'ambiente da configurare esattamente nella stessa maniera. In questo modo una configurazione nodo rappresenta un "ruolo" che uno o più nodi devono assumersi. Un blocco di configurazione nodo inizia con la parola chiave node, che deve essere seguita dal nome del ruolo, che può corrispondere a una variabile o a un'espressione. Dopo il nome del ruolo, usare le parentesi graffe {} per delimitare il blocco di configurazione nodo.

![testo alternativo](./media/automation-dsc-overview/AADSC_2.png)
 
All'interno del blocco di configurazione nodo è possibile definire blocchi risorsa per configurare risorse DSC specifiche. Un blocco risorsa inizia con il nome della risorsa, seguito dall'identificatore che si intende specificare per il blocco e quindi dalle parentesi graffe {} per delimitare il blocco.

![testo alternativo](./media/automation-dsc-overview/AADSC_3.png)

Per informazioni più dettagliate sulla parola chiave configuration, vedere l'articolo relativo alla [descrizione dell'uso di tale parola chiave nella piattaforma DSC](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Informazioni sulla parola chiave configuration nella piattaforma DSC").

Eseguendo (compilando) una configurazione DSC, verranno prodotte una o più configurazioni nodo DSC (documenti MOF), ovvero le indicazioni applicate dai nodi DSC per diventare conformi allo stato desiderato.

La piattaforma DSC di Automazione di Azure consente di importare, creare e compilare configurazioni DSC in Automazione di Azure in modo analogo all'importazione, creazione e avvio dei Runbook in Automazione di Azure.

>[AZURE.IMPORTANT] Una configurazione dovrebbe includere un solo blocco di configurazione, con lo stesso nome della configurazione, nella piattaforma DSC di Automazione di Azure.


###Configurazione nodo###

Quando una configurazione DSC viene compilata, vengono prodotte una o più configurazioni nodo a seconda dei blocchi Node presenti nella configurazione. Una configurazione nodo corrisponde a un "documento MOF" o "documento di configurazione", se si ha familiarità con la terminologia relativa a PowerShell DSC, e rappresenta un "ruolo", ad esempio server Web o ruolo di lavoro, il cui stato desiderato uno o più nodi dovrebbero assumersi o usare per il controllo di conformità. I nomi delle configurazioni di nodi in DSC di Automazione di Azure assumono la forma di “Configurazione Name.NodeConfigurationBlockName”.

I nodi DSC di PowerShell riconoscono le configurazioni nodo da eseguire mediante metodi push o pull DSC. Automation DSC per Azure si basa sul metodo pull DSC, in cui i nodi richiedono al server di pull della piattaforma le configurazioni nodo da applicare. Poiché i nodi effettuano la richiesta alla piattaforma DSC di Automazione di Azure, possono essere protetti da firewall, avere tutte le porte in ingresso chiuse e così via. Devono solo avere accesso in uscita a Internet, in modo diretto o tramite proxy.


###Nodo###

Un nodo DSC è qualsiasi computer in cui la configurazione è gestita da DSC Può essere una macchina virtuale di Azure per Windows o Linux, una macchina virtuale locale, un host fisico o una macchina virtuale in un altro cloud pubblico. I nodi eseguono le configurazioni nodo per diventare conformi e mantenere la conformità allo stato desiderato definito e possono anche segnalare a un server di reporting il proprio stato di configurazione e la propria conformità rispetto allo stato desiderato.

Automation DSC per Azure facilita il caricamento dei nodi per la gestione e consente di cambiare la configurazione assegnata a ciascun nodo sul lato server. In questo modo, la volta successiva che un nodo cercherà istruzioni nel server, assumerà un ruolo diverso e adatterà di conseguenza la propria configurazione e lo stato di conformità in base al quale segnalare la corrispondenza.


###Risorsa###
Le risorse DSC sono blocchi predefiniti utilizzabili per specificare una configurazione della piattaforma DSC (Desidered State Configuration) di Windows PowerShell. DSC include un set di risorse predefinite ad esempio per file e cartelle, funzionalità e ruoli del server, impostazioni del Registro di sistema, variabili di ambiente, servizi e processi. Per l'elenco completo delle risorse DSC predefinite e per informazioni su come usarle, vedere l'articolo relativo alle [risorse predefinite della piattaforma DSC di Windows PowerShell](https://technet.microsoft.com/library/dn249921.aspx).

È anche possibile importare risorse DSC come parte dei moduli di PowerShell per estendere il set delle risorse DSC predefinite. I nodi DSC recupereranno tramite pull le risorse non predefinite dal server di pull DSC se una configurazione nodo da eseguire include riferimenti a tali risorse. Per informazioni su come creare risorse personalizzate, vedere l'articolo relativo alla [creazione di risorse personalizzate della piattaforma DSC di Windows PowerShell](https://technet.microsoft.com/library/dn249927.aspx).

La piattaforma DSC di Automazione di Azure viene fornita con le stesse risorse predefinite della piattaforma DSC di PowerShell. È possibile aggiungere altre risorse ad Automazione di Azure importando moduli di PowerShell contenenti le risorse desiderate.


###Processo di compilazione###
Nella piattaforma DSC di Automazione di Azure un processo di compilazione è un'istanza di compilazione di una configurazione per creare una o più configurazioni nodo. Tali processi sono simili ai processi di Runbook di Automazione di Azure, con la differenza che non eseguono effettivamente alcuna attività tranne la creazione di configurazioni nodo. Qualsiasi configurazione nodo creata da un processo di compilazione viene inserita automaticamente nel server di pull della piattaforma DSC di Automazione di Azure e sovrascrive le rispettive versioni precedenti, se esistenti. Il nome di una configurazione di nodi prodotta da un processo di compilazione assume la forma di “ConfigurationName.NodeConfigurationBlockName”. Ad esempio, compilando la configurazione seguente, verrà prodotta una sola configurazione nodo denominata "MyConfiguration.webserver":


![testo alternativo](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE] Le configurazioni possono essere pubblicate come i Runbook, ma in questo caso non vengono inseriti elementi DSC nel server di pull della piattaforma DSC di Automazione di Azure. Sono i processi di compilazione a eseguire tale operazione. Per altre informazioni sulla "pubblicazione" in Automazione di Azure, vedere [Pubblicazione di un runbook](https://msdn.microsoft.com/library/dn903765.aspx).


##Ciclo di vita della piattaforma DSC di Automazione di Azure##
Passando da un account di automazione vuoto a un set gestito di nodi configurati correttamente si generano una serie di processi per la definizione delle configurazioni, l'attivazione di tali configurazioni in configurazioni di nodo e nodi di caricamento per DSC di automazione di Azure e le configurazioni di nodi. Nel diagramma seguente viene illustrato il ciclo di vita DSC di automazione di Azure:

![testo alternativo](./media/automation-dsc-overview/DSCLifecycle.png)


L'immagine seguente illustra in dettaglio il processo nel ciclo di vita di DSC. Include diverse modalità per importare e applicare una configurazione ai nodi in Automazione di Azure, ai componenti richiesti da un computer locale per supportare DSC e alle interazioni tra diversi componenti.

![Architettura DSC](./media/automation-dsc-overview/dsc-architecture.png)

##Problemi noti:##

- Durante l'aggiornamento a WMF 5 RTM, se il computer è già registrato come nodo in Automation DSC per Azure, annullare la registrazione da Automation DSC per Azure e ripeterla dopo l'aggiornamento di WMF 5 RTM.

- In questo momento la piattaforma DSC di Automazione di Azure non supporta configurazioni DSC parziali o composite. È tuttavia possibile importare e usare risorse DSC composite nelle configurazioni di Automation DSC per Azure, esattamente come in PowerShell locale, consentendo il riutilizzo della configurazione.

- Perché l'agente DSC di PowerShell per Windows possa comunicare con Automazione di Azure, deve essere installata la versione più recente di WMF 5. È necessario installare l’ultima versione dell'agente DSC di PowerShell per Windows per poter comunicare con Automazione di Azure.

- Il server di pull DSC di PowerShell tradizionale prevede che i file ZIP dei moduli vengono inseriti con il formato **NomeModulo\_Versione.zip**. Automazione di Azure prevede che i moduli di PowerShell vengano importati con nomi nel formato **NomeModulo.zip**. Per altre informazioni sul formato dei moduli di integrazione necessario per importare il modulo in Automazione di Azure, vedere [questo post di blog](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/).

- I moduli di PowerShell importati in Automazione di Azure non possono includere file con estensione doc o docx. Alcuni moduli di PowerShell contenenti risorse DSC includono file di questo tipo per rendere disponibile la Guida. È consigliabile rimuovere tali file dai moduli prima di eseguire l'importazione in Automazione di Azure.

- Quando un nodo viene inizialmente registrato in un account di Automazione di Azure o viene modificato in modo da essere mappato a una configurazione nodo diversa sul lato server, il relativo stato risulterà 'conforme' anche se non è effettivamente conforme alla nuova configurazione nodo a cui è stato mappato. Dopo che il nodo esegue il primo pull e invia il primo report, dopo la registrazione o la modifica del mapping a una configurazione nodo, il relativo stato può essere considerato attendibile.

- Quando si carica una VM Windows in Azure per la gestione con Automation DSC per Azure con uno dei metodi di caricamento diretto, potrebbe essere necessaria fino a un'ora prima che la VM venga visualizzata come nodo DSC in Automazione di Azure. Questo comportamento è causato dall'installazione di Windows Management Framework 5.0 nella macchina virtuale da parte dell'estensione DSC per le VM di Azure, necessario per caricare la VM in Automation DSC per Azure.

- Dopo la registrazione, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. A questo punto, il protocollo di registrazione PowerShell DSC non può rinnovare automaticamente certificati quando è prossimo alla scadenza, pertanto è necessario registrare nuovamente i nodi dopo il periodo di un anno. Prima di registrare di nuovo, verificare che ogni nodo esegua Windows Management Framework 5.0 RTM. Se il certificato di autenticazione di un nodo scade, e se il nodo non è registrato, il nodo non sarà in grado di comunicare con Automazione di Azure e sarà indicato che "Non risponde". La registrazione viene eseguita nello stesso modo con cui il nodo è stato registrato inizialmente. Una registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l’ora di scadenza del certificato. comporterà un nuovo certificato che viene generato e utilizzato.

- Durante l'aggiornamento a WMF 5 RTM, se il computer è già registrato come nodo in Automation DSC per Azure, annullare la registrazione da Automation DSC per Azure e ripeterla dopo l'aggiornamento di WMF 5 RTM. Prima di ripetere la registrazione, eliminare il file $env:windir\\system32\\configuration\\DSCEngineCache.mof.

- I cmdlet PowerShell DSC potrebbero non funzionare se WMF 5 RTM è installato su WMF 5 Production Preview. Per risolvere il problema, eseguire il comando seguente in una sessione di PowerShell con privilegi elevati come amministratore: `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
 

##Articoli correlati##

- [Caricamento di computer per la gestione con Automation DSC per Azure](../automation/automation-dsc-onboarding.md)
- [Compilazione di configurazioni in Azure Automation DSC](../automation/automation-dsc-compile.md)
- [Cmdlet di Automation DSC per Azure](https://msdn.microsoft.com/library/mt244122.aspx)
- [Prezzi di Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
- [La distribuzione continua nelle macchine virtuali IaaS tramite Azure Automation DSC e Chocolatey](automation-dsc-cd-chocolatey.md)

<!---HONumber=AcomDC_0921_2016-->