<properties
	pageTitle="Interfaccia della riga di comando per Mac, Linux e Windows | Microsoft Azure"
	description="Uso dell'interfaccia della riga di comando di Microsoft Azure per Mac, Linux e Windows con Gestione risorse di Azure."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra la gestione delle risorse con il modello di distribuzione Gestione risorse.

In questo articolo viene descritto come creare, gestire ed eliminare le risorse di Azure e le macchine virtuali tramite la CLI di Azure per Mac, Linux e Windows utilizzando la modalità di gestione risorse di Azure.

>[AZURE.NOTE]Per creare e gestire risorse di Azure tramite la riga di comando, sarà necessario un account Azure ([per ottenere una versione di valutazione gratuita, fare clic qui](http://azure.microsoft.com/pricing/free-trial/)). Sarà inoltre necessario [installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e [accedere per usare le risorse di Azure associate al proprio account](../xplat-cli-connect.md). Se queste operazioni sono già state eseguite, si è pronti per proseguire.

## Risorse di Azure

Usare Gestione risorse di Azure per gestire un gruppo di _risorse_ (entità gestite dall'utente quali una macchina virtuale, un server di database, un database o un sito Web) come una singola unità logica o _gruppo di risorse_. È possibile creare, gestire ed eliminare tali risorse in modo imperativo nella riga di comando, come in modalità asm.

Utilizzando la modalità di Gestione risorse di Azure, è inoltre possibile gestire le risorse di Azure in un modo _dichiarativo_descrivendo la struttura e le relazioni di un gruppo di risorse distribuibile in *modelli* JSON. Il modello descrive i parametri, che possono essere completati inline quando si esegue un comando oppure essere archiviati in un file JSON azuredeploy-parameters.json a parte. Ciò consente di creare facilmente nuove risorse usando lo stesso modello e semplicemente fornendo parametri diversi. Un modello che crea un sito Web disporrà ad esempio di parametri per il nome del sito, per l'area in cui verrà inserito e altri parametri comuni.

Quando un modello viene usato per modificare o creare un gruppo, viene creata una _distribuzione_, che viene quindi applicata al gruppo. Per altre informazioni su Gestione risorse, vedere l'articolo relativo alla [panoramica di Gestione risorse di Azure](../resource-group-overview.md).

## Autenticazione

L'uso di Gestione risorse tramite l'interfaccia della riga di comando di Azure richiede l'autenticazione in Microsoft Azure con un account aziendale o dell'istituto di istruzione. L'autenticazione tramite un certificato installato mediante un file con estensione publishsettings non è supportata.

Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere l'argomento relativo alla [connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).

> [AZURE.NOTE]Poiché si usa un account aziendale o dell'istituto di istruzione, che viene gestito da Azure Active Directory, è anche possibile usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso e l'uso delle risorse di Azure. Per informazioni dettagliate, vedere l'articolo relativo alla [gestione e controllo dell'accesso alle risorse](../resource-group-rbac.md).

## Impostazione della modalità di gestione risorse di Azure

Poiché la modalità Gestione risorse non è abilitata per impostazione predefinita, è necessario usare il comando seguente per abilitare i comandi di gestione risorse dell'interfaccia della riga di comando di Azure.

	azure config mode arm

>[AZURE.NOTE]La modalità Gestione risorse di Azure e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

## Ricerca delle località

La maggior parte dei comandi di gestione risorse di Azure necessita di un percorso valido da cui creare o trovare una risorsa. È possibile trovare tutte le località disponibili usando il comando seguente:

	azure location list

Verranno elencate località specifiche di aree come "West US", "East US" e così via.

## Creazione di un gruppo di risorse

Un gruppo di risorse è un raggruppamento logico di risorse di rete, di archiviazione e di altro tipo. Quasi tutti i comandi in modalità di gestione risorse di Azure necessitano di un gruppo di risorse. È possibile creare un gruppo di risorse denominato_testrg_, ad esempio, utilizzando il comando seguente.

	azure group create -n "testrg" -l "West US"

È quindi possibile iniziare ad aggiungere risorse al gruppo e usarlo per configurare una nuova macchina virtuale.

## Creazione di macchine virtuali

Esistono due modi per creare macchine virtuali in modalità di gestione risorse di Azure:

1. Uso di singoli comandi dell'interfaccia della riga di comando di Azure.
2. Uso di modelli di gruppi di risorse.

Assicurarsi di creare almeno un gruppo di risorse prima di procedere in uno di questi modi.

### Uso di singoli comandi dell'interfaccia della riga di comando di Azure

Questo è l'approccio di base per configurare e creare una macchina virtuale in base alle proprie esigenze. Nella modalità di gestione risorse di Azure, è necessario configurare alcune risorse obbligatorie come la rete prima di poter utilizzare il comando **vm create**.

>[AZURE.NOTE]Se è la prima volta che si creano risorse tramite la riga di comando per la propria sottoscrizione, è possibile che venga chiesto di registrarsi per determinati provider di risorse. In tal caso, è facile registrarsi al provider e riprovare il comando non riuscito, come illustrato nell'esempio seguente.
>
> `azure provider register Microsoft.Storage`
>
> È possibile trovare l'elenco dei provider registrati per la sottoscrizione tramite il comando seguente.
>
> `azure provider list`


#### Creazione di una risorsa IP pubblica

Sarà necessario creare un indirizzo IP pubblico per connettersi tramite SSH alla nuova macchina virtuale per svolgere attività significative. La creazione di un indirizzo IP pubblico è una procedura semplice. Il comando richiede un gruppo di risorse, un nome per la risorsa IP pubblica e una località, in quest'ordine.

	azure network public-ip create "testrg" "testip" "westus"

#### Creazione di una risorsa scheda di interfaccia di rete

La scheda di interfaccia di rete (NIC) richiede che vengano prima create una subnet e una rete virtuale. Creare una rete virtuale in una particolare località e in un determinato gruppo di risorse usando il comando **network vnet create**:

	azure network vnet create "testrg" "testvnet" "westus"

È quindi possibile creare una subnet nella rete virtuale utilizzando il comando **network vnet subnet create**.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

È possibile creare una scheda di interfaccia di rete che utilizzi tali risorse con il comando **network nic create**.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]Benché si tratti di un'operazione facoltativa, è importante passare il nome IP pubblico come parametro al comando **network nic create** perché in questo modo la scheda di interfaccia di rete viene associata a tale IP, che verrà usato successivamente per connettersi tramite SSH alla macchina virtuale creata mediante la scheda.

Per altre informazioni sui comandi **network**, vedere la guida della riga di comando oppure l'articolo relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione risorse di Azure](azure-cli-arm-commands.md).

#### Ricerca dell'immagine del sistema operativo

Attualmente è possibile trovare un sistema operativo solo in base all'editore dell'immagine. In altre parole, è necessario eseguire il comando seguente per trovare un elenco degli editori di immagine del sistema operativo nella posizione desiderata.

	azure vm image list-publishers "westus"

Scegliere un server di pubblicazione dall'elenco, quindi individuare l'elenco delle immagini da quell'autore eseguendo il comando seguente.

	azure vm image list "westus" "CoreOS"

Infine, scegliere un'immagine del sistema operativo dall'elenco che abbia un aspetto analogo al seguente.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

Salvare il nome URN dell'immagine da caricare nella macchina virtuale. Verrà utilizzato in seguito nell'articolo.

#### Creazione di una macchina virtuale

A questo punto si è pronti a creare una macchina virtuale eseguendo il comando **vm create** e fornendo le informazioni necessarie. È facoltativo passare l'indirizzo IP pubblico in questa fase, in quanto la scheda di interfaccia di rete (NIC) già dispone di tali informazioni. Il comando può avere un aspetto simile all'esempio seguente, dove _testvm_ è il nome della macchina virtuale creata nel gruppo di risorse _testrg_.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

Dovrebbe essere possibile avviare la macchina virtuale eseguendo il comando seguente.

	azure vm start "testrg" "testvm"

Connettersi a essa usando il comando **ssh username@ipaddress**. Per cercare rapidamente l'indirizzo IP della risorsa IP pubblica, usare il comando seguente.

	azure network public-ip show "testrg" "testip"

La gestione di questa macchina virtuale è semplice con i comandi **vm**. Per altre informazioni, visitare [Uso dell’interfaccia della riga di comando di Azure con gestione risorse di Azure](azure-cli-arm-commands.md).

### Comando semplificato vm quick-create

Il nuovo comando semplificato **vm quick-create** consente di evitare la maggior parte dei passaggi del metodo imperativo per la creazione di macchine virtuali. Questo è utile quando si vuole provare a creare macchine virtuali semplici o non sono importanti le configurazioni di rete. Si tratta di un comando interattivo e, prima di eseguirlo, è necessario solo determinare l'URN dell'immagine del sistema operativo.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

L'interfaccia della riga di comando di Azure creerà una macchina virtuale con la dimensione predefinita. Creerà inoltre un account di archiviazione, una scheda di interfaccia di rete, una rete virtuale, una subnet e un indirizzo IP pubblico. Dopo l'avvio della macchina virtuale, è possibile connettersi a essa tramite SSH usando l'indirizzo IP pubblico.

### Uso di modelli di gruppi di risorse

#### Individuazione e configurazione di un modello di gruppo di risorse

1. Quando si lavora con i modelli, è possibile crearne uno personalizzato oppure usarne uno dalla raccolta modelli o i modelli disponibili in [GitHub](https://github.com/azurermtemplates/azurermtemplates). Per iniziare, viene usato un modello denominato CoreOS.CoreOSStable.0.2.40-preview proveniente dalla raccolta modelli. Per ottenere un elenco dei modelli disponibili nella raccolta, usare il comando seguente. Dal momento che sono presenti migliaia di modelli, è possibile scorrere le diverse pagine di risultati oppure usare **grep** o **findstr** (in Windows) oppure il comando di ricerca stringhe preferito per trovare i modelli di interesse. In alternativa, è possibile usare l'opzione **--json** e scaricare l'intero elenco in formato JSON per una ricerca più semplice.

		azure group template list

	La risposta conterrà i server di pubblicazione e il nome del modello e avrà un aspetto simile al seguente (ma con molti più elementi).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. Per visualizzare i dettagli del modello, usare il comando seguente.

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	Verranno restituite informazioni descrittive sul modello. Il modello usato creerà una macchina virtuale Linux.

3. Dopo avere selezionato un modello, è possibile scaricarlo usando il comando seguente.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	Dopo avere scaricato un modello, è possibile personalizzarlo per meglio adattarlo alle proprie esigenze, ad esempio aggiungendovi una nuova risorsa.

	>[AZURE.NOTE]Se si modifica il modello, eseguire il comando `azure group template validate` per verificarlo prima di usarlo per creare o modificare un gruppo di risorse esistente.

4. Per configurare il modello di gruppo di risorse, aprire il file di modello in un editor di testo. Notare la raccolta JSON **parameters** nella parte iniziale. Questa contiene un elenco dei parametri previsti da questo modello per poter creare le risorse descritte dal modello stesso. Alcuni parametri possono avere valori predefiniti, mentre altri specificano il tipo di valore o un intervallo di valori consentiti.

	Quando si usa un modello, è possibile specificare parametri insieme ai parametri della riga di comando oppure specificando un file contenente i valori desiderati. È anche possibile scrivere i campi **value** direttamente nella sezione **parameters** del modello, anche se in questo modo il modello risulterebbe associato a una determinata distribuzione e non sarebbe facilmente riutilizzabile. In entrambi i casi, i parametri devono essere in formato JSON ed è necessario specificare valori personalizzati per le chiavi che non dispongono di valori predefiniti.

	Ad esempio, per creare un file contenente i parametri per il modello CoreOS.CoreOSStable.0.2.40-preview, usare i dati seguenti per creare un file denominato params.json. Sostituire i valori usati in questo esempio con i valori desiderati. **Location** deve specificare un'area di Azure, ad esempio **North Europe** o **South Central US**. Questo esempio usa l'area **Stati Uniti occidentali**.

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. Dopo aver salvato il file params.json, utilizzare il seguente comando per creare una nuova risorse in base al modello. Il parametro `-e` specifica il file params.json creato nel passaggio precedente. Sostituire **testRG** con il nome del gruppo che si desidera usare e **testDeploy** con il nome della distribuzione. La località dovrebbe corrispondere a quella specificata nel file dei parametri del modello params.json.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	Questo comando restituirà OK dopo che la distribuzione viene caricata, ma prima che venga applicata la distribuzione alle risorse del gruppo. Per verificare lo stato della distribuzione, usare il comando seguente.

		azure group deployment show "testRG" "testDeploy"

	La voce **ProvisioningState** mostra lo stato della distribuzione.

	Se la distribuzione ha avuto esito positivo, verrà visualizzato un output simile al seguente esempio.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]Se ci si accorge che la configurazione non è corretta ed è necessario arrestare una distribuzione a lunga esecuzione, usare il comando seguente.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Se non si fornisce un nome di distribuzione, ne verrà creato uno automaticamente in base al nome del file di modello. Verrà restituito come parte dell'output del comando `azure group create`.

6. Per visualizzare il gruppo, usare il comando seguente:

		azure group show "testRG"

	Questo comando restituisce informazioni relative alle risorse nel gruppo. Se sono presenti più gruppi, è possibile usare il comando `azure group list` per recuperare un elenco di nomi di gruppi e quindi usare il comando `azure group show` per visualizzare i dettagli di un gruppo specifico.

7. È inoltre possibile utilizzare i modelli più recenti direttamente da GitHub, anziché doverli scaricare dalla raccolta modelli. A tale scopo, aprire [GitHub.com](http://www.github.com) e cercare AzureRmTemplates. Selezionare il repository AzureRmTemplates e cercare i modelli che possono risultare interessanti, ad esempio _101-simple-vm-from-image_. Se si sceglie il modello, si noterà che contiene azuredeploy.json tra gli altri file. Questo è il modello che si intende usare nel comando con un'opzione **--template-url**. Aprirlo in modalità _raw_ e copiare l'URL che viene visualizzato nella barra degli indirizzi del browser. È quindi possibile utilizzare questo URL direttamente per creare una distribuzione, anziché doverli scaricare da una galleria modelli, utilizzando un comando simile al seguente esempio.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]È importante aprire il modello json in modalità _raw_. L'URL che viene visualizzato nella barra degli indirizzi del browser è diverso da quello visibile in modalità normale. Per aprire il file in modalità _raw_ quando si visualizza il file su GitHub, nell'angolo superiore destro fare clic su **Raw**.

#### Utilizzo delle risorse

Mentre i modelli consentono di dichiarare modifiche a livello di interi gruppi in ambito di configurazione, è talvolta necessario lavorare su una risorsa specifica. A tale scopo, è possibile usare i comandi `azure resource`.

> [AZURE.NOTE]Quando si usano comandi `azure resource` diversi da `list`, è necessario specificare la versione dell'API della risorsa usata tramite il parametro `-o`. Se non si è certi della versione dell'API da utilizzare, consultare il file di modello e trovare il campo **apiVersion** relativo alla risorsa.

1. Per elencare tutte le risorse in un gruppo, usare il comando seguente.

		azure resource list "testRG"

2. Per visualizzare le singole risorse all'interno del gruppo, usare il comando seguente.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	Notare il parametro **Microsoft.ClassicCompute/virtualMachines**. Questo indica il tipo di risorsa per il quale si stanno richiedendo informazioni. Se si esamina il file del modello scaricato in precedenza, si noterà che lo stesso valore viene usato per definire il tipo di risorsa di macchina virtuale descritta nel modello.

	Questo comando restituisce informazioni relative alla macchina virtuale.

3. Quando si visualizzano i dettagli di una risorsa, è spesso utile usare il parametro `--json` in quanto quest'ultimo rende l'output più leggibile in presenza di valori quali raccolte o strutture nidificate. L'esempio seguente illustra come restituire i risultati del comando **show** come un documento JSON.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]È possibile salvare i dati JSON nel file usando il carattere &gt; per inviare l'output al file. Ad esempio:
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. Per eliminare una risorsa esistente, usare il comando seguente.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## Registrazione

Per visualizzare le informazioni registrate sulle operazioni eseguite su un gruppo, usare il comando `azure group log show`. Per impostazione predefinita, questo comando restituisce l'ultima operazione eseguita sul gruppo. Per visualizzare tutte le operazioni, usare il parametro `--all` facoltativo. Per l'ultima distribuzione, usare `--last-deployment`. Per una distribuzione specifica, usare `--deployment` e specificare il nome della distribuzione. Nell'esempio seguente viene restituito un log di tutte le operazioni eseguite sul gruppo MyGroup.

	azure group log show mygroup --all

## Passaggi successivi

* Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure, vedere l'articolo relativo all'[Installazione e configurazione dell'interfaccia della riga di comando di Azure][clisetup].
* Per informazioni sull'uso di Gestione risorse con Azure PowerShell, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
* Per informazioni sull'uso di Gestione risorse di Azure dal portale di Azure, vedere [Uso di gruppi di risorse per la gestione risorse di Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli-install.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Oct15_HO1-->