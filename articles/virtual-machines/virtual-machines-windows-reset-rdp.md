<properties
	pageTitle="Reimpostare la password o il desktop remoto in una VM Windows | Microsoft Azure"
	description="Reimpostare la password di amministratore o i servizi Desktop remoto in una VM Windows creata con il modello di distribuzione di gestione risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="iainfou"/>

# Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una VM Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se non è possibile connettersi a una macchina virtuale Windows perché si è dimenticata la password o per un problema di configurazione del servizio Desktop remoto, è possibile reimpostare la password di amministratore locale o la configurazione del servizio Desktop remoto. È possibile usare il portale di Azure o l'estensione di accesso alla VM in Azure PowerShell per reimpostare la password. Se si usa PowerShell, verificare che nel computer di lavoro sia installato il modulo PowerShell più recente e di aver effettuato la connessione alla sottoscrizione di Azure. Per la procedura dettagliata, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] È possibile verificare la versione di PowerShell installata usando `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`.

## VM Windows nel modello di distribuzione di Resource Manager

### Portale di Azure
Selezionare la macchina virtuale facendo clic su **Esplora** > **Macchine virtuali** > *macchina virtuale Windows* > **Tutte le impostazioni** > **Reimposta password**. Verrà visualizzato il pannello di reimpostazione delle password, come illustrato di seguito:

![Pagina di reimpostazione della password](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Immettere il nome utente e una nuova password, quindi fare clic su **Salva**. Provare a connettersi di nuovo alla macchina virtuale.

### Estensione VMAccess e PowerShell

Assicurarsi che sia installato Azure PowerShell 1.0 o versione successiva e di aver effettuato l'accesso all'account tramite il cmdlet `Login-AzureRmAccount`.

#### **Reimpostare una password dell'account amministratore locale**

È possibile reimpostare la password o il nome utente di amministratore usando il comando di PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx).

Creare le credenziali dell'account amministratore locale usando il comando seguente:

	$cred=Get-Credential

Se si digita un nome diverso rispetto all'account attuale, il seguente comando VMAccessExtension assegnerà un nuovo nome all'account amministratore locale, assegnerà la password a tale account ed effettuerà la disconnessione da Desktop remoto. Se l'account amministratore locale è disabilitato, l'estensione VMAccess lo abiliterà.

Usare l'estensione VMAccess per impostare le nuove credenziali come indicato di seguito:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
		-Location WestUS -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Sostituire `myRG`, `myVM`, `myVMAccess` e posizione con i valori pertinenti alla propria configurazione.


#### **Reimpostare la configurazione del servizio Desktop remoto**

È possibile reimpostare l'accesso remoto alla VM tramite [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) come indicato di seguito. Sostituire `myRG`, `myVM`, `myVMAccess` e la posizione con valori personalizzati.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Oppure: <br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Entrambi i comandi aggiungono un nuovo agente di accesso con nome alla macchina virtuale. In qualsiasi momento una VM può avere un solo agente di accesso. Per poter impostare le proprietà dell'agente di accesso alla VM, rimuovere l'agente di accesso impostato in precedenza tramite `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. A partire da Azure PowerShell versione 1.2.2 è possibile evitare questo passaggio quando si usa `Set-AzureRmVMExtension` con l'opzione `-ForceRerun`. Quando si usa `-ForceRerun`, assicurarsi di usare lo stesso nome per l'agente di accesso alla macchina virtuale impostato dal comando precedente.

Se non è ancora possibile connettersi in remoto alla macchina virtuale, vedere altre procedure da provare in [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## Macchine virtuali Windows nel modello di distribuzione classica

### Portale di Azure

Per le macchine virtuali create tramite il modello di distribuzione classica, è possibile reimpostare il servizio Desktop remoto mediante il [portale di Azure](https://portal.azure.com). Fare clic su **Esplora** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Reimposta accesso remoto**. Verrà visualizzata la pagina seguente.

![Ripristinare la pagina di configurazione RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

È anche possibile reimpostare il nome e la password dell'account amministratore locale. Fare clic su **Esplora** > **Macchine virtuali (classico)** > *macchina virtuale Windows* > **Tutte le impostazioni** > **Reimposta password**. Verrà visualizzata la pagina seguente.

![Pagina di reimpostazione della password](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Dopo aver immesso il nuovo nome utente e la nuova password, fare clic su **Salva**.

### Estensione VMAccess e PowerShell

Assicurarsi che l'agente di macchine virtuali sia installato nella macchina virtuale. Per usare l'agente di macchine virtuali è sufficiente che quest'ultimo sia disponibile. Non è necessario che sia installata l'estensione VMAccess. Verificare che l'agente di macchine virtuali sia già installato usando il comando seguente. Sostituire "myCloudService" e "myVM" con i nomi rispettivamente del servizio cloud e della VM. Per scoprire questi nomi eseguire `Get-AzureVM` senza parametri.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all'[agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) sono disponibili istruzioni e un collegamento per il download.

Se la macchina virtuale è stata creata mediante il portale, controllare se `$vm.GetInstance().ProvisionGuestAgent` restituisce **True**. In caso contrario, è possibile impostarla usando questo comando:

	$vm.GetInstance().ProvisionGuestAgent = $true

Questo comando, durante l'esecuzione del comando **Set-AzureVMExtension** nei passaggi successivi, evita l'errore relativo alla necessità di abilitare l'agente guest di provisioning sull'oggetto VM prima della configurazione dell'estensione di accesso alle VM IaaS.

#### **Reimpostare una password dell'account amministratore locale**

Creare credenziali di accesso con il nome dell'account amministratore locale attuale e una nuova password, quindi eseguire `Set-AzureVMAccessExtension` come segue.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se si digita un nome diverso rispetto all'account attuale, l'estensione VMAccess assegnerà un nuovo nome all'account amministratore locale, assegnerà la password a tale account ed effettuerà la disconnessione da Desktop remoto. Se l'account amministratore locale è disabilitato, l'estensione VMAccess lo abiliterà.

Questi comandi reimpostano anche la configurazione del servizio Desktop remoto.

#### **Reimpostare la configurazione del servizio Desktop remoto**

Per reimpostare la configurazione del servizio Desktop remoto, eseguire il comando seguente.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

L'estensione VMAccess esegue due comandi sulla macchina virtuale:

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Questo comando abilita il gruppo predefinito Windows Firewall, che consente il traffico in ingresso di Desktop remoto, che usa la porta TCP 3389.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Questo comando imposta il valore fDenyTSConnections del Registro di sistema su 0, abilitando le connessioni a Desktop remoto.


## Risorse aggiuntive

[Estensioni VM e funzionalità di Azure](virtual-machines-windows-extensions-features.md)

[Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0615_2016-->