---
title: Aggiungere Kubernetes per il Marketplace di Azure Stack | Microsoft Docs
description: Informazioni su come aggiungere Kubernetes in Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 9250e38eadb9d9b1e8bf0c09fa1a9a165e5e69f1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886263"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Aggiungere Kubernetes per il Marketplace di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

È possibile offrire Kubernetes come un elemento del Marketplace per gli utenti. Gli utenti possono distribuire Kubernetes in un'unica operazione coordinata.

Esaminare l'articolo seguente usando un modello di Azure Resource Manager per la distribuzione e provisioning delle risorse per un cluster autonomo di Kubernetes. L'elemento del Marketplace di Cluster Kubernetes 0.3.0 richiede la versione di Azure Stack 1808. Prima di iniziare, verificare le impostazioni di globale tenant di Azure e Azure Stack. Raccogliere le informazioni necessarie su Azure Stack. Aggiungere le risorse necessarie per il tenant e per il Marketplace di Azure Stack. Il cluster dipende da un server Ubuntu, uno script personalizzato e gli elementi di Kubernetes in marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Creare un piano, un'offerta e una sottoscrizione

Creare un piano, un'offerta e una sottoscrizione per l'elemento del Marketplace di Kubernetes. È anche possibile usare un piano esistente e dell'offerta.

1. Accedi al [portale di amministrazione.](https://adminportal.local.azurestack.external)

1. Creare un piano come piano base. Per istruzioni, vedere [creare un piano in Azure Stack](azure-stack-create-plan.md).

1. Creare un'offerta. Per istruzioni, vedere [creare un'offerta in Azure Stack](azure-stack-create-offer.md).

1. Selezionare **offre**e trovare l'offerta è stato creato.

1. Selezionare **Panoramica** nel pannello dell'offerta.

1. Selezionare **modifica stato**. Selezionare **pubblica**.

1. Selezionare **+ crea una risorsa** > **offerte e piani** > **sottoscrizione** per creare una nuova sottoscrizione.

    a. Immettere un **NomeVisualizzato**.

    b. Immettere un **utente**. Usare l'account di Azure AD associato al tenant.

    c. **Descrizione del provider**

    d. Impostare il **tenant di Directory** al tenant di Azure AD per Azure Stack. 

    e. Selezionare **offrono**. Selezionare il nome dell'offerta, è stato creato. Assicurarsi di annotare l'ID sottoscrizione.

## <a name="add-an-ubuntu-server-image"></a>Aggiungere un'immagine di Ubuntu server

Nel Marketplace, aggiungere l'immagine di Ubuntu Server seguente:

1. Accedi per il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi**, quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `UbuntuServer`.

1. Selezionare la versione più recente del server con il seguente profilo:
    - **Server di pubblicazione**: Canonical
    - **Offrire**: UbuntuServer
    - **SKU**: 16.04-LTS

1. Selezionare **scaricare.**

## <a name="add-a-custom-script-for-linux"></a>Aggiungere uno script personalizzato per Linux

Aggiunta di Kubernetes da Marketplace:

1. Aprire il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi** e quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `Custom Script for Linux`.

1. Selezionare lo script con il seguente profilo:
    - **Offrire**: Script personalizzato per Linux 2.0
    - **Versione**: 2.0.6
    - **Server di pubblicazione**: Microsoft Corp

    > [!Note]  
    > Potrebbe essere presente più di una versione dello Script personalizzata per Linux. È necessario aggiungere la versione corrispondente. Il Kubernetes richiede la versione esatta dell'elemento.

1. Selezionare **scaricare.**


## <a name="add-kubernetes-to-the-marketplace"></a>Aggiungere Kubernetes nel Marketplace

1. Aprire il [portale di amministrazione](https://adminportal.local.azurestack.external).

1. Selezionare **tutti i servizi** e quindi nel **amministrazione** categoria, seleziona **gestione Marketplace**.

1. Selezionare **+ Aggiungi da Azure**.

1. Immettere `Kubernetes`.

1. Selezionare `Kubernetes Cluster`.

1. Selezionare **scaricare.**

    > [!note]  
    > Può richiedere cinque minuti per l'elemento del marketplace venga visualizzato nel Marketplace.

    ![kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aggiornare o rimuovere di Kubernetes 

Quando si aggiorna l'elemento di Kubernetes, è necessario rimuovere l'elemento che si trova in Marketplace. È possibile seguire le istruzioni in questo articolo per aggiungere il Kubernetes nel Marketplace.

Per rimuovere l'elemento di Kubernetes:

1. Connettersi ad Azure Stack con PowerShell come un operatore. Per istruzioni, vedere [connettersi ad Azure Stack con PowerShell come un operatore](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Trovare l'elemento di un Kubernetes Cluster corrente nella raccolta.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Si noti, ad esempio nome dell'elemento corrente, `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. Usare il cmdlet di PowerShell seguente per rimuovere l'elemento:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un Kubernetes in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Panoramica dell'offerta di servizi in Azure Stack](azure-stack-offer-services-overview.md)