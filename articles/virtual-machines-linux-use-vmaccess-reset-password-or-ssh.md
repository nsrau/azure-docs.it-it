<properties 
	pageTitle="Come usare VMAccess per le macchine virtuali Linux" 
	description="Come usare l'estensione VMAccess per Linux per reimpostare le password e le chiavi SSH, per reimpostare le configurazioni SSH ed eliminare gli utenti Linux" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Come reimpostare una password o SSH per le macchine virtuali Linux #

Se non è possibile connettersi a una macchina virtuale Linux a causa di una password dimenticata, una chiave SSH (Secure Shell) non valida o un problema di configurazione di SSH, usare l'estensione VMAccessForLinux per reimpostare la password o la chiave SSH oppure correggere la configurazione SSH. 

## Requisiti

- Agente Linux di Microsoft Azure versione 2.0.5 o successive. La maggior parte delle immagini Linux nella raccolta di macchine virtuali include la versione 2.0.5. Per verificare la versione installata, eseguire `waagent -version`. Per aggiornare l'agente, attenersi alle istruzioni presenti nella [Guida dell'utente dell'agente Linux di Azure].
- Azure PowerShell. I comandi disponibili nel cmdlet **Set-AzureVMExtension** verranno usati per caricare e configurare automaticamente l'estensione **VMAccessForLinux**. Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell].
- Una nuova password o un set di chiavi SSH da reimpostare. Queste non saranno necessarie se si vuole reimpostare la configurazione di SSH. 

## Non è necessaria alcuna installazione

Non è necessario che l'estensione VMAccess sia installata per poterla usare. Se l'agente Linux è installato nella macchina virtuale, l'estensione viene caricata automaticamente quando si esegue un comando di Azure PowerShell che usa il cmdlet **Set-AzureVMExtension**. 

## Usare l'estensione per reimpostare una password, la chiave SSH o la configurazione SSH oppure per eliminare un utente

Si userà il cmdlet **Set-AzureVMExtension** per apportare le modifiche consentite da VMAccess. In ogni caso, iniziare usando il nome del servizio cloud e il nome della macchina virtuale per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile. 

Specificare i nomi del servizio cloud e della macchina virtuale, quindi eseguire i comandi seguenti in un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Se non si conosce il servizio cloud e il nome della VM, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le VM nella sottoscrizione corrente.


> [AZURE.NOTE] Le righe di comando che iniziano con $ configurano variabili di PowerShell che verranno usate successivamente nei comandi di PowerShell.

Se la macchina virtuale è stata creata con il portale di gestione di Azure, eseguire anche il comando seguente:

	$vm.GetInstance().ProvisionGuestAgent = $true

Questo comando eviterà l'errore relativo alla necessità di abilitare l'agente guest di provisioning sull'oggetto VM prima della configurazione dell'estensione di accesso alle VM IaaS durante l'esecuzione del comando Set-AzureVMExtension nelle sezioni seguenti. 

A questo punto è possibile eseguire le attività seguenti:

+ [Reimpostare la password](#password)
+ [Reimpostare una chiave SSH](#SSHkey)
+ [Reimpostare la password e la chiave SSH](#both)
+ [Reimpostare la configurazione SSH](#config)
+ [Eliminare un utente](#delete)

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

> [AZURE.NOTE] Il file di configurazione SSH si trova in /etc/ssh/sshd_config.

### <a name="delete"></a> Eliminare un utente

Specificare il nome utente Linux da eliminare e quindi eseguire i comandi seguenti.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Risorse aggiuntive

[Estensioni VM e funzionalità di Azure] []

[Connettersi a una macchina virtuale di Azure con RDP o SSH] []


<!--Link references-->
[Guida dell'utente dell'agente Linux di Azure]: ../virtual-machines-linux-agent-user-guide
[Come installare e configurare Azure PowerShell]: ../install-configure-powershell
[Estensioni VM e funzionalità di Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
