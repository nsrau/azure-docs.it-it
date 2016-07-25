<properties
   pageTitle="Risolvere errori comuni durante la distribuzione di risorse in Azure | Microsoft Azure"
   description="Descrive come risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="errore di distribuzione, distribuzione di azure, distribuire in azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/06/2016"
   ms.author="tomfitz"/>

# Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager

Questo argomento illustra come risolvere alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure. Se sono necessarie altre informazioni sulla causa dell'errore durante la distribuzione, vedere prima di tutto [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md) e quindi tornare a questo articolo per informazioni su come risolvere l'errore.

## Risorsa o modello non valido

Se viene visualizzato un errore indicante che una proprietà in una risorsa o un modello non è valido, potrebbe trattarsi di un carattere mancante nel modello. È facile incorrere in questo errore quando si usano le espressioni del modello perché l'espressione è racchiusa tra virgolette, JSON la convalida comunque e l'editor potrebbe non rilevare l'errore. Ad esempio, l'assegnazione di nome seguente per un account di archiviazione contiene un set di parentesi, tre funzioni, tre set di parentesi, un set di virgolette singole e una proprietà:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se non si fornisce tutta la sintassi corrispondente, il modello produce un valore molto diverso dal previsto.

A seconda della posizione del carattere mancante nel modello, viene visualizzato un errore indicante che il modello o una risorsa non è valida. L'errore può anche indicare che il processo di distribuzione non è riuscito a elaborare l'espressione del linguaggio del modello. Quando viene visualizzato questo tipo di errore, esaminare attentamente la sintassi dell'espressione.

## Resource name already exists or is already used by another resource (Il nome della risorsa esiste già o è già usato da un'altra risorsa)

Per alcune risorse, in particolare gli account di archiviazione, i server di database e i siti Web, è necessario specificare un nome che sia univoco all'interno di Azure. È possibile creare un nome univoco concatenando la convenzione di denominazione con il risultato della funzione [uniqueString](resource-group-template-functions.md#uniquestring).

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

## Non si riesce a trovare la risorsa durante la distribuzione

Azure Resource Manager consente di ottimizzare la distribuzione creando risorse in parallelo, quando ciò è possibile. Se una risorsa deve essere distribuita dopo un'altra, è necessario usare l'elemento **dependsOn** nel modello per creare una dipendenza dall'altra risorsa. Ad esempio, quando si distribuisce un'app Web deve esistere il piano di servizio app. Se non è stato specificato che l'app Web dipende dal piano di servizio app, Azure Resource Manager crea entrambe le risorse nello stesso momento. Si riceve un messaggio di errore che informa che la risorsa del piano di servizio app non è stata trovata, perché non esiste al momento del tentativo di impostare una proprietà nell'app Web. Per prevenire questo errore è possibile impostare la dipendenza nell'app Web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## Could not find member 'copy' on object (Non è stato possibile trovare il membro 'copia' nell'oggetto)

Questo errore viene visualizzato quando è stato applicato l'elemento **copy** a una parte del modello che non supporta questo elemento. È possibile applicare l'elemento copy solo a un tipo di risorsa. Copia non è possibile applicare copy a una proprietà all'interno di un tipo di risorsa. Ad esempio, si applica copy a una macchina virtuale, ma non è possibile applicarlo ai dischi del sistema operativo per una macchina virtuale. In alcuni casi, è possibile convertire una risorsa figlio in una risorsa padre per creare un ciclo di copy. Per altre informazioni sull'uso di copy, vedere [Creare più istanze di risorse in Azure Resource Manager](resource-group-create-multiple.md).

## SKU not available (Non disponibile)

Quando si distribuisce una risorsa, in genere una macchina virtuale, è possibile che venga visualizzato il codice di errore e il messaggio di errore seguenti:

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato. Per risolvere il problema sono disponibili due opzioni:

