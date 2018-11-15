---
title: Usare i modelli di Azure Resource Manager in Azure Stack | Microsoft Docs
description: Informazioni su come usare i modelli di Azure Resource Manager in Azure Stack per il provisioning delle risorse.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 38e85ede1e984aa3d26fe509d68b4582d11b6c26
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636221"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usare i modelli di Gestione risorse di Azure in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare i modelli Azure Resource Manager per distribuire ed eseguire il provisioning di tutte le risorse per l'applicazione in un'unica operazione coordinata. È anche possibile ridistribuire i modelli per apportare modifiche alle risorse in un gruppo di risorse.

Questi modelli possono essere distribuiti con il portale di Microsoft Azure Stack, PowerShell, la riga di comando e Visual Studio.

I modelli di Guida introduttiva seguenti sono disponibili sul [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Distribuire SharePoint Server (distribuzione della disponibilità elevata)

Usare l'estensione DSC di PowerShell per [creare una farm di SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) che include le risorse seguenti:

* Una rete virtuale
* Tre account di archiviazione
* Due servizi di bilanciamento del carico esterni
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014
* Una VM configurata come una farm di SharePoint Server 2013 un solo computer

## <a name="deploy-ad-non-high-availability-deployment"></a>Distribuire Active Directory (non-elevata--distribuzione a disponibilità)

Usare l'estensione DSC di PowerShell per [creare un server di controller di dominio Active Directory](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) che include le risorse seguenti:

* Una rete virtuale
* Un account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio

## <a name="deploy-adsql-non-high-availability-deployment"></a>Distribuire AD/SQL (non-elevata--distribuzione a disponibilità)

Usare l'estensione DSC di PowerShell per [creare un server autonomo di SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) che include le risorse seguenti:

* Una rete virtuale
* Due account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale (VM) configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Usare l'estensione DSC di PowerShell per configurare una macchina virtuale esistente di Gestione configurazione locale e registrarla a un server di pull DSC dell'account di Automazione di Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creare una macchina virtuale da un'immagine dell'utente

[Creare una macchina virtuale da un'immagine utente personalizzata](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Questo modello distribuisce anche una rete virtuale (con DNS), un indirizzo IP pubblico e un'interfaccia di rete.

## <a name="basic-virtual-machine"></a>Macchina virtuale di base

[Distribuire una macchina virtuale Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) che include una rete virtuale (con DNS), indirizzo IP pubblico e un'interfaccia di rete.

## <a name="cancel-a-running-template-deployment"></a>Annullare la distribuzione di un modello in esecuzione

Per annullare una distribuzione modello in esecuzione, usare il [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) cmdlet di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con il portale](azure-stack-deploy-template-portal.md)
* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
