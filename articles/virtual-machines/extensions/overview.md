---
title: Estensioni e funzionalità delle macchine virtuali di Azure
description: Altre informazioni sulle estensioni di VM di Azure
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: b1dd26fce2e0a761ceed211933cb79ce518905e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965885"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Estensioni e funzionalità delle macchine virtuali di Azure
Le estensioni sono piccole applicazioni che forniscono configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. La piattaforma Azure ospita molte estensioni per la configurazione delle macchine virtuali, il monitoraggio, la sicurezza e le applicazioni di utilità. Gli editori accettano un'applicazione, ne esegue il wrapping in un'estensione e semplificano l'installazione. È sufficiente specificare parametri obbligatori. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Come trovare le estensioni disponibili
È possibile visualizzare le estensioni disponibili selezionando una macchina virtuale, selezionando **estensioni** nel menu a sinistra. Per ottenere un elenco completo delle estensioni, vedere [individuazione delle estensioni VM per Linux](features-linux.md) e [individuazione delle estensioni VM per Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Come installare un'estensione
Le estensioni di macchina virtuale di Azure possono essere gestite usando l'interfaccia della riga di comando di Azure, PowerShell, i modelli Gestione risorse e il portale di Azure. Per provare un'estensione, passare alla portale di Azure, selezionare l'estensione dello script personalizzato, quindi passare un comando o uno script per eseguire l'estensione.

Per altre informazioni, vedere [estensione script personalizzato di Windows](custom-script-windows.md) e [estensione script personalizzato Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Come gestire il ciclo di vita dell'applicazione di un'estensione
Non è necessario connettersi direttamente a una macchina virtuale per installare o eliminare un'estensione. Il ciclo di vita dell'estensione di Azure viene gestito all'esterno della macchina virtuale e integrato nella piattaforma Azure.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Altre informazioni sulle estensioni
Alcune singole applicazioni delle estensioni VM possono avere prerequisiti ambientali specifici, ad esempio l'accesso a un endpoint. Ogni estensione include un articolo che illustra i prerequisiti, inclusi i sistemi operativi supportati.

## <a name="troubleshoot-extensions"></a>Risoluzione dei problemi relativi alle estensioni

Per informazioni sulla risoluzione dei problemi di ogni estensione, vedere la sezione **risoluzione dei problemi e supporto** nella panoramica per l'estensione. Di seguito è riportato un elenco delle informazioni sulla risoluzione dei problemi disponibili:

| Spazio dei nomi | Risoluzione dei problemi |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Dipendenza di monitoraggio di Azure per Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Dipendenza di monitoraggio di Azure per Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Crittografia dischi di Azure per Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Crittografia dischi di Azure per Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. Compute. customscriptextension | [Script personalizzato per Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. alle customscriptforlinux | [Configurazione dello stato desiderato per Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Configurazione dello stato desiderato per Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [Estensione di driver GPU NVIDIA per Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [Estensione driver GPU NVIDIA per Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Estensione antimalware per Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Monitoraggio di Azure per Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. microsoftmonitoringagent | [Monitoraggio di Azure per Windows](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. Extension. stackifylinuxagentextension | [Stackify ritraccia per Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Reimposta la password per Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snapshot per Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. VMSnapshot | [Snapshot per Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul funzionamento dell'agente Linux e delle estensioni, vedere [estensioni e funzionalità delle macchine virtuali di Azure per Linux](features-linux.md).
* Per ulteriori informazioni sul funzionamento delle estensioni e dell'agente guest di Windows, vedere [estensioni e funzionalità delle macchine virtuali di Azure per Windows](features-windows.md).  
* Per installare l'agente guest di Windows, vedere [Panoramica dell'agente di macchine virtuali Windows di Azure](agent-windows.md).  
* Per installare l'agente Linux, vedere [Panoramica dell'agente di macchine virtuali Linux di Azure](agent-linux.md).  

