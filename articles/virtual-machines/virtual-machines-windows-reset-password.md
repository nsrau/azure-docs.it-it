<properties 
	pageTitle="Come reimpostare una password o il servizio Desktop remoto per macchine virtuali di Windows" 
	description="Reimpostare rapidamente una password di amministratore locale o il servizio Desktop remoto per macchine virtuali di Windows usando il portale di anteprima di Azure o i comandi PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="josephd"/>

# Come reimpostare una password o il servizio Desktop remoto per macchine virtuali di Windows

Se non è possibile connettersi a una macchina virtuale di Windows a causa di una password dimenticata o un problema con la configurazione del servizio Desktop remoto, usare il portale di anteprima di Azure o l'estensione VMAccess per reimpostare la password di amministratore locale o la configurazione del servizio Desktop remoto.

> [AZURE.NOTE]Questo articolo non è applicabile a macchine virtuali basate su Gestione risorse di Azure.

## Portale di anteprima di Azure

Per reimpostare il servizio Desktop remoto nel [portale di anteprima di Azure](https://portal.azure.com), fare clic su **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Reimposta accesso remoto**. Di seguito è fornito un esempio.


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Per reimpostare il nome e la password dell'account di amministratore locale nel [portale di anteprima di Azure](https://portal.azure.com), fare clic su **Sfoglia** > **Macchine virtuali** > *macchina virtuale Windows* > **Tutte le impostazioni** > **Reimpostazione password**. Di seguito è fornito un esempio.

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)

 
## Estensione VMAccess e PowerShell

Prima di iniziare, è necessario disporre di quanto segue:

- Modulo Azure PowerShell 0.8.5 o versioni successive. È possibile controllare la versione di Azure PowerShell installata con il comando **Get-Module azure | format-table version**. Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409). 
- Nuova password dell'account amministratore locale. Non sarà necessaria se si vuole reimpostare la configurazione del servizio Desktop remoto. 
- Agente di macchine virtuali. 

Non è necessario che l'estensione VMAccess sia installata per poterla usare. Se l'agente VM è installato nella macchina virtuale, l'estensione viene caricata automaticamente quando si esegue un comando di Azure PowerShell che usa il cmdlet **Set-AzureVMExtension**.
 
Verificare innanzitutto che l'agente di macchine virtuali sia già installato. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le macchine virtuali nella sottoscrizione corrente.

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all'[agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) sono disponibili istruzioni e un collegamento per il download.

Se la macchina virtuale è stata creata con il portale di gestione di Azure, eseguire anche il comando seguente:

	$vm.GetInstance().ProvisionGuestAgent = $true

Questo comando eviterà l'errore relativo alla necessità di abilitare l'agente guest di provisioning sull'oggetto VM prima della configurazione dell'estensione di accesso alle VM IaaS durante l'esecuzione del comando Set-AzureVMExtension nelle sezioni seguenti.

È ora possibile eseguire le attività seguenti:

- Reimpostare una password dell'account amministratore locale
- Reimpostare la configurazione del servizio Desktop remoto

## Reimpostare una password dell'account amministratore locale

Specificare il nome dell'account amministratore locale attuale e la nuova password, quindi eseguire i comandi seguenti.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Se si digita un nome diverso rispetto all'account attuale, l'estensione VMAccess assegnerà un nuovo nome all'account amministratore locale, assegnerà la password a tale account ed effettuerà la disconnessione da Desktop remoto.
- Se l'account amministratore locale è disabilitato, l'estensione VMAccess lo abiliterà.
 
Questi comandi reimpostano anche la configurazione del servizio Desktop remoto.

## Reimpostare la configurazione del servizio Desktop remoto

Per reimpostare la configurazione del servizio Desktop remoto, eseguire il comando seguente.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

L'estensione VMAccess esegue i due comandi seguenti sulla macchina virtuale:

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	Questo comando abilita il gruppo predefinito Windows Firewall, che consente il traffico in ingresso di Desktop remoto, che usa la porta TCP 3389.

- **Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Questo comando imposta il valore fDenyTSConnections del Registro di sistema su 0, abilitando le connessioni a Desktop remoto.

Se il problema di accesso a Desktop remoto non è stato risolto, eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Fare clic su **Pacchetto di diagnostica Microsoft Azure IaaS (Windows)** in questa pagina per creare una nuova sessione di diagnostica.
2.	Nella pagina relativa ai **problemi verificatisi nella macchina virtuale di Azure** selezionare il problema relativo alla **connettività Desktop remoto verso una macchina virtuale di Azure (riavvio necessario)**. 

Per ulteriori informazioni, vedere l’articolo della Knowledge base [Pacchetto di diagnostica Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Se non è possibile eseguire il pacchetto di diagnostica Azure IaaS (Windows) o se l'esecuzione non ha risolto il problema, vedere la pagina relativa alla [risoluzione dei problemi delle connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Risorse aggiuntive

[Estensioni VM e funzionalità di Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

 

<!---HONumber=July15_HO2-->