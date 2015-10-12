<properties 
   pageTitle="Panoramica di Automazione di Azure DSC | Microsoft Azure" 
   description="Panoramica della piattaforma DSC (Desired State Configuration) di Automazione di Azure, dei termini a essa relativi e dei problemi noti" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="09/24/2015"
   ms.author="coreyp"/>

# Panoramica della piattaforma DSC di Automazione di Azure #

## Che cos'è la piattaforma DSC di PowerShell? ##
DSC (Desired State Configuration) è una nuova piattaforma di gestione, disponibile in Windows PowerShell, che consente di gestire la configurazione degli host fisici e delle macchine virtuali mediante una sintassi dichiarativa di PowerShell.

La piattaforma DSC offre un set di estensioni del linguaggio di Windows PowerShell, nuovi cmdlet di Windows PowerShell e risorse utilizzabili per specificare in modo dichiarativo la configurazione desiderata per l'ambiente software. Consente anche di mantenere e gestire le configurazioni esistenti.

### Applicazioni pratiche ###
Di seguito sono elencati alcuni scenari di esempio in cui è possibile usare le risorse DSC predefinite per configurare e gestire un set di computer (noti anche come nodi di destinazione) in modo automatizzato:

- Abilitazione o disabilitazione di funzionalità e ruoli del server
- Gestione delle impostazioni del Registro di sistema
- Gestione di file e directory
- Avvio, arresto e gestione di processi e servizi
- Gestione di gruppi e account utente
- Distribuzione di nuovo software
- Gestione delle variabili di ambiente
- Esecuzione di script di Windows PowerShell
- Correzione di una configurazione che presenta deviazioni rispetto allo stato desiderato
- Individuare l'effettivo stato di configurazione in un determinato nodo

È anche possibile creare risorse personalizzate per configurare lo stato di qualsiasi applicazione o impostazione di sistema.


Per altre informazioni sulla piattaforma DSC di PowerShell, vedere l'articolo relativo alla [configurazione in un ambiente DevOps con tale piattaforma](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx).

##Che cos'è la piattaforma DSC di Automazione di Azure?##
La piattaforma DSC di Automazione di Azure si basa sugli elementi principali introdotti nella piattaforma DSC di PowerShell per garantire un'esperienza di gestione della configurazione ancora più semplice. Essa infatti introduce nella [piattaforma DSC di PowerShell](https://technet.microsoft.com/library/dn249912.aspx) <link> lo stesso livello di gestione attualmente offerto da Automazione di Azure per gli script di PowerShell.

