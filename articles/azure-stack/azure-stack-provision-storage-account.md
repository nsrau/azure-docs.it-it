---
title: Account di archiviazione in Azure Stack | Microsoft Docs
description: Informazioni su come creare un account di archiviazione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/18/2019
ms.author: mabrigg
ms.lastreviewed: 1/18/2019
ms.openlocfilehash: 4123d4cec25bab116c642f1b89cd8eff4779af42
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252150"
---
# <a name="storage-accounts-in-azure-stack"></a>Account di archiviazione in Azure Stack
Gli account di archiviazione includono i servizi BLOB e tabelle e lo spazio dei nomi univoco per gli oggetti dati di archiviazione. Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account di archiviazione.

1. Nel computer POC di Azure Stack, accedere al `https://adminportal.local.azurestack.external` come [amministratore](azure-stack-connect-azure-stack.md), quindi fare clic su **+ crea una risorsa** > **dati + archiviazione**  >  **Account di archiviazione**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Nel **creare account di archiviazione** pannello, digitare un nome per l'account di archiviazione. Creare una nuova **gruppo di risorse**, o selezionarne uno esistente, quindi fare clic su **crea** per creare l'account di archiviazione.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Per visualizzare il nuovo account di archiviazione, fare clic su **tutte le risorse**, quindi cercare l'account di archiviazione e fare clic sul relativo nome.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Passaggi successivi
[Usare i modelli di Gestione risorse di Azure](user/azure-stack-arm-templates.md)

[Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md)

[Scarica la Guida di convalida di archiviazione coerenti con Azure Stack di Azure](https://aka.ms/azurestacktp1doc)
