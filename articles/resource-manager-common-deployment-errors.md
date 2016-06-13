<properties
   pageTitle="Risolvere errori comuni durante la distribuzione di risorse in Azure | Microsoft Azure"
   description="Descrive come risolvere gli errori più comuni durante la distribuzione con Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager

Questo argomento illustra come risolvere alcuni errori comuni che possono verificarsi durante la distribuzione di risorse in Azure. È probabile che sia già stato visualizzato un messaggio di errore utile. In caso contrario o se sono necessari altri dettagli sulla causa dell'errore durante la distribuzione, vedere prima di tutto [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md) e quindi tornare a questo articolo per informazioni su come risolvere l'errore.

## Risorsa o modello non valido

Se viene visualizzato un errore indicante che una proprietà in una risorsa o un modello non è valido, potrebbe trattarsi di un carattere mancante nel modello. È facile incorrere in questo errore quando si usano le espressioni del modello perché l'espressione è racchiusa tra virgolette, JSON la convalida comunque e l'editor potrebbe non rilevare l'errore. Ad esempio, l'assegnazione di nome seguente per un account di archiviazione contiene un set di parentesi, tre funzioni, tre set di parentesi, un set di virgolette singole e una proprietà:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se non si fornisce tutta la sintassi corrispondente, il modello produce un valore molto diverso dal previsto.

A seconda della posizione del carattere mancante nel modello, viene visualizzato un errore indicante che il modello o una risorsa non è valida. L'errore può anche indicare che il processo di distribuzione non è riuscito a elaborare l'espressione del linguaggio del modello. Quando viene visualizzato questo tipo di errore, esaminare attentamente la sintassi dell'espressione.

## Il nome della risorsa esiste già

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

## Il percorso non è disponibile per la risorsa

Quando si specifica un percorso per una risorsa, è necessario usare uno dei percorsi in grado di supportarla. Prima di immettere un percorso per una risorsa, usare uno dei comandi seguenti per verificare che il percorso supporti il tipo di risorsa.

### PowerShell

Usare **Get-AzureRmResourceProvider** per ottenere le posizioni e i tipi supportati per un provider di risorse specifico.

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

Si otterrà un elenco dei tipi di risorse per tale provider di risorse.

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Per ottenere le località di un particolare tipo di risorsa è possibile usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Che restituisce le località supportate:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, è possibile usare **azure location list**. Poiché l'elenco dei percorsi può essere lungo e il numero di provider è elevato, è possibile usare gli strumenti per esaminare i provider e i percorsi prima di usare un percorso non ancora disponibile. Lo script seguente usa **jq** per individuare i percorsi in cui è disponibile il provider di risorse per le macchine virtuali di Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    
Che restituisce le località supportate:
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST

Per l'API REST, vedere [Ottenere informazioni su un provider di risorse](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## La quota è stata superata

Alcuni problemi potrebbero verificarsi quando una distribuzione supera una quota specifica per un gruppo di risorse, le sottoscrizioni, gli account o per altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una macchina virtuale con un numero di core superiore alla quantità consentita, si riceve un messaggio di errore che informa che la quota è stata superata. Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).

Per esaminare le quote della sottoscrizione per i core, è possibile usare il comando `azure vm list-usage` nell'interfaccia della riga di comando di Azure. L'esempio seguente mostra che la quota di core per un account della versione di valutazione gratuita è quattro:

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

Per altre informazioni sull'argomento, vedere [Controllo degli accessi in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

Oltre che dal controllo degli accessi in base al ruolo, le azioni di distribuzione possono essere limitate da criteri della sottoscrizione. Attraverso i criteri, l'amministratore può imporre convenzioni in tutte le risorse distribuite nella sottoscrizione. Ad esempio, un amministratore può richiedere che venga fornito un valore di tag specifico per un tipo di risorsa. Se non vengono soddisfatti i requisiti dei criteri, si riceve un messaggio di errore durante la distribuzione. Per altre informazioni sui criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## Verificare la registrazione del provider di risorse

Le risorse vengono gestite dai provider di risorse ed è necessario registrare un account o una sottoscrizione per usare un provider specifico. La maggior parte dei provider, ma non tutti, vengono registrati automaticamente dal portale di Azure o dall'interfaccia della riga di comando che si sta usando.

### PowerShell

Per visualizzare lo stato della registrazione, usare **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Che restituisce tutti i provider di risorse disponibili e lo stato della registrazione:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Per registrare un provider, usare **Register-AzureRmResourceProvider** e specificare il nome del provider di risorse da registrare.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Viene richiesto di confermare la registrazione e quindi viene restituito uno stato.

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Interfaccia della riga di comando di Azure

Per vedere se il provider è registrato per l'uso dell'interfaccia della riga di comando di Azure, usare il comando `azure provider list` (di seguito è riportato un esempio troncato dell'output).

    azure provider list
        
Che restituisce tutti i provider di risorse disponibili e lo stato della registrazione:
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

Per registrare un provider di risorse, usare il comando `azure provider register` e specificare lo *spazio dei nomi* per la registrazione.

    azure provider register Microsoft.Cdn

### API REST

Per ottenere lo stato di registrazione, vedere [Ottenere informazioni su un provider di risorse](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Per registrare un provider, vedere [Registrare una sottoscrizione con un provider di risorse](https://msdn.microsoft.com/library/azure/dn790548.aspx).

## Errori dell'estensione script personalizzata

Se si verifica un errore dell'estensione script personalizzata durante la distribuzione di una macchina virtuale, vedere [Risoluzione degli errori delle estensioni di macchina virtuale Windows di Azure ](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md) o [Risoluzione degli errori delle estensioni della macchina virtuale Linux di Azure](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).

## Errori di provisioning e di allocazione delle macchine virtuali

Se si sta tentando di distribuire una macchina virtuale e si verificano errori di provisioning dell'immagine del sistema operativo o di allocazione, vedere [Risolvere i problemi della distribuzione Resource Manager con la creazione di una nuova macchina virtuale Windows in Azure](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md) e [Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale Windows in Azure](./virtual-machines/virtual-machines-windows-allocation-failure.md).

## Comprendere quando una distribuzione è pronta 

Azure Resource Manager segnala l'esito positivo in una distribuzione quando tutti i provider vengono restituiti correttamente dalla distribuzione. Tuttavia, questo non implica necessariamente che il gruppo di risorse sia "attivo e disponibile per gli utenti". Ad esempio, per una distribuzione potrebbe essere necessario scaricare aggiornamenti, attendere risorse esterne al modello oppure installare script complessi o altre attività eseguibili che Azure non rileva perché non sono attività registrate da un provider. In questi casi potrebbe trascorrere altro tempo prima che le risorse siano effettivamente pronte per l'uso. Di conseguenza, è necessario che lo stato di distribuzione venga completato correttamente prima di poter usare la distribuzione.

Per impedire che Azure segnali lo stato di completamento della distribuzione, tuttavia, è possibile creare uno script personalizzato per il modello personalizzato usando, ad esempio, [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), che consente di monitorare la conformità dell'intera distribuzione a livello di sistema e di restituire lo stato di completamento corretto solo quando gli utenti possono interagire con l'intera distribuzione. Per assicurarsi che l'estensione specificata sia l'ultima a essere eseguita, usare la proprietà **dependsOn** nel modello.

## Passaggi successivi

- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md).
- Per risolvere errori di Remote Desktop Protocol in una macchina virtuale basata su Windows, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md).
- Per risolvere errori di Secure Shell in una macchina virtuale basata su Linux, vedere [Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

<!---HONumber=AcomDC_0601_2016-->