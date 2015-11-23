<properties 
	pageTitle="Utilizzo di SSH su Linux e Mac | Microsoft Azure" 
	description="Generare e utilizzare chiavi SSH su Linux e Mac per i modelli di Gestione risorse e quelli della distribuzione classica in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="rasquill"/>

#Come usare SSH con Linux e Mac in Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

In questo argomento viene descritto come utilizzare **ssh keygen** e **openssl** in Linux e Mac per creare e utilizzare i file di formato **ssh rsa** e **.pem** per proteggere la comunicazione con le macchine virtuali di Azure basate su Linux. È consigliabile creare macchine virtuali di Azure basate su Linux utilizzando il modello di distribuzione di Gestione risorse per nuove distribuzioni e accettare un tipo di stringa o file di chiave pubblica (in base al client di distribuzione) *ssh rsa*. Il [portale di anteprima](https://portal.azure.com) attualmente accetta solo le stringhe di formato **ssh rsa** sia per la distribuzione di Gestione risorse che per quella classica.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Per creare questi tipi di file per l'utilizzo in un computer Windows per comunicare in modo sicuro con macchine virtuali Linux in Azure, vedere [utilizzare chiavi SSH in Windows](virtual-machines-windows-use-ssh-key.md).

## Quali file sono necessari?

Una configurazione di base di ssh per Azure include una coppia di chiavi pubblica e privata **ssh-rsa** da 2.048 bit (per impostazione predefinita, **ssh keygen** archivia i file come **~/.ssh/id\_rsa** e **~/.ssh/id-rsa.pub**, a meno che non si modifichino le impostazioni predefinite), nonché un file `.pem` generato dal file di chiavi private **id\_rsa** da usare con il modello di distribuzione classica del portale classico.

Di seguito sono descritti gli scenari di distribuzione e i tipi di file da usare per ognuno:

1. Le chiavi **ssh-rsa** sono necessarie per qualsiasi distribuzione eseguita tramite il [portale di anteprima](https://portal.azure.com), indipendentemente dal modello di distribuzione.
2. I file con estensione .pem sono necessari per creare macchine virtuali utilizzando il [portale classico](https://manage.windowsazure.com). I file .pem sono supportati anche nelle distribuzioni classiche che usano l'[interfaccia della riga di comando di Azure](xplat-cli-install.md). 

## Creazione di chiavi per l'utilizzo con SSH

Azure richiede il formato dei file di chiave **ssh rsa** di 2.048 bit o i file .pem equivalenti, a seconda dello scenario. Se si dispone già di tali file, passare il file di chiave pubblica quando si crea una macchina virtuale di Azure.

Se è necessario creare i file:

1. Assicurarsi che l'implementazione di **ssh keygen** e **openssl** sia aggiornata. Ciò varia a seconda della piattaforma. 

	- Per Mac, assicurarsi di visitare il [Sito web del prodotto di protezione Apple](https://support.apple.com/HT201222) e, se necessario, scegliere gli aggiornamenti appropriati.
	- Per distribuzioni di Linux basate su Debian, come Ubuntu, Debian, Mint e così via:

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- Per distribuzioni di Linux basate su RPM, come CentOS e Oracle Linux:

			sudo yum update ssh-keygen
			sudo yum update openssl

	- Per SLES e OpenSUSE

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. Utilizzare **ssh-keygen** per creare dei file di chiave pubblici e privati RSA a 2048 bit, e a meno che non si disponga di una posizione specifica o di nomi specifici per i file, accettare la posizione predefinita e il nome di `~/.ssh/id_rsa`. Il comando basic è:

		ssh-keygen -t rsa -b 2048 

	In genere, l’implementazione **ssh-keygen** aggiunge un commento, spesso il nome utente e il nome host del computer. È possibile specificare un commento specifico utilizzando l’opzione `-C`.

3. Creare un file .pem del file `~/.ssh/id_rsa` per consentire di lavorare con il portale classico. Utilizzare l’**openssl** come indicato di seguito:

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Se si desidera creare un file .pem da un file di chiave privato diverso, modificare l’argomento `-key`.

> [AZURE.NOTE]Se si prevede di gestire i servizi distribuiti con il modello di distribuzione classica, è inoltre possibile creare un file in formato **.cer** da caricare nel portale, anche se ciò non riguarda **ssh** o la connessione a macchine virtuali Linux, ovvero l'oggetto di questo articolo. Per creare tali file su Linux o Mac, digitare: <br /> openssl.exe x. 509-outform der-in mycert-out mycert. cer

Per convertire il file .pem in un file di certificato codificato DER X509.

## Utilizzare chiavi SSH di cui si dispone già

È possibile utilizzare chiavi ssh-rsa (`.pub`) per tutti i nuovi lavori, soprattutto con il modello di distribuzione di Gestione risorse e il portale di anteprima, potrebbe essere necessario creare un file `.pem` dalle chiavi, se è necessario utilizzare il portale classico.

## Creare una macchina virtuale con il file di chiave pubblico

Dopo aver creato i file necessari, esistono molti modi per creare una macchina virtuale a cui è possibile connettersi in modo protetto utilizzando uno scambio di chiavi pubblica-privata. In quasi tutte le situazioni, soprattutto se si utilizzano le distribuzioni di Gestione risorse, passare il file .pub quando viene richiesto un percorso di file di chiave ssh o i contenuti di un file sotto forma di stringa.

### Esempio: Creazione di una macchina virtuale con il file id\_rsa.pub

L'utilizzo più comune è quando si crea in modo imperativo una macchina virtuale -- o si carica un modello per creare una macchina virtuale. L’esempio di codice seguente illustra come creare una macchina virtuale di Linux nuova e sicura in Azure passando il nome del file pubblico (in questo caso, il file predefinito `~/.ssh/id_rsa`) al comando `azure vm create`. (Gli altri argomenti sono stati creati in precedenza).

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa \
	testrg testvm westeurope linux

Nell'esempio seguente viene illustrato come utilizzare il formato **ssh-rsa** con un modello di Gestione risorse e CLI di Azure per creare una macchina virtuale di Ubuntu protetta da un nome utente e i contenuto di `~/.ssh/id_rsa.pub` sotto forma di stringa. (In questo caso, la stringa di chiave pubblica viene accorciata per migliorare la leggibilità).

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### Esempio: Creazione di una macchina virtuale con un file .pem

È possibile utilizzare quindi il file .pem nel portale classico o con la modalità di distribuzione classica e `azure vm create`, come nell'esempio seguente:

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-it-IT-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-it-IT-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Connettersi alla macchina virtuale

Il comando **ssh** accetta un nome utente con cui accedere, l'indirizzo di rete del computer, e la porta con cui connettersi all'indirizzo -- nonché molte altre varianti speciali. (Per ulteriori informazioni su **ssh**, è possibile iniziare [qui](https://en.wikipedia.org/wiki/Secure_Shell))

Un uso tipico con la distribuzione di Gestione risorse potrebbe essere simile al seguente, se è stato specificato semplicemente un sotto-dominio e un percorso di distribuzione:

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

in alternativa, se ci si connette a un servizio cloud di distribuzione classica l’indirizzo da utilizzare potrebbe essere simile al seguente:

	ssh user@subdomain.cloudapp.net -p 22

Poiché il formato dell’indirizzo può essere modificato -- è sempre possibile utilizzare l'indirizzo IP o forse si dispone di un nome di dominio personalizzato assegnato -- sarà necessario individuare l'indirizzo della macchina virtuale di Azure.

### Individuare l'indirizzo SSH della macchina virtuale di Azure con le distribuzioni classiche

È possibile individuare l'indirizzo da utilizzare con una macchina virtuale e il modello di distribuzione classica utilizzando il comando `azure vm show` con il nome della macchina virtuale:

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-it-IT-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-it-IT-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Individuare l'indirizzo SSH della macchina virtuale di Azure con le distribuzioni di Gestione risorse

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Esaminare la sezione del profilo di rete:

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Se si non utilizza la porta SSH predefinita 22 durante la creazione della macchina virtuale, è possibile scoprire quali sono le porte dotate di regole connessioni in entrata con il comando `azure network nsg show`, come illustrato nell'esempio seguente:

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Esempio: output della sessione SSH mediante le chiavi .pem e la distribuzione classica

Se è stata creata una macchina virtuale utilizzando un file .pem creato dal `~/.ssh/id_rsa` file, è possibile collegarsi a SSH direttamente in quella macchina virtuale. Si noti che quando si esegue questa operazione, il certificato handshake utilizzerà la chiave privata in `~/.ssh/id_rsa`. Sarà simile all’esempio seguente:

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Saving password to keychain failed
	Identity added: /Users/rasquill/.ssh/id_rsa (/Users/rasquill/.ssh/id_rsa)
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## Nel caso di problemi di connessione

È possibile leggere i suggerimenti in [Risoluzione dei problemi di connessioni SSH](virtual-machines-troubleshoot-ssh-connections.md) per verificare se è possibile risolvere la situazione.

## Passaggi successivi
 
Ora che si è connessi alla macchina virtuale, assicurarsi di aggiornare la distribuzione scelta prima di continuare a utilizzarla.

<!---HONumber=Nov15_HO3-->