1.	Accedere al portale e iniziare ad aggiungere una nuova risorsa tramite l'interfaccia utente. Quando si impostano i valori, verranno visualizzati gli SKU disponibili per tale risorsa.

    ![sku disponibili](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	Se non si riesce a trovare uno SKU appropriato in tale area o un'area alternativa che soddisfi le esigenze aziendali, è possibile visitare la pagina del [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).


## No registered provider found (Non è stato trovato un provider registrato)

Quando si distribuisce una risorsa, è possibile che venga visualizzato il codice di errore e il messaggio seguenti:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

Questo errore viene visualizzato per uno di questi tre motivi:

1. Il percorso non è supportato per il tipo di risorsa
2. La versione dell'API non è supportata per il tipo di risorsa
3. Il provider di risorse non è stato registrato per la sottoscrizione

Il messaggio di errore dovrebbe fornire suggerimenti per le versioni di API e i percorsi supportati. È possibile modificare il modello impostando uno dei valori suggeriti. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando. Se non è mai stato usato un provider di risorse specifico, potrebbe essere necessario registrarlo. È possibile trovare altre informazioni sui provider di risorse con i comandi seguenti.

### PowerShell

Per visualizzare lo stato della registrazione, usare **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Per registrare un provider, usare **Register-AzureRmResourceProvider** e specificare il nome del provider di risorse da registrare.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Per ottenere i percorsi supportati per un tipo di risorsa particolare è possibile usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Per ottenere le versioni di API supportate per un tipo di risorsa particolare è possibile usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Interfaccia della riga di comando di Azure

Per vedere se il provider è registrato, usare il comando `azure provider list`.

    azure provider list

Per registrare un provider di risorse, usare il comando `azure provider register` e specificare lo *spazio dei nomi* per la registrazione.

    azure provider register Microsoft.Cdn

Per visualizzare le versioni di API e i percorsi supportati per un provider di risorse, usare:

    azure provider show -n Microsoft.Compute --json > compute.json

## La quota è stata superata

Alcuni problemi potrebbero verificarsi quando una distribuzione supera una quota specifica per un gruppo di risorse, le sottoscrizioni, gli account o per altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una macchina virtuale con un numero di core superiore alla quantità consentita, si riceve un messaggio di errore che informa che la quota è stata superata. Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).

Per esaminare le quote per i core della sottoscrizione, è possibile usare il comando `azure vm list-usage` nell'interfaccia della riga di comando di Azure. L'esempio seguente mostra che la quota di core per un account della versione di valutazione gratuita è quattro:

    azure vm list-usage

Che restituisce:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se si tenta di distribuire un modello che crea più di 4 core nell'area Stati Uniti occidentali per la sottoscrizione precedente, si otterrà un errore di distribuzione che potrebbe essere simile alla seguente (nel portale o esaminando i log di distribuzione):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

In alternativa, in PowerShell è possibile usare il cmdlet **Get-AzureRmVMUsage**.

    Get-AzureRmVMUsage

Che restituisce:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

In questi casi, si deve accedere al portale e rivolgersi all'assistenza per richiedere l'aumento della quota per l'area di destinazione della distribuzione.

> [AZURE.NOTE] Tenere presente che per i gruppi di risorse, la quota è riferita alle singole aree e non all'intera sottoscrizione. Se è necessario distribuire 30 core nell'area Stati Uniti occidentali, è necessario richiedere 30 core di gestione delle risorse per Stati Uniti occidentali. Se è necessario distribuire 30 core in qualsiasi area a cui si ha accesso, è necessario richiedere 30 core di gestione delle risorse per tutte le aree.


## L'autorizzazione non è riuscita

Si può ricevere un messaggio di errore durante la distribuzione perché l'account o l'entità servizio che prova a distribuire le risorse non ha l'accesso necessario per eseguire tali azioni. Azure Active Directory consente all'utente o all'amministratore di controllare con un'elevata precisione quali identità possono accedere e a quali risorse. Ad esempio, se l'account è assegnato al ruolo Lettore non può creare nuove risorse. In tal caso, viene visualizzato un messaggio di errore che indica che l'autorizzazione non è riuscita.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse di Azure Active Directory](./active-directory/role-based-access-control-configure.md).

