---
title: Gli account di archiviazione nello Stack di Azure | Documenti Microsoft
description: Informazioni su come creare un account di archiviazione di Azure Stack.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="storage-accounts-in-azure-stack"></a>Account di archiviazione in Azure Stack
Gli account di archiviazione includono i servizi BLOB e tabelle e lo spazio dei nomi univoco per gli oggetti dati di archiviazione. Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account di archiviazione.

1. Nel computer di prova dello Stack di Azure, accedere a `https://adminportal.local.azurestack.external` come [amministratore](azure-stack-connect-azure-stack.md), quindi fare clic su **New** > **dati e archiviazione**  >  **Account di archiviazione**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Nel **creare account di archiviazione** pannello, digitare un nome per l'account di archiviazione. Creare un nuovo **gruppo di risorse**, o selezionarne uno esistente, quindi fare clic su **crea** per creare l'account di archiviazione.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Per visualizzare il nuovo account di archiviazione, fare clic su **tutte le risorse**, quindi cercare l'account di archiviazione e fare clic sul relativo nome.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Passaggi successivi
[Usare i modelli di Azure Resource Manager](user/azure-stack-arm-templates.md)

[Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md)

[Scaricare la Guida di convalida di archiviazione coerente con Azure Azure Stack](http://aka.ms/azurestacktp1doc)
