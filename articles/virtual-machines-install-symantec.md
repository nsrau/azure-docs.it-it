<properties title="Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure" pageTitle="Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure" description="Descrive come installare e configurare Symantec Endpoint Protection in una macchina virtuale in Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="7/16/2014" ms.author="kathydav" />

# Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure

Questo articolo illustra come installare e configurare il client di Symantec Endpoint Protection in una macchina virtuale (VM) nuova o esistente in cui si esegue Windows Server. Si tratta del client completo, che include servizi come protezione da virus e spyware, firewall e prevenzione delle intrusioni.

Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali. In una nuova macchina virtuale si procede all'installazione dell'agente insieme al client dell'endpoint. In una macchina virtuale esistente senza l'agente, sarà necessario prima scaricare e installare l'agente stesso. In questo articolo vengono descritte entrambe le situazioni.

Se si dispone di una sottoscrizione Symantec per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di valutazione. Per altre informazioni su questa soluzione, vedere [Symantec Endpoint Protection sulla piattaforma Microsoft Azure][Symantec Endpoint Protection sulla piattaforma Microsoft Azure]. Questa pagina fornisce anche collegamenti a informazioni sulle licenze e a istruzioni alternative per installare il client se si è già clienti Symantec.

## Installare Symantec Endpoint Protection in una nuova macchina virtuale

Il [portale di gestione di Azure][portale di gestione di Azure] consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec quando si usa l'opzione **Da raccolta** per creare la macchina virtuale. L'uso di questo approccio rappresenta un modo semplice per aggiungere la protezione di Symantec quando si sta creando una singola macchina virtuale.

L'opzione **Da raccolta** apre una procedura guidata che consente di configurare la macchina virtuale. L'ultima pagina della procedura guidata consente di installare l'agente di macchine virtuali e l'estensione di sicurezza Symantec.

Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server][Creare una macchina virtuale che esegue Windows Server]. Quando si raggiunge l'ultima pagina della procedura guidata, eseguire le operazioni seguenti:

1.  In Agente di macchine virtuali selezionare **Installa agente di macchine virtuali**.

2.  In Estensioni di sicurezza selezionare **Symantec Endpoint Protection**.

    ![Installare l'agente di macchine virtuali e il client di Endpoint Protection][Installare l'agente di macchine virtuali e il client di Endpoint Protection]

3.  Fare clic sul segno di spunta nella parte inferiore della pagina per creare la macchina virtuale.

## Installare Symantec Endpoint Protection in una macchina virtuale esistente

Per installare Deep Security Agent in una macchina virtuale esistente, è necessario disporre di quanto segue:

-   Modulo Azure PowerShell 0.8.2 o versioni successive. Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell][modalità di installazione e configurazione di Azure PowerShell].

-   Agente di macchine virtuali. Per istruzioni e un collegamento al download, vedere il post di blog relativo all'[agente di macchine virtuali ed estensioni, parte 2][agente di macchine virtuali ed estensioni, parte 2].

Aprire una sessione di Azure PowerShell ed eseguire i comandi riportati di seguito. Assicurarsi di sostituire i segnaposto, ad esempio MyServiceName, con i valori personalizzati.

1.  Ottenere il nome del servizio cloud, il nome della macchina virtuale e la VM, quindi archiviare ognuna di queste variabili in modo che i comandi seguenti possano farne uso:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Se non si conosce il servizio cloud e il nome della VM, eseguire Get-AzureVM per visualizzare tali informazioni per tutte le VM nella sottoscrizione corrente.

2.  Aggiungere l'agente Symantec Endpoint Protection Agent alla macchina virtuale. Per installare la versione più recente, eseguire:

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Se si vuole installare una versione specifica, eseguire il comando seguente per ottenere un elenco delle versioni disponibili: `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Quindi, includere il parametro Version quando si esegue Set-AzureVMExtension.

3.  Aggiornare la VM. L'operazione determinerà l'aggiornamento dell'agente Symantec Endpoint Protection:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Risorse aggiuntive

[Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server]

[Gestire le estensioni][Gestire le estensioni]

<!--Link references-->

  [Symantec Endpoint Protection sulla piattaforma Microsoft Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Creare una macchina virtuale che esegue Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installare l'agente di macchine virtuali e il client di Endpoint Protection]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [modalità di installazione e configurazione di Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [agente di macchine virtuali ed estensioni, parte 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/
  [Gestire le estensioni]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
