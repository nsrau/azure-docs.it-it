<properties
	pageTitle="Installare Symantec Endpoint Protection in una macchina virtuale | Microsoft Azure"
	description="Informazioni su come installare e configurare l'estensione di sicurezza Symantec Endpoint Protection in una macchina virtuale di Azure nuova o esistente creata con il modello di distribuzione classica."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="iainfou"/>

# Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Questo articolo illustra come installare e configurare il client di Symantec Endpoint Protection in una macchina virtuale (VM) esistente in cui si esegue Windows Server. Si tratta del client completo, che include servizi come protezione da virus e spyware, firewall e prevenzione delle intrusioni. Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali.

Se si dispone di una sottoscrizione Symantec per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di valutazione. Per altre informazioni su questa soluzione, vedere [Symantec Endpoint Protection sulla piattaforma Microsoft Azure][Symantec]. Questa pagina include anche collegamenti a informazioni sulle licenze e a istruzioni per installare il client se si è già clienti Symantec.

## Installare Symantec Endpoint Protection in una VM esistente

Prima di iniziare, è necessario disporre di quanto segue:

- Il modulo Azure PowerShell 0.8.2 o versione successiva sul computer di lavoro. È possibile controllare la versione di Azure PowerShell installata con il comando **Get-Module azure | format-table version**. Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell][PS]. Effettuare l'accesso alla sottoscrizione di Azure usando `Add-AzureAccount`.

- L'agente di macchine virtuali in esecuzione sulla macchina virtuale di Azure.

Verificare innanzitutto che l'agente di macchine virtuali sia già installato sulla macchina virtuale. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >.

> [AZURE.TIP] Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare l'elenco dei nomi di tutte le macchine virtuali nella sottoscrizione corrente.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all'[agente di macchine virtuali ed estensioni, parte 2][Agent] sono disponibili istruzioni e un collegamento per il download.

Se l'agente di macchine virtuali è installato, eseguire questi comandi per installare l'agente Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Per verificare che l'estensione di sicurezza Symantec sia stata installata e sia aggiornata:

1.	Accedere alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][Logon].
2.	Per Windows Server 2008 R2, fare clic su **Start > Symantec Endpoint Protection**. Per Windows Server 2012 o Windows Server 2012 R2, nella schermata Start digitare **Symantec**, quindi fare clic su **Symantec Endpoint Protection**.
3.	Nella scheda **Stato** della finestra **Stato - Symantec Endpoint Protection** applicare gli aggiornamenti o, se necessario, riavviare.

## Risorse aggiuntive

[Come accedere a una macchina virtuale che esegue Windows Server][Logon]

[Estensioni VM e funzionalità di Azure][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_0831_2016-->