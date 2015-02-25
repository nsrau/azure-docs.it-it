<properties pageTitle="Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure" description="Descrive come installare e configurare Symantec Endpoint Protection in una macchina virtuale in Azure." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="1/26/2015" ms.author="kathydav"/>

#Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure

Questo articolo illustra come installare e configurare il client di Symantec Endpoint Protection in una macchina virtuale (VM) nuova o esistente in cui si esegue Windows Server. Si tratta del client completo, che include servizi come protezione da virus e spyware, firewall e prevenzione delle intrusioni. 

Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali. In una nuova macchina virtuale si procede all'installazione dell'agente insieme al client dell'endpoint. In una macchina virtuale esistente senza l'agente, sarà necessario prima scaricare e installare l'agente stesso. In questo articolo vengono descritte entrambe le situazioni.

Se si dispone di una sottoscrizione Symantec per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di prova. Per altre informazioni su questa soluzione, vedere [Symantec Endpoint Protection sulla piattaforma Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=403942). Questa pagina fornisce anche collegamenti a informazioni sulle licenze e a istruzioni alternative per installare il client se si è già clienti Symantec.

##Installare Symantec Endpoint Protection in una nuova macchina virtuale

Il [portale di gestione di Azure](http://manage.windowsazure.com) consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec quando si usa l'opzione **Da raccolta** per creare la macchina virtuale. L'uso di questo approccio rappresenta un modo semplice per aggiungere la protezione di Symantec quando si sta creando una singola macchina virtuale. 

L'opzione **Da raccolta** apre una procedura guidata che consente di configurare la macchina virtuale. L'ultima pagina della procedura guidata consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec. 

Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=403943). Quando si raggiunge l'ultima pagina della procedura guidata, eseguire le operazioni seguenti:

1.	In Agente di macchine virtuali l'opzione **Installa agente di macchine virtuali** dovrebbe essere già selezionata.

2.	In Estensioni di sicurezza selezionare **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Fare clic sul segno di spunta nella parte inferiore della pagina per creare la macchina virtuale.

## Installare Symantec Endpoint Protection in una macchina virtuale esistente

Prima di iniziare, è necessario disporre di quanto segue:

- Modulo Azure PowerShell 0.8.2 o versioni successive. Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- Agente di macchine virtuali. Per istruzioni e un collegamento al download, vedere il post di blog relativo all'[agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Per installare l'estensione di sicurezza Symantec in una macchina virtuale esistente:

1.	Ottenere i nomi del servizio cloud e della macchina virtuale. Se non si conoscono questi nomi, utilizzare il comando **Get-AzureVM** per visualizzare i nomi di tutte le macchine virtuali presenti nella sottoscrizione corrente. Sostituire quindi tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >, e attivare i seguenti comandi:

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Dalle informazioni visualizzate dal comando Get-AzureVMAvailableExtension, prendere nota del numero della versione della proprietà Version ed eseguire i comandi seguenti:

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Per verificare che l'estensione di sicurezza Symantec sia stata installata e sia aggiornata:

1.	Accedere alla macchina virtuale.
2.	Per Windows Server 2008 R2, fare clic su **Start > Tutti i programmi > Symantec Endpoint Protection**. Per Windows Server 2012, nella schermata Start digitare **Symantec**, quindi fare clic su **Symantec Endpoint Protection**.
3.	Se necessario, dalla finestra di stato applicare gli aggiornamenti.

## Risorse aggiuntive
[Come accedere a una macchina virtuale che esegue Windows Server]

[Gestire le estensioni]

<!--Link references-->
[Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/

[Gestire le estensioni]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409





<!--HONumber=42-->
