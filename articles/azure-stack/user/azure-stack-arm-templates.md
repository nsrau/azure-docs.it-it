---
title: Utilizzare i modelli di gestione risorse di Azure nello Stack di Azure | Documenti Microsoft
description: Informazioni su come utilizzare i modelli di gestione risorse di Azure nello Stack di Azure per le risorse di provisioning.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Usare i modelli di Gestione risorse di Azure in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Modelli di gestione risorse di Azure distribuiscono ed eseguire il provisioning di tutte le risorse per l'applicazione in un'operazione singola, coordinata. È anche possibile ridistribuire i modelli per apportare modifiche alle risorse nel gruppo di risorse.

Questi modelli possono essere distribuiti con il portale di Microsoft Azure Stack, PowerShell, la riga di comando e Visual Studio.

I seguenti modelli di avvio rapido sono disponibili in [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Distribuire SharePoint (disponibilità non elevata)
Utilizzare l'estensione DSC PowerShell per creare una farm di SharePoint 2013 che include le risorse seguenti:

* Una rete virtuale
* Tre account di archiviazione
* Due servizi di bilanciamento del carico esterni
* Una macchina virtuale configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014
* Una VM configurata come farm con un singolo computer di SharePoint 2013

## <a name="deploy-ad-non-high-availability"></a>Distribuire Active Directory (non-alta disponibilità)
Utilizzare l'estensione DSC PowerShell per creare un server di controller di dominio Active Directory che include le risorse seguenti:

* Una rete virtuale
* Un account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale configurata come controller di dominio per una nuova foresta con un singolo dominio

## <a name="deploy-adsql-non-high-availability"></a>Distribuire AD/SQL (disponibilità non elevata)
Utilizzare l'estensione DSC PowerShell per creare un server autonomo di SQL Server 2014 che include le risorse seguenti:

* Una rete virtuale
* Due account di archiviazione
* Un servizio di bilanciamento del carico esterno
* Una macchina virtuale configurata come controller di dominio per una nuova foresta con un singolo dominio
* Una VM configurata come server autonomo di SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Usare l'estensione DSC di PowerShell per configurare una macchina virtuale esistente di Gestione configurazione locale e registrarla a un server di pull DSC dell'account di Automazione di Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creare una macchina virtuale da un'immagine dell'utente
Creare una macchina virtuale da un'immagine dell'utente personalizzata. Questo modello distribuisce anche una rete virtuale (con DNS), un indirizzo IP pubblico e un'interfaccia di rete.

## <a name="simple-vm"></a>VM semplice
Distribuire una macchina virtuale di Windows che include un'interfaccia di rete, indirizzo IP pubblico e una rete virtuale (con DNS).

## <a name="cancel-a-running-template-deployment"></a>Annullare la distribuzione di un modello in esecuzione
Per annullare una distribuzione modello in esecuzione, utilizzare il `Stop-AzureRmResourceGroupDeployment` cmdlet di PowerShell.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire modelli con il portale](azure-stack-deploy-template-portal.md)

[Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

