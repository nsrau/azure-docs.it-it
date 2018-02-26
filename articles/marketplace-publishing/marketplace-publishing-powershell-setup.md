---
title: Configurare PowerShell per la creazione di una macchina virtuale per Marketplace | Documentazione Microsoft
description: Istruzioni per la configurazione di Azure PowerShell e l'uso come un flusso di processo facoltativo per la creazione di immagini di VM da distribuire e vendere in Azure Marketplace
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurare Azure PowerShell per la creazione di un'offerta per Azure Marketplace
Per informazioni dettagliate su come configurare PowerShell in Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Un approccio semplice consiste nell'usare il metodo basato sul certificato, che scarica e importa un certificato necessario per l'autenticazione. Per ottenere il certificato richiesto, usare il cmdlet **Get-AzurePublishSettingsFile** . Quando richiesto, salvare il file. Per importare il certificato in una sessione di PowerShell, usare il cmdlet **Import-AzurePublishSettingsFile** .

Per configurare e archiviare le impostazioni di sottoscrizione comuni di Microsoft Azure per la sessione di PowerShell, usare i cmdlet **Set-AzureSubscription** e **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Il primo comando associa un account di archiviazione predefinito alla sottoscrizione (necessario per alcune operazioni di provisioning delle macchine virtuali).  Il secondo rende corrente la sottoscrizione (riconosciuta da altri cmdlet).

## <a name="see-also"></a>Vedere anche 
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
* [Creazione di un'immagine di macchina virtuale per Marketplace](marketplace-publishing-vm-image-creation.md)

