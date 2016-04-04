<properties
	pageTitle="Reimpostare le password delle VM Linux e aggiungere gli utenti dall'interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Come usare l'estensione VMAccess dal portale o dall'interfaccia della riga di comando di Azure per reimpostare le password delle VM di Linux e le chiavi SSH, le configurazioni SSH, aggiungere o eliminare gli account utente e verificare la coerenza dei dischi."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="cynthn"/>

# Reimpostare l'accesso, gestire gli utenti e controllare i dischi con l'estensione VMAccess di Azure per Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Se non è possibile connettersi a una macchina virtuale Linux su Azure per una password dimenticata, una chiave SSH (Secure Shell) non valida o un problema di configurazione di SSH, usare il portale di Azure o l'estensione VMAccessForLinux con l'interfaccia della riga di comando di Azure per reimpostare la password o la chiave SSH, correggere la configurazione SSH e verificare la coerenza del disco.

## Portale di Azure

Per ripristinare la configurazione SSH nel [portale di Azure](https://portal.azure.com), fare clic su **Sfoglia** > **Macchine virtuali** > *macchina virtuale Linux* > **Reimposta accesso remoto**. Di seguito è fornito un esempio.

![](./media/virtual-machines-linux-classic-reset-access/Portal-RDP-Reset-Linux.png)

Per reimpostare il nome e la password dell'account utente con privilegi sudo o la chiave pubblica SSH nel [portale di Azure](https://portal.azure.com), fare clic su **Sfoglia** > **Macchine virtuali** > *macchina virtuale Linux* > **Tutte le impostazioni** > **Reimpostazione password**. Di seguito è fornito un esempio.

![](./media/virtual-machines-linux-classic-reset-access/Portal-PW-Reset-Linux.png)


## Interfaccia della riga di comando di Azure e PowerShell

È necessario quanto segue:

- Agente Linux di Microsoft Azure versione 2.0.5 o successive. La maggior parte delle immagini Linux nella raccolta di macchine virtuali include la versione 2.0.5. Per verificare la versione installata, eseguire **waagent -version**. Per aggiornare l'agente, attenersi alle istruzioni presenti nella [Guida dell'utente dell'agente Linux di Azure].
- Interfaccia della riga di comando di Azure (CLI) Per ulteriori informazioni sull'impostazione dell’interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
- Azure PowerShell. I comandi disponibili nel cmdlet Set-AzureVMExtension verranno usati per caricare e configurare automaticamente l'estensione VMAccessForLinux. Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell].
- Una nuova password o un set di chiavi SSH da reimpostare. Queste non saranno necessarie se si vuole reimpostare la configurazione di SSH.

### Non è necessaria alcuna installazione

Non è necessario che l'estensione VMAccess sia installata per poterla usare. Se l'agente Linux è installato nella macchina virtuale, l'estensione viene caricata automaticamente quando si esegue un comando di Azure PowerShell che usa il cmdlet **Set-AzureVMExtension**.

## Utilizzare l’interfaccia della riga di comando di Azure

Con l’interfaccia della riga di comando di Azure sarà possibile usare il comando **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai comandi. Eseguire **azure vm extension set –help** per l’uso dell’estensione dettagliato.

Con l’interfaccia della riga di comando di Azure è possibile eseguire le attività seguenti:

+ [Reimpostare la password](#pwresetcli)
+ [Reimpostare la chiave SSH](#sshkeyresetcli)
+ [Reimpostare la password e la chiave SSH](#resetbothcli)
+ [Creare un nuovo account utente sudo](#createnewsudocli)
+ [Reimpostare la configurazione SSH](#sshconfigresetcli)
+ [Eliminare un utente](#deletecli)
+ [Visualizzare lo stato dell'estensione VMAccess](#statuscli)
+ [Verificare la coerenza dei dischi aggiunti](#checkdisk)
+ [Ripristinare i dischi aggiunti nella VM Linux](#repairdisk)

### <a name="pwresetcli"></a>Reimpostare la password

Passaggio 1: creare un file denominato PrivateConf.json con questo contenuto, sostituendo i valori dei segnaposto.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

Passaggio 2: eseguire questo comando, sostituendo il nome della macchina virtuale per "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Reimpostare la chiave SSH

Passaggio 1: creare un file denominato PrivateConf.json con questo contenuto, sostituendo i valori dei segnaposto.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

Passaggio 2: eseguire questo comando, sostituendo il nome della macchina virtuale per "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Reimpostare la password e la chiave SSH

Passaggio 1: creare un file denominato PrivateConf.json con questo contenuto, sostituendo i valori dei segnaposto.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
	}

Passaggio 2: eseguire questo comando, sostituendo il nome della macchina virtuale per "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Creare un nuovo account utente sudo

Se si dimentica il nome utente, è possibile usare VMAccess per crearne uno nuovo con privilegi sudo. In questo caso, il nome utente e la password esistenti non verranno modificati.

Per creare un nuovo utente sudo con accesso tramite password, usare lo script in [Reimpostare la password](#pwresetcli) e specificare il nuovo nome utente.

Per creare un nuovo utente sudo con accesso tramite chiave SSH, usare lo script in [Reimpostare la chiave SSH](#sshkeyresetcli) e specificare il nuovo nome utente.

È inoltre possibile usare [Reimpostare la password e la chiave SSH](#resetbothcli) per creare un nuovo utente con accesso tramite password e chiave SSH.

### <a name="sshconfigresetcli"></a>Reimpostare la configurazione SSH

Se la configurazione SSH è in uno stato indesiderato, si potrebbe perdere anche l'accesso alla macchina virtuale. È possibile usare l'estensione VMAccess per reimpostare la configurazione allo stato predefinito. A tale scopo, è sufficiente impostare la chiave "reset\_ssh" su "True". L'estensione riavvia il server SSH, apre la porta SSH nella VM e ripristina la configurazione SSH predefinita. L'account utente (nome, password o chiavi SSH) non verrà modificato.

> [AZURE.NOTE] Il file di configurazione SSH che viene reimpostato si trova in /etc/ssh/sshd\_config.

Passaggio 1: creare un file denominato PrivateConf.json con questo contenuto.

	{
	"reset_ssh":"True"
	}

Passaggio 2: eseguire questo comando, sostituendo il nome della macchina virtuale per "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Eliminare un utente

Se si desidera eliminare un account utente senza accedere alla macchina virtuale direttamente, è possibile usare questo script.

Passaggio 1: creare un file denominato PrivateConf.json con questo contenuto, sostituendo il valore del segnaposto.

	{
	"remove_user":"usernametoremove"
	}

Passaggio 2: eseguire questo comando, sostituendo il nome della macchina virtuale per "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Visualizzare lo stato dell'estensione VMAccess

Per visualizzare lo stato dell'estensione VMAccess, eseguire questo comando.

	azure vm extension get

### <a name='checkdisk'<</a>Verificare la coerenza dei dischi aggiunti

Per eseguire fsck su tutti i dischi nella macchina virtuale Linux, è necessario eseguire le operazioni seguenti:

Passaggio 1: Creare un file denominato PublicConf.json con questo contenuto. Il controllo del disco accetta un valore booleano che indica se controllare o meno i dischi collegati alla macchina virtuale.

    {   
    "check_disk": "true"
    }

Passaggio 2: Eseguire questo comando, sostituendo i valori segnaposto.

   azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Ripristinare i dischi aggiunti nella macchina virtuale Linux

Per ripristinare i dischi che presentano problemi di montaggio o errori di configurazione di montaggio, usare l'estensione VMAccess per reimpostare la configurazione di montaggio nella macchina virtuale Linux.

Passaggio 1: Creare un file denominato PublicConf.json con questo contenuto.

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

Passaggio 2: Eseguire questo comando, sostituendo i valori segnaposto.

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## Uso di Azure PowerShell

Si userà il cmdlet **Set-AzureVMExtension** per apportare le modifiche consentite da VMAccess. In ogni caso, iniziare usando il nome del servizio cloud e il nome della macchina virtuale per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile.

Specificare i nomi del servizio cloud e della macchina virtuale, quindi eseguire i comandi seguenti in un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Se non si conosce il servizio cloud e il nome della macchina virtuale, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le VM nella sottoscrizione corrente.


> [AZURE.NOTE] Le righe di comando che iniziano con $ configurano variabili di PowerShell che verranno usate successivamente nei comandi di PowerShell.

Se la macchina virtuale è stata creata con il portale di Azure classico, eseguire il seguente comando aggiuntivo.

	$vm.GetInstance().ProvisionGuestAgent = $true

Questo comando eviterà l'errore relativo alla necessità di abilitare l'agente guest di provisioning sull'oggetto VM prima della configurazione dell'estensione di accesso alle VM IaaS durante l'esecuzione del comando Set-AzureVMExtension nelle sezioni seguenti.

A questo punto è possibile eseguire le attività seguenti:

+ [Reimpostare la password](#password)
+ [Reimpostare una chiave SSH](#SSHkey)
+ [Reimpostare la password e la chiave SSH](#both)
+ [Reimpostare la configurazione SSH](#config)
+ [Eliminare un utente](#delete)
+ [Visualizzare lo stato dell'estensione VMAccess](#status)
+ [Verificare la coerenza dei dischi aggiunti](#checkdisk)
+ [Ripristinare i dischi aggiunti nella VM Linux](#repairdisk)

### <a name="password"></a>Reimpostare la password

Specificare il nome utente Linux attuale e la nuova password, quindi eseguire i comandi seguenti.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Se si desidera reimpostare la password o la chiave SSH per un account utente esistente, assicurarsi di digitare il nome utente esatto. Se si digita un nome diverso, l'estensione VMAccess creerà un nuovo account utente e assegnerà la password a tale account.


### <a name="SSHKey"></a>Reimpostare una chiave SSH

Specificare il nome utente Linux attuale e il percorso del certificato contenente le chiavi SSH, quindi eseguire i comandi seguenti.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Reimpostare la password e la chiave SSH

Specificare il nome utente Linux attuale, la nuova password e il percorso del certificato contenente le chiavi SSH, quindi eseguire i comandi seguenti.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Reimpostare la configurazione SSH

Eventuali errori nella configurazione SSH possono impedire l'accesso alla macchina virtuale. È possibile risolvere il problema ripristinando la configurazione di SSH predefinita. Verranno rimossi tutti i nuovi parametri di accesso nella configurazione, ad esempio nome utente, password e chiave SSH, ma ciò non modifica la password o le chiavi SSH dell'account utente. L'estensione riavvia il server SSH, apre la porta SSH nella macchina virtuale e ripristina la configurazione SSH predefinita.

Eseguire i comandi seguenti.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Il file di configurazione SSH si trova in /etc/ssh/sshd\_config.

### <a name="delete"></a> Eliminare un utente

Specificare il nome utente Linux da eliminare e quindi eseguire i comandi seguenti.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Visualizzare lo stato dell'estensione VMAccess

Per visualizzare lo stato dell'estensione VMAccess, eseguire questo comando.

	$vm.GuestAgentStatus

### <a name="checkdisk"<</a>Verificare la coerenza dei dischi aggiunti

Per verificare la coerenza dei dischi con l'utilità fsck, eseguire questi comandi.

	$PublicConfig = "{"check_disk": "true"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

### <a name="checkdisk"<</a>Ripristinare i dischi aggiunti nella VM Linux

Per ripristinare i dischi con l'utilità fsck, eseguire questi comandi.

	$PublicConfig = "{"repair_disk": "true", "disk_name": "my_disk"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

## Risorse aggiuntive

[Estensioni VM e funzionalità di Azure][]

[Connettersi a una macchina virtuale di Azure con RDP o SSH][]


<!--Link references-->
[Guida dell'utente dell'agente Linux di Azure]: virtual-machines-linux-agent-user-guide.md
[Come installare e configurare Azure PowerShell]: ../install-configure-powershell.md
[Estensioni VM e funzionalità di Azure]: virtual-machines-windows-extensions-features.md
[Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=AcomDC_0323_2016-->