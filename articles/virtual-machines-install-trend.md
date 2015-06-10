<properties 
	pageTitle="Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure" 
	description="Descrive come installare e configurare Trend Micro Security in una macchina virtuale in Azure." 
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
	ms.date="02/17/2015" 
	ms.author="kathydav"/>


# Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure

Questo articolo illustra come installare e configurare Trend Micro Deep Security as a Service in una macchina virtuale (VM) nuova o esistente che esegue Windows Server. Deep Security as a Service offre protezione antimalware, firewall, sistema di prevenzione delle intrusioni e monitoraggio dell'integrità.

Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali. In una nuova macchina virtuale l'agente di macchine virtuali viene installato insieme a Deep Security Agent. In una macchina virtuale esistente in cui non è presente l'agente di macchine virtuali, sarà necessario prima scaricarlo e installarlo. In questo articolo vengono descritte entrambe le situazioni.

Se si dispone di una sottoscrizione Trend Micro per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di prova. Per altre informazioni su questa soluzione, vedere il post di blog Trend Micro relativo all'[estensione dell'agente di macchine virtuali di Microsoft Azure per Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Installare Deep Security Agent in una nuova macchina virtuale

Il [portale di gestione di Azure](http://manage.windowsazure.com) consente di installare l'agente di macchine virtuali e l'estensione per la sicurezza di Trend Micro quando si usa l'opzione **Da raccolta** per creare la macchina virtuale. L'uso di questo approccio rappresenta un modo semplice per aggiungere la protezione di Trend Micro quando si crea un'unica macchina virtuale.

L'opzione **Da raccolta** apre una procedura guidata che consente di configurare la macchina virtuale. L'ultima pagina della procedura guidata consente di installare l'agente di macchine virtuali e l'estensione di sicurezza di Trend Micro. Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server](virtual-machines-windows-tutorial.md). Quando si raggiunge l'ultima pagina della procedura guidata, eseguire le operazioni seguenti:

1.	In Agente di macchine virtuali selezionare **Installa agente di macchine virtuali**.

2.	In Estensioni di sicurezza fare clic su **Trend Micro Deep Security Agent**.

	![Installare l'agente di macchine virtuali e Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	Fare clic sul segno di spunta per creare la macchina virtuale.

## Installare Deep Security Agent in una macchina virtuale esistente

A questo scopo è necessario disporre di quanto segue:

- Modulo Azure PowerShell 0.8.2 o versioni successive installato nel computer locale. È possibile controllare la versione di Azure PowerShell installata con il comando **Get-Module azure | format-table version**. Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell](install-configure-powershell.md). 

- L'agente di macchine virtuali installato nella macchina virtuale di destinazione.

Verificare innanzitutto che l'agente di macchine virtuali sia già installato. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le macchine virtuali nella sottoscrizione corrente.

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all'[agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947) sono disponibili istruzioni e un collegamento per il download.

Se l'agente di macchine virtuali è installato, eseguire questi comandi.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA
	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Passaggi successivi

Per l'avvio dell'agente sono necessari alcuni minuti dopo l'installazione. In seguito sarà necessario attivare Deep Security sulla macchina virtuale in modo che venga gestita da un Deep Security Manager. Per altre istruzioni, vedere:

- Articolo di Trend Micro sulla [sicurezza cloud immediata per Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101) relativo a questa soluzione.
- [Script di Windows PowerShell di esempio](http://go.microsoft.com/fwlink/?LinkId=404100) per configurare la macchina virtuale.
- [Istruzioni](http://go.microsoft.com/fwlink/?LinkId=404099) per l'esempio.

## Risorse aggiuntive

[Come accedere a una macchina virtuale che esegue Windows Server]

[Estensioni VM e funzionalità di Azure]


<!--Link references-->
[Come accedere a una macchina virtuale che esegue Windows Server]: virtual-machines-log-on-windows-server.md
[Estensioni VM e funzionalità di Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=58-->