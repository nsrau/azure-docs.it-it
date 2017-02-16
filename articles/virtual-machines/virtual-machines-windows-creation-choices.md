---
title: Diversi modi per creare una macchina virtuale Windows | Microsoft Docs
description: Elenca i diversi modi per creare una macchina virtuale Windows con Gestione risorse.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: bc3921003bf3b91de88e70ff18b5c397687c288c
ms.openlocfilehash: 6f4a825faac413652b1dc3880d899e0e40caedbc


---
# <a name="different-ways-to-create-a-windows-vm"></a>Diversi modi di creare una VM di Windows
Azure offre diversi modi per creare una macchina virtuale perché le macchine virtuali sono adatte a scopi e utenti diversi. Di conseguenza è necessario effettuare alcune scelte sulla macchina virtuale e su come crearla. In questo articolo viene fornito un riepilogo di queste opzioni e i collegamenti alle istruzioni.

## <a name="azure-portal"></a>Portale di Azure
L’utilizzo del portale di Azure è un modo semplice per provare a usare una macchina virtuale, soprattutto se si ha poca esperienza con Azure. 

[Creare una macchina virtuale con Windows tramite il portale](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Modello
Le macchine virtuali richiedono una combinazione di risorse (come set di disponibilità e account di archiviazione). Anziché distribuire e gestire separatamente ogni risorsa, è possibile creare un modello Azure Resource Manager che distribuisce e fornisce tutte le risorse in un'unica operazione coordinata.

* [Creare una macchina virtuale Windows con un modello di Gestione risorse](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
Se si preferisce lavorare in una shell dei comandi, è possibile utilizzare Azure PowerShell.

* [Creare una VM Windows tramite PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Usare Visual Studio per creare, gestire e distribuire VM con gli strumenti di Azure per Visual Studio e Azure SDK.

[Strumenti di Azure per Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)




<!--HONumber=Nov16_HO5-->


