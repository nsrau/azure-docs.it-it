---
title: Gestione remota Windows su HTTPS per Azure | Azure Marketplace
description: Viene illustrato come configurare una macchina virtuale basata su Windows ospitata in Azure in modo che possa essere gestita in remoto con PowerShell.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: 6e159bd9b57b26c99afd590d6a9f2153dba2a205
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808416"
---
# <a name="windows-remote-management-over-https"></a>Gestione remota Windows tramite HTTPS

Questa sezione spiega come configurare una macchina virtuale basata su Windows e ospitata in Azure, in modo che possa essere gestita e distribuita in modalità remota con PowerShell.  Per abilitare la comunicazione remota tramite PowerShell, la macchina virtuale di destinazione deve esporre un endpoint HTTPS di Gestione remota Windows (WinRM).  Per altre informazioni sulla comunicazione remota tramite PowerShell, vedere [Running Remote Commands](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6) (Esecuzione di comandi remoti).  Per altre informazioni su WinRM, vedere [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal) (Gestione remota Windows).

Se è stata creata una macchina virtuale usando uno degli approcci di Azure "classici", ovvero il portale di Azure Service Manager o l'[API Gestione dei servizi di Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)), viene automaticamente configurata con un endpoint WinRM.  Tuttavia, se si crea una macchina virtuale usando uno degli approcci di Azure "moderni" elencati di seguito, la macchina virtuale *non* sarà configurata per WinRM tramite HTTPS.  

- Uso del [portale di Azure](https://portal.azure.com/), in genere da una base approvata, come descritto nella sezione [Creare un disco rigido virtuale compatibile con Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Uso dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Uso della shell dei comandi di Azure PowerShell o dell'interfaccia della riga di comando di Azure.  Per esempi, vedere [Guida introduttiva: creare una macchina virtuale Windows in Azure con PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [Guida introduttiva: creare una macchina virtuale Linux con l'interfaccia della](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)riga di comando di Azure.

Questo endpoint WinRM è richiesto anche per eseguire lo strumento di certificazione per l'onboarding della macchina virtuale, come descritto in [Certificare l'immagine di macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Al contrario, le macchine virtuali Linux vengono in genere gestite in modalità remota tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) o i comandi Linux in una console SSH.  Azure offre anche diversi metodi alternativi per [eseguire script nelle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Per scenari più complessi, sono disponibili numerose soluzioni di automazione e integrazione per le macchine virtuali basate su Windows o Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurare e distribuire con WinRM

L'endpoint WinRM per una macchina virtuale basata su Windows può essere configurato durante due diverse fasi di sviluppo:

- Durante la creazione, in fase di distribuzione di una macchina virtuale in un disco rigido virtuale esistente.  Si tratta dell'approccio preferito per le nuove offerte.  Questo approccio richiede la creazione di un certificato di Azure, usando i modelli di Azure Resource Manager forniti ed eseguendo script di PowerShell personalizzati. 
- Dopo la distribuzione, in una macchina virtuale esistente ospitata in Azure.  Usare questo approccio se si dispone già una soluzione di macchina virtuale distribuita in Azure ed è necessario abilitare Gestione remota Windows per tale soluzione.  Questo approccio richiede modifiche manuali nel portale di Azure e l'esecuzione di uno script nella macchina virtuale di destinazione. 


## <a name="next-steps"></a>Passaggi successivi
Se si sta creando una nuova macchina virtuale, è possibile abilitare WinRM durante la [distribuzione della macchina virtuale dai relativi dischi rigidi virtuali](./cpp-deploy-vm-vhd.md).  In caso contrario, WinRM può essere abilitato in una macchina virtuale esistente.  
