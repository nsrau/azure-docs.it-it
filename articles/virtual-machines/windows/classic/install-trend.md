---
title: Installare Trend Micro Deep Security in una macchina virtuale | Microsoft Docs
description: In questo articolo viene descritto come installare e configurare la sicurezza Trend Micro su una macchina virtuale creata con il modello di distribuzione classica in Azure.
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 7922a9561369acf3c4299e1cadc0dc9f8f8eadb1
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Windows
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Questo articolo illustra come installare e configurare Trend Micro Deep Security as a Service in una macchina virtuale (VM) nuova o esistente che esegue Windows Server. Deep Security as a Service offre protezione antimalware, un firewall, un sistema di prevenzione delle intrusioni e il monitoraggio dell'integrità.

Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali. In una nuova macchina virtuale è necessario installare Deep Security Agent, in quanto l'agente di macchine virtuali viene creato automaticamente tramite il portale di Azure.

Una macchina virtuale esistente creata tramite il portale classico, l'interfaccia della riga di comando di Azure o PowerShell potrebbe non disporre di un agente di macchine virtuali. In una macchina virtuale esistente priva dell'agente di macchine virtuali, è necessario prima di tutto scaricare l'agente e installarlo. In questo articolo vengono descritte entrambe le situazioni.

Se si dispone di una sottoscrizione Trend Micro per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di valutazione. Per altre informazioni su questa soluzione, vedere il post di blog Trend Micro relativo all' [estensione dell'agente di macchine virtuali di Microsoft Azure per Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installare Deep Security Agent in una nuova VM

Il [portale di Azure classico](http://portal.azure.com) consente di installare l'estensione per la sicurezza di Trend Micro quando si usa un'immagine da **Marketplace** per creare la macchina virtuale. Quando si crea un'unica macchina virtuale, l'uso del portale è un metodo semplice per aggiungere la protezione di Trend Micro.

L'uso di una voce di **Marketplace** determina l'avvio di una procedura guidata che consente di configurare la macchina virtuale. Usare il terzo pannello della procedura guidata, **Impostazioni**, per installare l'estensione per la sicurezza di Trend Micro.  Per istruzioni generali, vedere [Creare una macchina virtuale con Windows nel portale di Azure](tutorial.md).

Quando si raggiunge il pannello **Impostazioni** della procedura guidata, seguire questa procedura:

1. Fare clic su **Estensioni**, quindi fare clic su **Aggiungi estensione** nel riquadro successivo.

   ![Iniziare ad aggiungere l'estensione][1]

2. Selezionare **Deep Security Agent** nel riquadro **Nuova risorsa**. Nel riquadro Deep Security Agent fare clic su **Crea**.

   ![Identificare Deep Security Agent][2]

3. Immettere l'**identificatore del tenant** e la **password di attivazione del tenant** per l'estensione. Se lo si desidera, è possibile immettere un **identificatore dei criteri di sicurezza**. Fare quindi clic su **OK** per aggiungere il client.

   ![Immettere i dettagli relativi all'estensione][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installare Deep Security Agent in una VM esistente
Per installare l'agente in una VM esistente, è necessario quanto segue:

* Il modulo Azure PowerShell 0.8.2 o versioni successive installato nel computer locale. È possibile controllare la versione di Azure PowerShell installata con il comando **Get-Module azure | format-table version** . Per istruzioni e un collegamento alla versione più recente, vedere l'argomento relativo alla [modalità di installazione e configurazione di Azure PowerShell](/powershell/azure/overview). Effettuare l'accesso alla sottoscrizione di Azure usando `Add-AzureAccount`.
* L'agente di macchine virtuali installato nella macchina virtuale di destinazione.

Verificare innanzitutto che l'agente di macchine virtuali sia già installato. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare tali informazioni per tutte le macchine virtuali nella sottoscrizione corrente.

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all' [agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947)sono disponibili istruzioni e un collegamento per il download.

Se l'agente di macchine virtuali è installato, eseguire questi comandi.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi
Per l'avvio dell'agente sono necessari alcuni minuti dopo l'installazione. In seguito è necessario attivare Deep Security sulla macchina virtuale in modo che venga gestita da un Deep Security Manager. Per altre istruzioni, vedere gli articoli seguenti:

* Articolo di Trend Micro sulla [sicurezza cloud immediata per Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* Uno [Script di Windows PowerShell di esempio](http://go.microsoft.com/fwlink/?LinkId=404100) per configurare la macchina virtuale.
* [Istruzioni](http://go.microsoft.com/fwlink/?LinkId=404099) per l'esempio

## <a name="additional-resources"></a>Risorse aggiuntive
[Come accedere a una macchina virtuale che esegue Windows Server]

[Estensioni VM e funzionalità di Azure]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Come accedere a una macchina virtuale che esegue Windows Server]:connect-logon.md
[Estensioni VM e funzionalità di Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