Oltre che dal controllo degli accessi in base al ruolo, le azioni di distribuzione possono essere limitate da criteri della sottoscrizione. Attraverso i criteri, l'amministratore può imporre convenzioni in tutte le risorse distribuite nella sottoscrizione. Ad esempio, un amministratore può richiedere che venga fornito un valore di tag specifico per un tipo di risorsa. Se non vengono soddisfatti i requisiti dei criteri, si riceve un messaggio di errore durante la distribuzione. Per altre informazioni sui criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## Risoluzione dei problemi delle macchine virtuali

| Errore | Articoli |
| -------- | ----------- |
| Errori dell'estensione script personalizzata | [Risoluzione degli errori delle estensioni della macchina virtuale Windows di Azure](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />o<br />[Risoluzione degli errori delle estensioni della macchina virtuale Linux di Azure](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Errori di provisioning dell'immagine del sistema operativo | [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />o<br />[Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Linux in Azure](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| Errori di allocazione | [Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Windows in Azure](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />o<br />[Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Linux in Azure](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Errori SSH (Secure Shell) errori durante il tentativo di connessione | [Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Errori di connessione all'applicazione in esecuzione nella macchina virtuale | [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md) (Windows)<br />o<br />[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) (Linux) |
| Errori di connessione Desktop remoto | [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Errori di connessione risolti con la ridistribuzione | [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Errori del servizio cloud | [Risolvere eventuali problemi di distribuzione dei servizi cloud](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## Risoluzione dei problemi di altri servizi

La tabella seguente non include un elenco completo di argomenti relativi alla risoluzione dei problemi per Azure. È incentrata invece sui problemi relativi alla distribuzione o alla configurazione delle risorse. Se occorre assistenza per la risoluzione dei problemi in fase di esecuzione di una risorsa, vedere la documentazione relativa al servizio di Azure specifico.

| Service | Articolo |
| -------- | -------- |
| Automazione | [Suggerimenti sulla risoluzione dei problemi relativi agli errori comuni in Automazione di Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Microsoft Azure Stack troubleshooting (Risoluzione dei problemi di Microsoft Azure Stack)](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Web Apps and Azure Stack (App Web e Azure Stack)](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Data factory | [Risolvere i problemi di Data factory](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [Risolvere i problemi comuni quando si distribuiscono servizi in Azure Service Fabric](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Site Recovery | [Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Archiviazione | [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Risoluzione dei problemi di distribuzione del dispositivo StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Database SQL | [Risoluzione dei problemi di connessione al database SQL di Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [Risoluzione dei problemi relativi a SQL Data Warehouse di Azure](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## Comprendere quando una distribuzione è pronta

Azure Resource Manager segnala l'esito positivo in una distribuzione quando tutti i provider vengono restituiti correttamente dalla distribuzione. Tuttavia, questo non implica necessariamente che il gruppo di risorse sia "attivo e disponibile per gli utenti". Ad esempio, per una distribuzione potrebbe essere necessario scaricare aggiornamenti, attendere risorse esterne al modello oppure installare script complessi o altre attività eseguibili che Azure non rileva perché non sono attività registrate da un provider. In questi casi potrebbe trascorrere altro tempo prima che le risorse siano effettivamente pronte per l'uso. Di conseguenza, è necessario che lo stato di distribuzione venga completato correttamente prima di poter usare la distribuzione.

Per impedire che Azure segnali lo stato di completamento della distribuzione, tuttavia, è possibile creare uno script personalizzato per il modello personalizzato usando, ad esempio, [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), che consente di monitorare la conformità dell'intera distribuzione a livello di sistema e di restituire lo stato di completamento corretto solo quando gli utenti possono interagire con l'intera distribuzione. Per assicurarsi che l'estensione specificata sia l'ultima a essere eseguita, usare la proprietà **dependsOn** nel modello.

## Passaggi successivi

- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0713_2016-->