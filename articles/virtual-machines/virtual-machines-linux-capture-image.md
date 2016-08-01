<properties
	pageTitle="Acquisire una VM Linux da usare come modello | Microsoft Azure"
	description="Informazioni su come acquisire e ";generalizzare"; l'immagine di una VM di Azure basata su Linux creata con il modello di distribuzione Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="danlep"/>


# Come acquisire una macchina virtuale Linux da usare come modello di Gestione risorse

Usare l'interfaccia della riga di comando di Azure per acquisire e "generalizzare" una macchina virtuale (VM) di Azure che esegue Linux e usarla come modello di Azure Resource Manager per creare altre macchine virtuali. Questo modello specifica il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. Non include le risorse della rete virtuale necessarie per creare una VM di Gestione risorse di Azure, perciò nella maggior parte dei casi sarà necessario configurarle separatamente prima di creare un'altra macchina virtuale che usa il modello.

>[AZURE.TIP]Se si desidera creare l'immagine di una VM Linux e caricarla in Azure in modo da poterla utilizzare per creare altre VM, vedere l'argomento su [come caricare e creare una VM da un'immagine del disco personalizzata](virtual-machines-linux-upload-vhd.md).

## Prima di iniziare

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure nel modello di distribuzione di Gestione risorse di Azure classico e che sia stato configurato il sistema operativo, incluse le connessioni di eventuali dischi dati e l'applicazione di altre personalizzazioni, ad esempio l'installazione di applicazioni. È possibile farlo in diversi modi, ad esempio tramite l'interfaccia della riga di comando di Azure. Se non è ancora stato fatto, vedere queste istruzioni per l'uso dell'interfaccia della riga di comando di Azure nella modalità Gestione risorse di Azure:

- [Creare una VM Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md)

- [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md)

Ad esempio, si potrebbe creare un gruppo di risorse denominato *MyResourceGroup* nell'area Stati Uniti centrali. Usare quindi un comando **azure vm quick-create** simile al seguente per distribuire una VM Ubuntu 14.04 LTS nel gruppo di risorse.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

Dopo avere effettuato il provisioning e avviato l'esecuzione della VM, è possibile [connettere e montare un disco dati](virtual-machines-linux-add-disk.md).


## Acquisire la VM

1. Quando si è pronti per acquisire la VM, connettersi a essa con il client SSH.

2. Nella finestra di SSH digitare il comando seguente. Si noti che l'output di **waagent** può variare leggermente, in base alla versione dell'utilità:

	`sudo waagent -deprovision+user`

	Questo comando prova a pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione esegue le attività seguenti:

	- Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
	- Cancella la configurazione NomeServer in /etc/resolvconf
	- Rimuove la password `root` dell'utente da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
	- Rimuove i lease client DHCP memorizzati nella cache
	- Ripristina il nome host su localhost.localdomain
	- Elimina anche l'ultimo account utente (ottenuto da /var/lib/waagent) di cui è stato effettuato il provisioning e i dati associati.

	>[AZURE.NOTE] Il deprovisioning elimina file e dati nel tentativo di "generalizzare" l'immagine. Eseguire questo comando solo su una VM che si intende acquisire come immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione a terze parti.

3. Digitare **y** per continuare. È possibile aggiungere il parametro **-force** per evitare questo passaggio di conferma.

4. Digitare **exit** per chiudere il client SSH.

	>[AZURE.NOTE] I passaggi successivi presuppongono che l'[interfaccia della riga di comando di Azure sia stata già installata](../xplat-cli-install.md) nel computer client.

5. Dal computer client aprire l'interfaccia della riga di comando di Azure ed eseguire l'accesso alla sottoscrizione di Azure. Per informazioni dettagliate, leggere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).

6. Assicurarsi di essere in modalità Gestione risorse:

	`azure config mode arm`

7. Arrestare la VM di cui è già stato effettuato il deprovisioning usando il comando seguente:

	`azure vm deallocate -g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Generalizzare la VM con il comando seguente:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Ora acquisire l'immagine e un modello di file locale con il comando seguente:

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <path-to-your-template-file-name.json>`

	Questo comando crea un'immagine del sistema operativo generalizzata, usando il prefisso del nome VHD specificato per i dischi della VM. Per impostazione predefinita, il file VHD dell'immagine viene creato nello stesso account di archiviazione della VM originale usata. I dischi rigidi virtuali delle nuove VM eventualmente create dall'immagine verranno archiviati nello stesso account. L'opzione **-t** crea un modello di file JSON locale che è possibile usare per creare una nuova VM dall'immagine.