La piattaforma DSC di Automazione di Azure consente di [creare e gestire configurazioni dello stato desiderato di PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importare [risorse DSC](https://technet.microsoft.com/library/dn282125.aspx) e generare configurazioni di nodi DSC (documenti MOF), interamente nel cloud. Questi elementi DSC verranno inseriti nel [server di pull DSC](https://technet.microsoft.com/library/dn249913.aspx) di Automazione di Azure in modo che i nodi di destinazione (ad esempio, computer fisici e macchine virtuali) nel cloud o in locale possano selezionarli, conformarsi allo stato desiderato specificato e segnalare ad Automazione di Azure la propria conformità allo stato desiderato.

Si preferisce guardare che leggere? Esaminare il video di seguito del maggio 2015, quando Automazione di Azure DSC è stato annunciato per la prima volta. **Nota:** mentre i concetti e il ciclo di vita descritti in questo video sono corretti, Automazione di Azure DSC ha fatto molti progressi dalla registrazione del video. È ora in anteprima pubblica, dispone di un'interfaccia utente più estesa nel portale di Azure e supporta funzionalità aggiuntive.

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

>[AZURE.IMPORTANT]Una configurazione dovrebbe includere un solo blocco di configurazione, con lo stesso nome della configurazione, nella piattaforma DSC di Automazione di Azure.


###Configurazione nodo###

Quando una configurazione DSC viene compilata, vengono prodotte una o più configurazioni nodo a seconda dei blocchi Node presenti nella configurazione. Una configurazione nodo corrisponde a un "documento MOF" (o "documento di configurazione" se si ha familiarità con la terminologia relativa alla piattaforma DSC di PowerShell) e rappresenta un "ruolo" (ad esempio, server Web o computer di lavoro) che uno o più nodi dovrebbero assumersi. I nomi delle configurazioni di nodi in Automazione di Azure DSC assumono la forma di "<Configuration-name>.<Node configuration-block-name>".

I nodi DSC di PowerShell riconoscono le configurazioni nodo da eseguire mediante metodi push o pull DSC. La piattaforma DSC di Automazione di Azure si basa sul metodo pull DSC, in cui i nodi richiedono al server di pull della piattaforma le configurazioni nodo da applicare. Poiché i nodi effettuano la richiesta alla piattaforma DSC di Automazione di Azure, possono essere protetti da firewall, avere tutte le porte in ingresso chiuse e così via. Necessitano solo dell'accesso in uscita a Internet.


###Nodo###

Un nodo DSC è qualsiasi computer in cui la configurazione è gestita da DSC e può corrispondere a una macchina virtuale di Azure oppure a un host fisico o a una macchina virtuale locale. I nodi eseguono le configurazioni nodo per diventare conformi e mantenere la conformità allo stato desiderato definito e possono anche segnalare a un server di reporting il proprio stato di configurazione e la propria conformità.

La piattaforma DSC di Automazione di Azure facilita il caricamento dei nodi per la gestione e consente di cambiare la configurazione assegnata a ciascun nodo sul lato server. In questo modo, la volta successiva che un nodo cercherà istruzioni nel server, assumerà un ruolo diverso e adatterà di conseguenza la propria configurazione. I nodi inoltre segnalano alla piattaforma il proprio stato e la conformità alla configurazione.


###Risorsa###
Le risorse DSC sono blocchi predefiniti utilizzabili per specificare una configurazione della piattaforma DSC (Desidered State Configuration) di Windows PowerShell. La piattaforma DSC viene fornita con un set di funzionalità predefinite per la configurazione di risorse come file e cartelle, funzionalità e ruoli del server, impostazioni del Registro di sistema, variabili di ambiente, servizi e processi. Per l'elenco completo delle risorse DSC predefinite e per informazioni su come usarle, vedere l'articolo relativo alle [risorse predefinite della piattaforma DSC di Windows PowerShell](https://technet.microsoft.com/library/dn249921.aspx).

È anche possibile importare risorse DSC come parte dei moduli di PowerShell per estendere il set delle risorse DSC predefinite. I nodi DSC recupereranno tramite pull le risorse non predefinite dal server di pull DSC se una configurazione nodo da eseguire include riferimenti a tali risorse. Per informazioni su come creare risorse personalizzate, vedere l'articolo relativo alla [creazione di risorse personalizzate della piattaforma DSC di Windows PowerShell](https://technet.microsoft.com/library/dn249927.aspx).

La piattaforma DSC di Automazione di Azure viene fornita con le stesse risorse predefinite della piattaforma DSC di PowerShell. È possibile aggiungere altre risorse ad Automazione di Azure importando moduli di PowerShell contenenti le risorse desiderate.


###Processo di compilazione###
Nella piattaforma DSC di Automazione di Azure un processo di compilazione è un'istanza di compilazione di una configurazione per creare una o più configurazioni nodo. Tali processi sono simili ai processi di Runbook di Automazione di Azure, con la differenza che non eseguono effettivamente alcuna attività tranne la creazione di configurazioni nodo. Qualsiasi configurazione nodo creata da un processo di compilazione viene inserita automaticamente nel server di pull della piattaforma DSC di Automazione di Azure e sovrascrive le rispettive versioni precedenti, se esistenti. Il nome di una configurazione nodo prodotta da un processo di compilazione è nel formato "<Configuration-name>.<Node configuration-block-name>". Ad esempio, compilando la configurazione seguente, verrà prodotta una sola configurazione nodo denominata "MyConfiguration.webserver":


![testo alternativo](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Le configurazioni possono essere pubblicate come i Runbook, ma in questo caso non vengono inseriti elementi DSC nel server di pull della piattaforma DSC di Automazione di Azure. Sono i processi di compilazione a eseguire tale operazione. Per altre informazioni sulla "pubblicazione" in Automazione di Azure, vedere [Pubblicazione di un runbook](https://msdn.microsoft.com/library/dn903765.aspx).

La piattaforma DSC di Automazione di Azure attualmente rende disponibili i cmdlet seguenti nel [modulo AzureResourceManager di PowerShell](https://msdn.microsoft.com/library/mt244122.aspx) per la gestione dei processi di compilazione:

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Ciclo di vita della piattaforma DSC di Automazione di Azure##
Passando da un account di automazione vuoto a un set gestito di nodi configurati correttamente si generano una serie di processi per la definizione delle configurazioni, l'attivazione di tali configurazioni in configurazioni di nodo e nodi di caricamento per DSC di automazione di Azure e le configurazioni di nodi. Nel diagramma seguente viene illustrato il ciclo di vita DSC di automazione di Azure:

![testo alternativo](./media/automation-dsc-overview/DSCLifecycle.png)


##Problemi noti:##

- In questo momento la piattaforma DSC di Automazione di Azure non supporta configurazioni DSC parziali o composite.

- Perché l'agente DSC di PowerShell per Windows possa comunicare con Automazione di Azure, deve essere installata la versione più recente di WMF 5. L'agente DSC di PowerShell per Linux attualmente non supporta la comunicazione con Automazione di Azure, ma presto verrà aggiornato.

- Automazione di Azure non supporta l'uso side-by-side di moduli di PowerShell. Questo significa che tutte le configurazioni in un account di automazione devono interagire con l'ultima versione di un modulo di PowerShell importato in tale account e con qualsiasi risorsa DSC PowerShell contenuta nel modulo e usata dalla configurazione.

- Il server di pull DSC di PowerShell tradizionale prevede che i file ZIP dei moduli vengono inseriti con il formato **NomeModulo\_Versione.zip**. Automazione di Azure prevede che i moduli di PowerShell vengano importati con nomi nel formato **NomeModulo.zip**. Per altre informazioni sul formato dei moduli di integrazione necessario per importare il modulo in Automazione di Azure, vedere [questo post di blog](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/).

- I moduli di PowerShell che specificano risorse DSC side-by-side all'interno del modulo usando il formato "versione per cartella" al momento non funzioneranno in Automazione di Azure.

- I moduli di PowerShell importati in Automazione di Azure non possono includere file con estensione doc o docx. Alcuni moduli di PowerShell contenenti risorse DSC includono file di questo tipo per rendere disponibile la Guida. È consigliabile rimuovere tali file dai moduli prima di eseguire l'importazione in Automazione di Azure.

- Quando un nodo viene inizialmente registrato in un account di Automazione di Azure o viene modificato in modo da essere mappato a una configurazione nodo diversa sul lato server, il relativo stato risulterà 'conforme' anche se non è effettivamente conforme alla nuova configurazione nodo a cui è stato mappato. Dopo che il nodo esegue il primo pull e invia il primo report, dopo la registrazione o la modifica del mapping a una configurazione nodo, il relativo stato può essere considerato attendibile.

- Quando si carica una macchina virtuale di Azure per la gestione nella piattaforma DSC di Automazione di Azure tramite `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` o l'estensione VM di tale piattaforma nel portale di anteprima di Azure, se la registrazione non riesce e viene visualizzato un messaggio che indica che il nome computer non è stato specificato e la directory di configurazione non dispone di file di configurazione, tenere presente che si tratta di un falso allarme e che la registrazione della macchina virtuale ha effettivamente avuto esito positivo. È possibile verificare se la registrazione è riuscita eseguendo il cmdlet `Get-AzureAutomationDscNode`.

- Quando si carica una VM di Azure per la gestione con Automazione di Azure DSC tramite `Register-AzureAutomationDscNode``Set-AzureVMExtension` o l'estensione della VM di Automazione di Azure DSC nel portale di anteprima di Azure, potrebbe essere necessaria fino a un'ora prima che la VM venga visualizzata come nodo DSC di automazione di Azure. Questo comportamento è causato dall'installazione di Windows Management Framework 5.0 nella macchina virtuale da parte dell'estensione DSC della VM di Azure, necessario per caricare la VM nella piattaforma DSC di Automazione di Azure.

- Dopo la registrazione, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. A questo punto, il protocollo di registrazione PowerShell DSC non può rinnovare automaticamente certificati quando è prossimo alla scadenza, pertanto è necessario registrare nuovamente i nodi dopo il periodo di un anno. Prima di registrare di nuovo, verificare che ogni nodo esegua Windows Management Framework 5.0 RTM. Se il certificato di autenticazione di un nodo scade, e se il nodo non è registrato, il nodo non sarà in grado di comunicare con l'automazione di Azure e si indicherà che ’Non risponde’. La registrazione viene eseguita nello stesso modo, con cui il nodo è stato registrato inizialmente. Una registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l’ora di scadenza del certificato comporterà un nuovo certificato che viene generato e utilizzato.

##Articoli correlati##

- [Cmdlet di Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
- [Prezzi di Azure Automation DSC](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Oct15_HO1-->