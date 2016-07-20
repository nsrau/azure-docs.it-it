
<properties
	pageTitle="Interfaccia della riga di comando di Azure con Resource Manager | Microsoft Azure"
	description="Usare l'interfaccia della riga di comando di Azure per distribuire più risorse come gruppo di risorse"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="danlep"/>

# Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager

> [AZURE.SELECTOR]
- [Portale](azure-portal/resource-group-portal.md)
- [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nodo](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


Questo articolo illustra i metodi comuni per creare e gestire risorse di Azure usando l'interfaccia della riga di comando di Azure in modalità Azure Resource Manager.

>[AZURE.NOTE] Per creare e gestire risorse di Azure dalla riga di comando, è necessaria una sottoscrizione di Azure ([fare clic qui per un account Azure gratuito](https://azure.microsoft.com/free/)). È anche necessario [installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e [accedere per usare le risorse di Azure associate all'account](xplat-cli-connect.md). Se queste operazioni sono già state eseguite, si è pronti per proseguire.

## Risorse di Azure

Usare Gestione risorse di Azure per creare e gestire un gruppo di _risorse_ (entità gestite dall'utente quali una macchina virtuale, un server di database, un database o un sito Web) come una singola unità logica o _gruppo di risorse_.

Uno dei vantaggi di Azure Resource Manager è che permette di creare le risorse di Azure in modo _dichiarativo_, descrivendo la struttura e le relazioni di un gruppo di risorse distribuibile in *modelli* JSON. Il modello identifica i parametri che possono essere completati inline quando si esegue un comando oppure archiviati in un file di parametri JSON (JavaScript Object Notation) distinto. Ciò consente di creare facilmente nuove risorse usando lo stesso modello e semplicemente fornendo parametri diversi. Un modello che crea un sito Web disporrà ad esempio di parametri per il nome del sito, per l'area in cui verrà inserito e altre impostazioni comuni.

Quando un modello viene usato per modificare o creare un gruppo, viene creata una _distribuzione_, che viene quindi applicata al gruppo. Per altre informazioni su Gestione risorse, vedere l'articolo relativo alla [panoramica di Gestione risorse di Azure](resource-group-overview.md).

Dopo aver creato una distribuzione, è possibile gestire le singole risorse in modo imperativo dalla riga di comando, in modo analogo a quello usato nel modello di distribuzione classica. Usare ad esempio i comandi dell'interfaccia della riga di comando in modalità Resource Manager per avviare, arrestare o eliminare risorse, ad esempio [macchine virtuali di Azure Resource Manager](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

## Autenticazione

L'uso di Azure Resource Manager con l'interfaccia della riga di comando di Azure attualmente richiede l'autenticazione in Microsoft Azure con il comando `azure login` e quindi l'immissione di un account gestito da Azure Active Directory, che può essere un account aziendale o dell'istituto di istruzione o un account Microsoft. L'autenticazione tramite un certificato installato mediante un file con estensione publishsettings non funziona in questa modalità.

Per altre informazioni sull'autenticazione a Microsoft Azure, vedere l'argomento relativo alla [connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando si usa un account gestito da Azure Active Directory, è anche possibile usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse di Azure e il loro utilizzo. Per maggiori dettagli, vedere [Controllo di accesso in base al ruolo Azure](./active-directory/role-based-access-control-configure.md).

## Impostare la modalità Resource Manager

Poiché l'interfaccia della riga di comando non è in modalità Resource Manager per impostazione predefinita, usare il comando seguente per abilitare i comandi di Resource Manager dell'interfaccia della riga di comando di Azure.

	azure config mode arm

## Ricercare le posizioni

La maggior parte dei comandi di gestione risorse di Azure necessita di un percorso valido da cui creare o trovare una risorsa. È possibile trovare tutte le posizioni disponibili per le diverse risorse di Azure usando il comando seguente.

	azure location list

Questo comando elenca le aree di Azure disponibili, ad esempio "Stati Uniti occidentali", "Stati Uniti orientali" e così via. Per informazioni dettagliate sui provider di risorse disponibili e sulle posizioni in cui sono disponibili, usare il comando `azure provider list` seguito dal comando `azure provider show`. Il comando seguente, ad esempio, elenca le posizioni del servizio contenitore di Azure:

    azure provider show Microsoft.ContainerService 

## Creare un gruppo di risorse

Un gruppo di risorse è un raggruppamento logico di risorse, ad esempio una rete, risorse di archiviazione e risorse di calcolo. Quasi tutti i comandi in modalità Resource Manager richiedono un gruppo di risorse. È ad esempio possibile creare un gruppo di risorse denominato _testRG_ nell'area Stati Uniti occidentali usando il comando seguente.

	azure group create -n "testRG" -l "West US"

La distribuzione al gruppo di risorse *testRG* verrà eseguita in un secondo momento, quando si usa un modello per avviare una VM Ubuntu. Dopo aver creato un gruppo di risorse, è possibile aggiungere risorse come macchine virtuali e reti o risorse di archiviazione.


## Utilizzo di modelli di gruppo di risorse

### Individuare e configurare un modello di gruppo di risorse

Quando si usano i modelli, è possibile [crearne uno personalizzato](resource-group-authoring-templates.md) oppure usare uno dei [modelli di avvio rapido](https://azure.microsoft.com/documentation/templates/) creati dalla community, disponibili anche in [GitHub](https://github.com/Azure/azure-quickstart-templates).

La creazione di un nuovo modello esula dall'ambito di questo articolo. Per iniziare verrà quindi usato il modello _101-simple-vm-from-image_ disponibile nei [modelli di avvio rapido](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). Per impostazione predefinita, verrà creata una singola macchina virtuale Ubuntu 14.04.2-LTS in una nuova rete virtuale con una singola subnet. È necessario solo specificare un gruppo di risorse e i pochi parametri seguenti per usare questo modello:

* Nome utente amministratore per la VM = `adminUsername`
* Password = `adminPassword`
* Nome di dominio per la VM = `dnsLabelPrefix`

>[AZURE.TIP] Questi passaggi illustrano solo un modo per utilizzare un modello di macchina virtuale con l’interfaccia della linea di comando di Azure. Per altri esempi, vedere [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

1. Fare clic sul collegamento relativo ad altre informazioni con GitHub per scaricare i file azuredeploy.json e azuredeploy.parameters.json da [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) in una cartella di lavoro nel computer locale. Assicurarsi di selezionare il formato _raw_di ogni file in GitHub.

2. Aprire il file azuredeploy.parameters.json in un editor di testo e immettere i valori dei parametri appropriati per l'ambiente (lasciando invariato il valore **ubuntuOSVersion**).

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  Dopo aver modificato i parametri di distribuzione, sarà possibile distribuire la VM Ubuntu nel gruppo di risorse *testRG* creato in precedenza. Scegliere un nome per la distribuzione (*testRGDeploy* in questo esempio) e quindi usare il comando seguente per avviarla.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGDeploy
	```

	L’opzione `-e` specifica il file azuredeploy.parameters.json che è stato modificato nel passaggio precedente. L'opzione `-f` specifica il file modello azuredeploy.json.

	Questo comando restituirà OK dopo che la distribuzione viene caricata, ma prima che venga applicata la distribuzione alle risorse del gruppo.

4. Per verificare lo stato della distribuzione, usare il comando seguente.

	```
	azure group deployment show testRG testRGDeploy
	```

	La voce **ProvisioningState** mostra lo stato della distribuzione.

	Se la distribuzione ha avuto esito positivo, verrà visualizzato un output simile al seguente.

		azure-cli@0.8.0:/# azure group deployment show testRG testRGDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsLabelPrefix    String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Se ci si accorge che la configurazione non è corretta ed è necessario arrestare una distribuzione a lunga esecuzione, usare il comando seguente.
	>
	> `azure group deployment stop testRG testRGDeploy`
	>
	> Se non si fornisce un nome di distribuzione, ne viene creato uno automaticamente in base al nome del file di modello. Viene restituito come parte dell'output del comando `azure group create`.

	Ora è possibile collegare SSH alla macchina virtuale, utilizzando il nome di dominio specificato. Quando avviene il collegamento con la macchina virtuale, è necessario utilizzare un nome di dominio completo nel formato `<domainName>.<region>.cloudapp.azure.com`, ad esempio `MyDomainName.westus.cloudapp.azure.com`.

5. Per visualizzare il gruppo, usare il comando seguente:

		azure group show testRG

	Questo comando restituisce informazioni relative alle risorse nel gruppo. Se sono presenti più gruppi, usare il comando `azure group list` per recuperare un elenco di nomi di gruppi e quindi usare il comando `azure group show` per visualizzare i dettagli di un gruppo specifico.

È inoltre possibile utilizzare un modello direttamente da [GitHub](https://github.com/Azure/azure-quickstart-templates), invece di scaricarne uno nel computer. A tale scopo, passare l'URL al file azuredeploy.json per il modello nel comando usando l'opzione **--template-uri**. Per ottenere l’URL, aprire zuredeploy.json in GitHub in modalità _raw_, e copiare l'URL che viene visualizzato nella barra degli indirizzi del browser. È quindi possibile utilizzare questo URL direttamente per creare una distribuzione, utilizzando un comando simile al seguente.

	azure group deployment create testRG testRGDeploy --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Viene chiesto di immettere i parametri del modello necessari.

> [AZURE.NOTE] È importante aprire il modello json in modalità _raw_. L'URL che viene visualizzato nella barra degli indirizzi del browser è diverso da quello visibile in modalità normale. Per aprire il file in modalità_raw_quando si visualizza il file su GitHub, nell'angolo superiore destro fare clic su**Raw**.

## Usare le risorse

Mentre i modelli consentono di dichiarare modifiche a livello di interi gruppi in ambito di configurazione, è talvolta necessario lavorare su una risorsa specifica. A tale scopo, è possibile usare i comandi `azure resource`.

> [AZURE.NOTE] Quando si usano comandi `azure resource` diversi da `list`, è necessario specificare la versione dell'API della risorsa usata tramite il parametro `-o`. Se non si è certi della versione dell'API da utilizzare, consultare il file di modello e trovare il campo **apiVersion** relativo alla risorsa.

1. Per elencare tutte le risorse in un gruppo, usare il comando seguente.

		azure resource list testRG

2. Per visualizzare una singola risorsa all'interno del gruppo, ad esempio la VM denominata *MyUbuntuVM*, usare un comando come il seguente.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

	Notare il parametro **Microsoft.Compute/virtualMachines**. Questo indica il tipo di risorsa per il quale si stanno richiedendo informazioni. Se si esamina il file del modello scaricato in precedenza, si noterà che lo stesso valore viene usato per definire il tipo di risorsa di macchina virtuale descritta nel modello.

	Questo comando restituisce informazioni relative alla macchina virtuale.

3. Quando si visualizzano i dettagli su una risorsa, è spesso utile utilizzare il parametro`--json`. In questo modo l'output è più leggibile poiché alcuni valori sono strutture nidificate o raccolte. L'esempio seguente illustra come restituire i risultati del comando **show** come un documento JSON.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] È possibile salvare i dati JSON nel file usando il carattere &gt; per indirizzare l'output a un file. Ad esempio:
	>
	> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Per eliminare una risorsa esistente, usare un comando simile al seguente.

		azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

## Visualizzare i log dei gruppi

Per visualizzare le informazioni registrate sulle operazioni eseguite su un gruppo, usare il comando `azure group log show`. Per impostazione predefinita, questo comando restituisce l'ultima operazione eseguita sul gruppo. Per visualizzare tutte le operazioni, usare il parametro `--all` facoltativo. Per l'ultima distribuzione, usare `--last-deployment`. Per una distribuzione specifica, usare `--deployment` e specificare il nome della distribuzione. L'esempio seguente restituisce un log di tutte le operazioni eseguite nel gruppo *testRG*.

	azure group log show testRG --all
    
## Esportare un gruppo di risorse come modello

È possibile visualizzare il modello di Resource Manager per un gruppo di risorse esistente. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.

2. Per acquisire familiarità con la sintassi del modello, esaminare il codice JSON che rappresenta la soluzione.

Usando l'interfaccia della riga di comando di Azure, è possibile esportare un modello che rappresenta lo stato corrente del gruppo di risorse oppure scaricare il modello usato per una distribuzione specifica.

* **Esportare il modello per un gruppo di risorse**: questa operazione è utile quando sono state apportate modifiche a un gruppo di risorse ed è necessario recuperare la rappresentazione JSON del rispettivo stato corrente. Il modello generato, tuttavia, contiene solo un numero minimo di parametri e nessuna variabile. La maggior parte dei valori del modello è hardcoded. Prima di distribuire il modello generato, è possibile che si voglia convertire altri valori in parametri, per potere personalizzare la distribuzione per diversi ambienti.

    Per esportare il modello per un gruppo di risorse in una directory locale, eseguire il comando `azure group export`, come illustrato nell'esempio seguente. Usare una directory locale appropriata per l'ambiente del sistema operativo in uso.

        azure group export testRG ~/azure/templates/

* **Scaricare il modello per una distribuzione specifica**: questa operazione è utile quando è necessario visualizzare il modello effettivo usato per distribuire le risorse. Il modello includerà tutte le variabili e tutti i parametri definiti per la distribuzione originale. Se, tuttavia, un utente dell'organizzazione ha modificato il gruppo di risorse in modo diverso da quanto definito nel modello, questo modello non rappresenterà lo stato corrente del gruppo di risorse.

    Per scaricare in una directory locale il modello usato per una distribuzione specifica, eseguire il comando `azure group deployment template download`.

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] La funzionalità di esportazione del modello è disponibile in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si prova a esportare un modello, è possibile che venga visualizzato un errore che indica che alcune risorse non sono state esportate. Se necessario, è possibile definire manualmente queste risorse nel modello dopo averlo scaricato.

## Passaggi successivi

* Per informazioni sull'uso di Gestione risorse con Azure PowerShell, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).
* Per informazioni sull'uso di Azure Resource Manager dal portale di Azure, vedere [Gestire le risorse di Azure mediante il portale](./azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0706_2016-->