>[AZURE.TIP] Per trovare la posizione di un'immagine, aprire il modello di file JSON. In **storageProfile** trovare l'**uri** di **image** nel contenitore **system**. Ad esempio, l'URI dell'immagine del disco del sistema operativo è simile a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-vhd-name-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Distribuire una nuova VM dall'immagine acquisita
Usare ora l'immagine con un modello per creare una nuova VM Linux. Questi passaggi mostrano come usare l'interfaccia della riga di comando di Azure e il modello di file JSON creato con il comando `azure vm capture` per creare la VM in una nuova rete virtuale.

### Creare risorse di rete

Per usare il modello, prima di tutto è necessario configurare una rete virtuale e una scheda di interfaccia di rete per la nuova VM. Per queste risorse si consiglia di creare un nuovo gruppo di risorse nel percorso in cui è archiviata l'immagine della VM. Eseguire comandi simili ai seguenti, sostituendo i nomi delle proprie risorse e una posizione di Azure appropriata ("centralus" in questi comandi):

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Per distribuire una VM dall'immagine usando il file JSON salvato durante l'acquisizione, sarà necessario l'ID della scheda di interfaccia di rete. Per ottenerlo, eseguire il comando seguente.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

L'**Id** nell'output è una stringa simile alla seguente.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Creare una nuova distribuzione
Eseguire ora il comando seguente per creare la VM dall'immagine della VM acquisita e dal file JSON del modello salvato.

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <path-to-your-template-file-name.json>

Viene chiesto di specificare un nuovo nome di VM, il nome e la password amministratore e l'ID della scheda di interfaccia di rete creata in precedenza.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Se la distribuzione ha avuto esito positivo, verrà visualizzato un output simile al seguente.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Verificare la distribuzione

Connettersi ora con SSH alla macchina virtuale creata per verificare la distribuzione e iniziare a usare la nuova VM. Per connettersi con SSH, trovare l'indirizzo IP della VM creata eseguendo il comando seguente:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

L'indirizzo IP pubblico viene elencato nell'output del comando. Per impostazione predefinita, ci si connette alla VM Linux con SSH sulla porta 22.

## Creare VM aggiuntive con il modello

Usare l'immagine acquisita e il modello per distribuire altre VM seguendo i passaggi illustrati nella sezione precedente.

* Assicurarsi che l'immagine della VM sia nello stesso account di archiviazione che ospiterà il VHD della VM
* Copiare il file JSON del modello e immettere un valore univoco per l'**URI** del VHD di ogni VM
* Creare una nuova scheda di interfaccia di rete nella stessa rete virtuale o in un'altra
* Creare una distribuzione nel gruppo di risorse in cui è stata configurata la rete virtuale, usando il file JSON del modello modificato

Per configurare automaticamente la rete quando si crea una VM dall'immagine, usare [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) da GitHub. Questo modello crea una VM dall'immagine personalizzata e la rete virtuale, l'indirizzo IP pubblico e le risorse NIC necessari. Per una procedura dettagliata sull'uso del modello nel portale di Azure, vedere la pagina che illustra [Come creare una macchina virtuale da un'immagine personalizzata con un modello ARM](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

## Usare il comando azure vm create

In genere si preferisce usare un modello di Gestione risorse per creare una VM dall'immagine. È però possibile creare la VM _in modo imperativo_ usando il comando **azure vm create** con il parametro **-Q** (**--image-urn**). Passare anche il parametro**-d** (**--os-disk-vhd**) per specificare il percorso del file con estensione vhd del sistema operativo per la nuova VM. Questo deve trovarsi nel contenitore dei VHD dell'account di archiviazione in cui è archiviato il file VHD dell'immagine. Il comando copierà automaticamente il VHD per la nuova VM nel contenitore dei VHD.

Prima di eseguire **azure vm create** con l'immagine, effettuare le operazioni seguenti:

1.	Creare un nuovo gruppo di risorse o identificarne uno esistente per la distribuzione.

2.	Creare una risorsa indirizzo IP pubblico e una risorsa NIC per la nuova VM. Per conoscere i passaggi per creare una rete virtuale, un indirizzo IP pubblico e una scheda di interfaccia di rete usando l'interfaccia della riga di comando, vedere più sopra in questo articolo. **azure vm create** può anche creare una nuova scheda di interfaccia di rete, ma sarà necessario passare i parametri aggiuntivi per una rete e una subnet virtuali.


Eseguire quindi un comando simile a quello riportato di seguito, passando gli URI sia al nuovo file VHD del sistema operativo che all'immagine esistente.

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Per altre opzioni del comando, eseguire `azure help vm create`.

## Passaggi successivi

Per gestire le VM con l'interfaccia della riga di comando, vedere le attività in [Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md).

<!---HONumber=AcomDC_0720_2016-->