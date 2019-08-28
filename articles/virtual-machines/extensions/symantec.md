---
title: Installare Symantec Endpoint Protection in una macchina virtuale Windows in Azure | Documentazione Microsoft
description: Informazioni su come installare e configurare l'estensione di sicurezza Symantec Endpoint Protection in una macchina virtuale di Azure nuova o esistente creata con il modello di distribuzione classica.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: e6e81732c0e25bc46d28172cdf085f6c3f457ca8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084262"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows
> [!IMPORTANT] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Questo articolo illustra come installare e configurare il client di Symantec Endpoint Protection in una macchina virtuale (VM) esistente in cui si esegue Windows Server. Questo client completo include servizi come protezione da virus e spyware, firewall e prevenzione delle intrusioni. Il client viene installato come estensione di sicurezza usando l'agente di macchine virtuali.

Se si dispone di una sottoscrizione Symantec per una soluzione locale, è possibile usarla per proteggere le macchine virtuali di Azure. Se non si è ancora clienti, è possibile iscriversi per una sottoscrizione di valutazione. Per ulteriori informazioni su questa soluzione, vedere [Symantec Endpoint Protection sulla piattaforma Azure di Microsoft][Symantec]. Questa pagina include anche collegamenti a informazioni sulle licenze e a istruzioni per installare il client se si è già clienti Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installare Symantec Endpoint Protection in una VM esistente
Prima di iniziare, è necessario disporre di quanto segue:

* Il modulo Azure PowerShell 0.8.2 o versione successiva sul computer di lavoro. È possibile controllare la versione di Azure PowerShell installata con il comando **Get-Module azure | format-table version** . Per istruzioni e un collegamento alla versione più recente, vedere [Come installare e configurare Azure PowerShell][PS]. Effettuare l'accesso alla sottoscrizione di Azure usando `Add-AzureAccount`.
* L'agente di macchine virtuali in esecuzione sulla macchina virtuale di Azure.

Verificare innanzitutto che l'agente di macchine virtuali sia già installato sulla macchina virtuale. Specificare il nome del servizio cloud e il nome della macchina virtuale, quindi eseguire i comandi seguenti a un prompt dei comandi di Azure PowerShell di livello amministratore. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < e >.

> [!TIP]
> Se non si conosce il nome del servizio cloud e della macchina virtuale, eseguire **Get-AzureVM** per visualizzare l'elenco dei nomi di tutte le macchine virtuali nella sottoscrizione corrente.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se viene visualizzato **false**, vedere le istruzioni e un collegamento al download nel post di Blog di Azure [agente di macchine virtuali ed estensioni-parte 2][Agent].

Se l'agente di macchine virtuali è installato, eseguire questi comandi per installare l'agente Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Per verificare che l'estensione di sicurezza Symantec sia stata installata e sia aggiornata:

1. Accedere alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][Logon].
2. Per Windows Server 2008 R2, fare clic su **Start > Symantec Endpoint Protection**. Per Windows Server 2012 o Windows Server 2012 R2, nella schermata Start digitare **Symantec**, quindi fare clic su **Symantec Endpoint Protection**.
3. Nella scheda **Stato** della finestra **Stato - Symantec Endpoint Protection** applicare gli aggiornamenti o, se necessario, riavviare.

## <a name="additional-resources"></a>Risorse aggiuntive
[Come accedere a una macchina virtuale che esegue Windows Server][Logon]

[Estensioni VM e funzionalità di Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
