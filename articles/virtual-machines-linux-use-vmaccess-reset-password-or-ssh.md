<properties 
	pageTitle="Come usare VMAccess per le macchine virtuali Linux" 
	description="Come usare l'estensione VMAccess per Linux per reimpostare le password, le chiavi e le configurazioni SSH" 
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
	ms.date="10/30/2014" 
	ms.author="kathydav"/>

#Come reimpostare una password o SSH per le macchine virtuali Linux#

Se si dimentica la password o la chiave SSH oppure si verifica un problema con la configurazione SSH e non è possibile connettersi a una VM Linux, usare l'estensione VMAccessforLinux per reimpostare la password, la chiave SSH o la configurazione SSH. 


##Requisiti

- Agente Linux di Microsoft Azure versione 2.0.5 o successive. La maggior parte delle immagini Linux nella raccolta di macchine virtuali include la versione 2.0.5. Per verificare la versione installata, eseguire `waagent -version`. Per aggiornare l'agente, attenersi alle istruzioni presenti nella [Guida dell'utente dell'agente Linux di Azure].

- Modulo di Azure Powershell. Il modulo include il cmdlet **Set-AzureVMExtension**, con il quale è possibile eseguire i comandi per usare l'estensione **VMAccessForLinux**. Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell].

- Una nuova password o le chiavi SSH che si desidera reimpostare. Queste non saranno necessarie se invece si desidera correggere la configurazione SSH. 

##Non è necessaria alcuna installazione

Non è necessario che VMAccess sia installato per poterlo usare. Se l'agente Linux e il modulo di Azure sono installati, l'estensione viene caricata automaticamente quando si esegue un comando che chiama il cmdlet **Set-AzureVMExtension**. 

##Usare l'estensione per reimpostare una password, la chiave o la configurazione SSH oppure per aggiungere un utente

Si userà il cmdlet **Set-AzureVMExtension** per apportare le modifiche consentite da VMAccess. In ogni caso, iniziare usando il nome del servizio cloud e il nome della macchina virtuale per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile.   

Aprire Azure PowerShell e digitare il comando seguente al prompt dei comandi. Assicurarsi di sostituire i segnaposto MyServiceName e MyVMName con i nomi effettivi:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

A questo punto è possibile eseguire le attività seguenti:

+ [Reimpostare la password](#password)
+ [Reimpostare una chiave SSH](#SSHkey)
+ [Reimpostare la password e la chiave SSH](#both)
+ [Reimpostare la configurazione SSH](#config)

### <a name="password"></a>Reimpostare la password
Digitare il nome utente e la password e archiviarli in variabili, quindi creare una singola variabile per archiviare i valori in modo che possano essere usati dai comandi successivi.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Archiviare il nome, l'entità di pubblicazione e il numero della versione in variabili: 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Una volta archiviati in variabili i valori necessari, eseguire il comando seguente:

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Se si desidera reimpostare la password o la chiave SSH per un account utente esistente, assicurarsi di digitare il nome utente esatto. Se si digita un nome diverso, l'estensione VMAccess creerà un nuovo account utente e assegnerà la password a tale account.

### <a name="SSHkey"></a>Reimpostare una chiave SSH

Digitare il nome utente e il percorso della nuova chiave pubblica SSH e archiviarli in variabili:

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Attivare i seguenti comandi:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Reimpostare la password e la chiave SSH

Per l'utente corrente, digitare una nuova password e il percorso del nuovo certificato con la chiave pubblica SSH e archiviarli in variabili: 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Attivare i seguenti comandi:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Reimpostare la configurazione SSH

Eventuali errori nella configurazione SSH possono impedire l'accesso alla macchina virtuale. È possibile risolvere il problema ripristinando la configurazione predefinita. In questo modo verranno rimossi tutti i nuovi parametri di accesso della configurazione (nome utente, password o chiave SSH). Non verrà invece modificata la password o le chiavi SSH dell'account utente. L'estensione riavvia il server SSH, apre la porta SSH nella VM e ripristina la configurazione SSH predefinita.  

Impostare il flag che indica che si desidera reimpostare la configurazione e archiviarlo in una variabile: 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Attivare i seguenti comandi:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Il file di configurazione SSH si trova in /etc/ssh/sshd_config.

## Risoluzione dei problemi

Quando si usa il cmdlet **Set-AzureVMExtension**, è possibile che venga visualizzato questo errore: "Prima di impostare l'estensione per l'accesso alle VM IaaS, è necessario abilitare l'agente guest di provisioning nella macchina virtuale". 

Questa situazione può verificarsi se la VM Linux è stata creata usando il portale di gestione, in quanto il valore della proprietà dell'agente guest non può essere impostato su "True". Per risolvere questo problema, attivare i seguenti comandi:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Risorse aggiuntive
[Estensioni VM e funzionalità di Azure] []

[Connettersi a una macchina virtuale di Azure con RDP o SSH] []


<!--Link references-->
[Guida dell'utente dell'agente Linux di Azure]: ../virtual-machines-linux-agent-user-guide
[Come installare e configurare Azure PowerShell]: ../install-configure-powershell
[Estensioni VM e funzionalità di Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Connettersi a una macchina virtuale di Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx



<!--HONumber=42-->
