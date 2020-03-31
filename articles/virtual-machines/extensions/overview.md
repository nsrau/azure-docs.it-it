---
title: Estensioni e funzionalità delle macchine virtuali di Azure
description: Informazioni sulle estensioni delle VM di Azure e su come usarle con le macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072971"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Estensioni e funzionalità delle macchine virtuali di Azure
Le estensioni macchina virtuale (VM) di Azure sono piccole applicazioni che consentono di eseguire attività di configurazione e automazione post-distribuzione nelle VM di Azure. È possibile usare le immagini esistenti e quindi personalizzarle durante le distribuzioni, evitando così di dover compilare immagini personalizzate.

La piattaforma Azure ospita diverse estensioni, tra cui applicazioni di utilità, sicurezza, monitoraggio e configurazione delle VM. Gli editori eseguono il wrapping di un'applicazione esistente in un'estensione e semplificano l'installazione in modo che all'utente rimanga solo da specificare i parametri obbligatori. 

 È disponibile un'ampia scelta di estensioni Microsoft e di terze parti, se l'applicazione nel repository di estensioni non esiste, quindi è possibile usare l'estensione di script personalizzati e configurare la VM senza i propri script e comandi.

Esempi di scenari chiave per cui vengono usate le estensioni:
* Configurazione di VM. È possibile usare le estensioni PowerShell DSC (Desired State Configuration), Chef, Puppet e di script personalizzati per installare gli agenti di configurazione di VM e configurare la VM. 
* Prodotti antivirus, ad esempio Symantec ed ESET.
* Strumento per la vulnerabilità della VM, ad esempio Qualys, Rapid7, HPE.
* Strumenti di monitoraggio di app e VM, ad esempio DynaTrace, Azure Network Watcher, Site24x7 e Stackify.

Le estensioni possono essere aggregate con una nuova distribuzione di VM. Possono, ad esempio, fare parte di una distribuzione più ampia, in le applicazioni vengono configurate durante il provisioning della VM, o venire eseguite nei sistemi gestiti dalle estensioni supportate dopo la distribuzione.

## <a name="how-can-i-find-what-extensions-are-available"></a>Come trovare le estensioni disponibili
È possibile visualizzare le estensioni disponibili nella sezione delle estensioni nel pannello della VM nel portale. Queste sono solo una piccola parte. Per l'elenco completo, è possibile usare gli strumenti dell'interfaccia della riga di comando. Vedere [Discovering VM Extensions for Linux](features-linux.md) (Individuazione delle estensioni VM per Linux) e [Discovering VM Extensions for Windows](features-windows.md) (Individuazione delle estensioni VM per Windows).

## <a name="how-can-i-install-an-extension"></a>Come installare un'estensione
Le estensioni di macchina virtuale di Azure possono essere gestite con l'interfaccia della riga di comando di Azure, Azure PowerShell, i modelli di Azure Resource Manager e il portale di Azure. Per provare un'estensione, è possibile andare al portale di Azure, selezionare l'estensione di script personalizzati, quindi passare un comando/script ed eseguire le estensioni.

Per usare la stessa estensione aggiunta nel portale con l'interfaccia della riga di comando o il modello di Resource Manager, vedere la documentazione relativa alle altre estensioni, ad esempio [Estensione di script personalizzati Windows](custom-script-windows.md) e [Estensione di script personalizzati Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Come gestire il ciclo di vita dell'applicazione di un'estensione
Non è necessario connettersi direttamente a una VM per installare o eliminare l'estensione. Poiché il ciclo di vita dell'applicazione dell'estensione di Azure viene gestito al di fuori della VM e integrato nella piattaforma Azure, si ottiene anche lo stato integrato dell'estensione.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Altre informazioni sulle estensioni
Le estensioni installano applicazioni. Per qualsiasi applicazione sono previsti alcuni requisiti. Per le estensioni, esiste un elenco di sistemi operativi Windows e Linux supportati e gli agenti delle VM di devono essere installati. Alcune singole applicazioni delle estensioni VM possono avere prerequisiti ambientali specifici, ad esempio l'accesso a un endpoint.

## <a name="troubleshoot-extensions"></a>Risoluzione dei problemi relativi alle estensioni

Le informazioni sulla risoluzione dei problemi per ogni estensione sono disponibili nella sezione **Risoluzione dei problemi e supporto** nella panoramica dell'estensione. Ecco un elenco delle informazioni per la risoluzione dei problemi disponibili:

| Spazio dei nomi | Risoluzione dei problemi |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Dependency for Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows (informazioni in due informazioni insuppato in microsoft.azure.monitoring.dependencyagentwindows) | [Azure Monitor Dependency for Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure Disk Encryption for Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption (informazioni in due sul nome del documento d'altri utenti) | [Crittografia dischi di Azure per Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension (estensione microsoft.compute.customscriptextension) | [Script personalizzato per Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux (informazioni in base alle chiavi) | [Configurazione dello stato desiderato per Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Configurazione dello stato desiderato per Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Estensione del driver GPU NVIDIA per Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Estensione del driver GPU NVIDIA per Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Estensione antimalware per Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor for Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor for Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Ritraccia Stackify per Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Reimpostare la password (VMAccess) per Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Istantanea per Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Istantanea per Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul funzionamento delle estensioni e dell'agente Linux, vedere [Azure VM extensions and features for Linux (Funzionalità ed estensioni VM di Azure per Linux)](features-linux.md).
* Per altre informazioni sul funzionamento delle estensioni e dell'agente guest di Windows, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).  
* Per installare l'agente guest di Windows, vedere [Panoramica dell'agente di macchine virtuali Windows](agent-windows.md)di Azure .  
* Per installare l'agente Linux, vedere [Panoramica](agent-linux.md)di Azure Linux Virtual Machine Agent .  

