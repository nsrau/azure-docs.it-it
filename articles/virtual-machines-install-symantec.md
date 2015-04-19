<properties 
	pageTitle="Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure" 
	description="Descrive come installare e configurare l'estensione di sicurezza Symantec Endpoint Protection in una macchina virtuale nuova o esistente in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

# Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure

Questo articolo illustra come installare e configurare il client di Symantec Endpoint Protection in una macchina virtuale (VM) nuova o esistente in cui si esegue Windows Server. Si tratta del client completo, che include servizi come protezione da virus e spyware, firewall e prevenzione delle intrusioni. 

Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali. In una nuova macchina virtuale si procede all'installazione dell'agente insieme al client dell'endpoint. In una macchina virtuale esistente senza l'agente, sarà necessario prima scaricare e installare l'agente stesso. In questo articolo vengono descritte entrambe le situazioni.

Se si dispone di una sottoscrizione Symantec per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di prova. Per altre informazioni su questa soluzione, vedere la pagina relativa all'uso di [Symantec Endpoint Protection sulla piattaforma Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=403942). Questa pagina fornisce anche collegamenti a informazioni sulle licenze e a istruzioni alternative per installare il client se si è già clienti Symantec.

## Installare Symantec Endpoint Protection in una nuova macchina virtuale

Il [portale di gestione di Azure](http://manage.windowsazure.com) consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec quando si usa l'opzione **Da raccolta** per creare la macchina virtuale. L'uso di questo approccio rappresenta un modo semplice per aggiungere la protezione di Symantec quando si sta creando una singola macchina virtuale. 

L'opzione **Da raccolta** apre una procedura guidata che consente di configurare la macchina virtuale. L'ultima pagina della procedura guidata consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec. 

Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server](virtual-machines-windows-tutorial.md). Quando si raggiunge l'ultima pagina della procedura guidata, eseguire le operazioni seguenti:

1.	In Agente di macchine virtuali l'opzione **Installa agente di macchine virtuali** dovrebbe essere già selezionata.

2.	In Estensioni di sicurezza selezionare **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Fare clic sul segno di spunta nella parte inferiore della pagina per creare la macchina virtuale.

## Installare Symantec Endpoint Protection in una macchina virtuale esistente

Prima di iniziare, è necessario disporre di quanto segue:

- Modulo Azure PowerShell 0.8.2 o versioni successive. Per verificare quale versione di Azure PowerShell è installata, è possibile usare il comando **Get-Module azure | format-table version**. Per istruzioni e un collegamento alla versione più recente, vedere [Come installare e configurare Azure PowerShell](install-configure-powershell.md).  

- Agente di macchine virtuali. 

Verificare innanzitutto che l'agente di macchine virtuali sia già installato. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le macchine virtuali nella sottoscrizione corrente.

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all'[agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947) sono disponibili istruzioni e un collegamento per il download.

Se l'agente di macchine virtuali è installato, eseguire questi comandi per installare l'agente Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection
	Set-AzureVMExtension -Publisher Symantec -Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Per verificare che l'estensione di sicurezza Symantec sia stata installata e sia aggiornata:

1.	Accedere alla macchina virtuale. Per altre informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).
2.	Per Windows Server 2008 R2, fare clic su **Start > Symantec Endpoint Protection**. Per Windows Server 2012 o Windows Server 2012 R2, nella schermata Start digitare **Symantec**, quindi fare clic su **Symantec Endpoint Protection**.
3.	Nella scheda **Stato** della finestra **Stato - Symantec Endpoint Protection** applicare gli aggiornamenti o, se necessario, riavviare.

## Risorse aggiuntive

[Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md)

[Gestione delle estensioni](https://msdn.microsoft.com/library/dn606311.aspx)


<!--HONumber=47-->
