<properties
	pageTitle="Interfaccia della riga di comando di Azure con Gestione risorse | Microsoft Azure"
	description="Utilizzare l’interfaccia della riga di comando di Azure per Mac, Linux e Windows Azure per distribuire più risorse come un gruppo di risorse."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>

# Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



In questo articolo viene descritto come creare e gestire le risorse di Azure tramite l’interfaccia della riga di comando (CLI) di Azure per Mac, Linux e Windows nella modalità di Gestione risorse di Azure.

>[AZURE.NOTE]Per creare e gestire risorse di Azure tramite la riga di comando, sarà necessario un account Azure ([per ottenere una versione di valutazione gratuita, fare clic qui](http://azure.microsoft.com/pricing/free-trial/)). Sarà inoltre necessario [installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e [accedere per usare le risorse di Azure associate al proprio account](xplat-cli-connect.md). Se queste operazioni sono già state eseguite, si è pronti per proseguire.

## Risorse di Azure

Usare Gestione risorse di Azure per creare e gestire un gruppo di _risorse_ (entità gestite dall'utente quali una macchina virtuale, un server di database, un database o un sito Web) come una singola unità logica o _gruppo di risorse_.

Un vantaggio della Gestione risorse di Azure, è che è possibile creare le risorse di Azure in un modo _dichiarativo_: si descrivono la struttura e le relazioni di un gruppo di risorse distribuibile in *modelli* JSON. Il modello individua i parametri che possono essere completati inline quando si esegue un comando oppure essere archiviati in un file JSON azuredeploy-parameters.json a parte. Ciò consente di creare facilmente nuove risorse usando lo stesso modello e semplicemente fornendo parametri diversi. Un modello che crea un sito Web disporrà ad esempio di parametri per il nome del sito, per l'area in cui verrà inserito e altre impostazioni comuni.

Quando un modello viene usato per modificare o creare un gruppo, viene creata una _distribuzione_, che viene quindi applicata al gruppo. Per altre informazioni su Gestione risorse, vedere l'articolo relativo alla [panoramica di Gestione risorse di Azure](../resource-group-overview.md).

Dopo aver creato una distribuzione, è possibile gestire le singole risorse in modo imperativo nella riga di comando, in modo analogo a quello del modello di distribuzione classica (Gestione dei servizi). Ad esempio, utilizzare i comandi CLI di Gestione risorse di Azure per avviare, arrestare o eliminare risorse, ad esempio [le macchine virtuali della Gestione risorse di Azure](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

## Autenticazione

L'uso di Gestione risorse di Azure tramite l'interfaccia della riga di comando di Azure richiede l'autenticazione in Microsoft Azure con un account aziendale o dell'istituto di istruzione (un account aziendale) o un account Microsoft (a partire dalla versione 0.9.10 dell’interfaccia della riga di comando). L'autenticazione tramite un certificato installato mediante un file con estensione publishsettings non funziona in questa modalità.

Per altre informazioni sull'autenticazione a Microsoft Azure, vedere l'argomento relativo alla [connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

>[AZURE.NOTE]Quando si usa un account aziendale o dell'istituto di istruzione, che viene gestito da Azure Active Directory, è anche possibile usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso e l'uso delle risorse di Azure. Per informazioni dettagliate, vedere l'articolo relativo alla [gestione e controllo dell'accesso alle risorse](resource-group-rbac.md).

## Impostare la modalità di gestione risorse di Azure

Poiché la modalità Gestione risorse non è abilitata per impostazione predefinita, usare il comando seguente per abilitare i comandi di gestione risorse dell'interfaccia della riga di comando di Azure.

	azure config mode arm

>[AZURE.NOTE]La modalità Gestione risorse di Azure e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

## Ricercare le posizioni

La maggior parte dei comandi di gestione risorse di Azure necessita di un percorso valido da cui creare o trovare una risorsa. È possibile trovare tutte le posizioni disponibili per le diverse risorse di Azure usando il comando seguente.

	azure location list

Elenca le risorse di Azure e le aree di Azure in cui queste sono disponibili, ad esempio "Stati Uniti occidentali", "Stati Uniti orientali" e così via.

## Creare un gruppo di risorse

Un gruppo di risorse è un raggruppamento logico di risorse di rete, di archiviazione e di altro tipo. Quasi tutti i comandi in modalità di gestione risorse di Azure necessitano di un gruppo di risorse. È possibile creare un gruppo di risorse denominato_testrg_, ad esempio, utilizzando il comando seguente.

	azure group create -n "testRG" -l "West US"

È quindi possibile iniziare ad aggiungere risorse al gruppo e usarlo per configurare una risorsa come ad esempio una nuova macchina virtuale.


## Utilizzo di modelli di gruppo di risorse

### Individuare e configurare un modello di gruppo di risorse

Quando si lavora con i modelli, è possibile [crearne uno personalizzato](resource-group-authoring-templates.md) oppure usare uno dei modelli dalla [Raccolta modelli](https://azure.microsoft.com/documentation/templates/) che sono disponibili anche in [GitHub](https://github.com/Azure/azure-quickstart-templates).

La creazione di un nuovo modello esula dall'ambito di questo articolo, quindi iniziare con l’utilizzo del modello _101-simple-vm-from-image_ disponibile da [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm). Per impostazione predefinita, verrà creata una macchina virtuale LTS di Ubuntu 4.04.2 in una nuova rete virtuale con una singola subnet nell’area degli Stati Uniti occidentali. È necessario solo specificare i pochi parametri seguenti per utilizzare questo modello:

* Un nome dell'account di archiviazione univoco.
* Un nome utente dell'amministratore per la macchina virtuale
* Una password
* Un nome di dominio per la macchina virtuale

>[AZURE.TIP]Questi passaggi illustrano solo un modo per utilizzare un modello di macchina virtuale con l’interfaccia della linea di comando di Azure. Per altri esempi, vedere [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

1. Scaricare i file .azuredeploy.json e .azuredeploy.parameters.json da [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) a una cartella di lavoro nel computer locale.

2. Aprire il file azuredeploy.parameters.json in un editor di testo e immettere i valori dei parametri appropriati per l'ambiente (lasciando invariato il valore **ubuntuOSVersion**).

		{
	  	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  	"contentVersion": "1.0.0.0",
	  	"parameters": {
		    "newStorageAccountName": {
		      "value": "MyStorageAccount"
		    },
		    "adminUsername": {
		      "value": "MyUserName"
		    },
		    "adminPassword": {
		      "value": "MyPassword"
		    },
		    "dnsNameForPublicIP": {
		      "value": "MyDomainName"
		    },
		    "ubuntuOSVersion": {
		      "value": "14.04.2-LTS"
		    }
		  }
		}
	```
3. Dopo aver salvato il file azuredeploy.parameters.json, utilizzare il seguente comando per creare un nuovo gruppo di risorse in base al modello. L’opzione `-e` specifica il file azuredeploy.parameters.json che è stato modificato nel passaggio precedente. Sostituire *testRG* con il nome del gruppo che si desidera usare e *testDeploy* con un nome di distribuzione a scelta. La località dovrebbe corrispondere a quella specificata nel file dei parametri del modello.

		azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

	Questo comando restituirà OK dopo che la distribuzione viene caricata, ma prima che venga applicata la distribuzione alle risorse del gruppo.

4. Per verificare lo stato della distribuzione, usare il comando seguente.

		azure group deployment show "testRG" "testDeploy"

	La voce **ProvisioningState** mostra lo stato della distribuzione.

	Se la distribuzione ha avuto esito positivo, verrà visualizzato un output simile al seguente.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE]Se ci si accorge che la configurazione non è corretta ed è necessario arrestare una distribuzione a lunga esecuzione, usare il comando seguente.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Se non si fornisce un nome di distribuzione, ne viene creato uno automaticamente in base al nome del file di modello. Viene restituito come parte dell'output del comando `azure group create`.

	Ora è possibile collegare SSH alla macchina virtuale, utilizzando il nome di dominio specificato. Quando avviene il collegamento con la macchina virtuale, è necessario utilizzare un nome di dominio completo nel formato `<domainName>.<region>.cloudapp.azure.com`, ad esempio `MyDomainName.westus.cloudapp.azure.com`.

5. Per visualizzare il gruppo, usare il comando seguente:

		azure group show "testRG"

	Questo comando restituisce informazioni relative alle risorse nel gruppo. Se sono presenti più gruppi, usare il comando `azure group list` per recuperare un elenco di nomi di gruppi e quindi usare il comando `azure group show` per visualizzare i dettagli di un gruppo specifico.

È inoltre possibile utilizzare un modello direttamente da [GitHub](https://github.com/Azure/azure-quickstart-templates), invece di scaricarne uno nel computer. A tale scopo, passare l'URL al file azuredeploy.json per il modello nel comando utilizzando l’opzione **--template-url**. Per ottenere l’URL, aprire zuredeploy.json in GitHub in modalità _raw_, e copiare l'URL che viene visualizzato nella barra degli indirizzi del browser. È quindi possibile utilizzare questo URL direttamente per creare una distribuzione, utilizzando un comando simile al seguente.

	azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
Viene chiesto di immettere i parametri del modello necessari.

> [AZURE.NOTE]È importante aprire il modello json in modalità _raw_. L'URL che viene visualizzato nella barra degli indirizzi del browser è diverso da quello visibile in modalità normale. Per aprire il file in modalità _raw_ quando si visualizza il file su GitHub, nell'angolo superiore destro fare clic su **Raw**.

## Utilizzo delle risorse

Mentre i modelli consentono di dichiarare modifiche a livello di interi gruppi in ambito di configurazione, è talvolta necessario lavorare su una risorsa specifica. A tale scopo, è possibile usare i comandi `azure resource`.

> [AZURE.NOTE]Quando si usano comandi `azure resource` diversi da `list`, è necessario specificare la versione dell'API della risorsa usata tramite il parametro `-o`. Se non si è certi della versione dell'API da utilizzare, consultare il file di modello e trovare il campo **apiVersion** relativo alla risorsa.

1. Per elencare tutte le risorse in un gruppo, usare il comando seguente.

		azure resource list "testRG"

2. Per visualizzare una singola risorsa all'interno del gruppo, usare un comando simile al seguente.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Notare il parametro **Microsoft.Compute/virtualMachines**. Questo indica il tipo di risorsa per il quale si stanno richiedendo informazioni. Se si esamina il file del modello scaricato in precedenza, si noterà che lo stesso valore viene usato per definire il tipo di risorsa di macchina virtuale descritta nel modello.

	Questo comando restituisce informazioni relative alla macchina virtuale.

3. Quando si visualizzano i dettagli su una risorsa, è spesso utile utilizzare il parametro`--json`. In questo modo l'output è più leggibile poiché alcuni valori sono strutture nidificate o raccolte. L'esempio seguente illustra come restituire i risultati del comando **show** come un documento JSON.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE]È possibile salvare i dati JSON nel file usando il carattere &gt; per inviare l'output al file. Ad esempio:
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Per eliminare una risorsa esistente, usare un comando simile al seguente.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Registrazione

Per visualizzare le informazioni registrate sulle operazioni eseguite su un gruppo, usare il comando `azure group log show`. Per impostazione predefinita, questo comando restituisce l'ultima operazione eseguita sul gruppo. Per visualizzare tutte le operazioni, usare il parametro `--all` facoltativo. Per l'ultima distribuzione, usare `--last-deployment`. Per una distribuzione specifica, usare `--deployment` e specificare il nome della distribuzione. Nell'esempio seguente viene restituito un log di tutte le operazioni eseguite sul gruppo *MyGroup*.

	azure group log show MyGroup --all

## Passaggi successivi

* Per informazioni sull'uso di Gestione risorse con Azure PowerShell, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
* Per informazioni sull'uso di Gestione risorse di Azure dal portale di Azure, vedere [Uso di gruppi di risorse per la gestione risorse di Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Nov15_HO2-->