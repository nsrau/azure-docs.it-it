---
title: Estensioni e funzionalità delle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle estensioni delle VM di Azure e su come usarle con le macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3a8c571d3c6bb55fdd8b2d097b71b83afab5ca00
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705911"
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

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul funzionamento delle estensioni e dell'agente Linux, vedere [Azure VM extensions and features for Linux (Funzionalità ed estensioni VM di Azure per Linux)](features-linux.md).
* Per altre informazioni sul funzionamento delle estensioni e dell'agente guest di Windows, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).  
* Per installare l'agente Guest di Windows, vedere [panoramica dell'agente di macchine virtuali di Windows Azure](agent-windows.md).  
* Per installare l'agente Linux, vedere [panoramica dell'agente di macchine virtuali Linux di Azure](agent-linux.md).  

