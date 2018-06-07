---
title: Aggiungere un Cluster Kubernetes nel Marketplace Stack Azure | Documenti Microsoft
description: Informazioni su come aggiungere un Kubernetes Cluster a Stack Azure Marketplace.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603454"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Aggiungere un Cluster Kubernetes nel Marketplace Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

> [!note]  
> Il Kubernetes del contenitore Servizi di Azure (ACS) nello Stack di Azure è disponibile in anteprima privata. Per richiedere l'accesso per l'elemento del Kubernetes Marketplace necessaria per eseguire le istruzioni riportate in questo articolo [inviare una richiesta per ottenere l'accesso](https://aka.ms/azsk8).

È possibile offrire un Kubernetes Cluster come un elemento del Marketplace per gli utenti. Gli utenti possono distribuire Kubernetes in un'operazione singola, coordinata.

Il seguente articolo esaminare utilizzando un modello di gestione risorse di Azure per distribuire ed eseguire il provisioning delle risorse per un cluster di Kubernetes autonomo. Prima di iniziare, verificare le impostazioni di globale tenant di Azure e Azure Stack. Raccogliere le informazioni necessarie sullo Stack di Azure. Aggiungere le risorse necessarie per il tenant e Azure Marketplace dello Stack. Il cluster dipende dal server Ubuntu, uno script personalizzato e gli elementi Kubernetes Cluster sia in marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Creare un piano, un'offerta e una sottoscrizione

Creare un piano, un'offerta e una sottoscrizione per l'elemento Kubernetes Cluster Marketplace. È inoltre possibile utilizzare un piano esistente e offrire.

1. Accedi al [portale di amministrazione.](https://adminportal.local.azurestack.external)

2. Creare un piano del piano di base. Per istruzioni, vedere [creare un piano nello Stack di Azure](azure-stack-create-plan.md).

3. Creare un'offerta. Per istruzioni, vedere [creare un'offerta nello Stack di Azure](azure-stack-create-offer.md).

4. Selezionare **offre**e individuare l'offerta è stato creato.

5. Selezionare **Panoramica** nel pannello offerta.

6. Selezionare **cambiare lo stato**. Selezionare **pubblica**.

7. Selezionare **+ nuovo** > **offre e piani** > **sottoscrizione** per creare una nuova sottoscrizione.

    a. Immettere un **nome visualizzato**.

    b. Immettere un **utente**. Usare l'account di Azure Active Directory associato al tenant.

    c. **Descrizione del provider**

    d. Impostare il **tenant di Directory** al tenant di Azure AD per lo Stack di Azure. 

    e. Selezionare **offrono**. Selezionare il nome dell'offerta che è stato creato. Prendere nota dell'ID della sottoscrizione.

## <a name="add-an-ubuntu-server-image"></a>Aggiungere un'immagine server Ubuntu

Aggiungere l'immagine Ubuntu Server seguente Marketplace:

1. Accedi ai [portale di amministrazione](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **Marketplace gestione**.

3. Selezionare **+ Aggiungi da Azure**.

4. Immettere `UbuntuServer`.

5. Selezionare il server con il seguente profilo:
    - **Server di pubblicazione**: canonico
    - **Offrono**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **Versione**: 16.04.201802220

    > [!Note]  
    > Potrebbe essere elencata più di una versione di Ubuntu Server 16.04 LTS. È necessario aggiungere la versione corrispondente. Il Kubernetes Cluster richiede la versione esatta dell'elemento.

6. Selezionare **scaricare.**

## <a name="add-a-custom-script-for-linux"></a>Aggiungere uno script personalizzato per Linux

Aggiungere il Cluster Kubernetes del Marketplace:

1. Aprire la [portale di amministrazione](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **Marketplace gestione**.

3. Selezionare **+ Aggiungi da Azure**.

4. Immettere `Custom Script for Linux`.

5. Selezionare lo script con il seguente profilo:
    - **Offrono**: Script personalizzato per Linux 2.0
    - **Versione**: 2.0.3
    - **Server di pubblicazione**: Microsoft Corp

    > [!Note]  
    > Potrebbe essere elencata più di una versione dello Script personalizzato per Linux. È necessario aggiungere la versione corrispondente. Il Kubernetes Cluster richiede la versione esatta dell'elemento.

6. Selezionare **scaricare.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Aggiungere il Kubernetes Cluster in marketplace

1. Aprire la [portale di amministrazione](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **Marketplace gestione**.

3. Selezionare **+ Aggiungi da Azure**.

4. Immettere `Kubernetes Cluster`.

5. Selezionare `Kubernetes Cluster`.

6. Selezionare **scaricare.**

    > [!note]  
    > Potrebbero occorrere cinque minuti per l'elemento del marketplace siano visibili nel Marketplace.

    ![Kubernetes Cluster](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Aggiornare o rimuovere il Kubernetes Cluster 

Quando si aggiorna l'elemento Kubernetes Cluster, è necessario rimuovere l'elemento che è in Marketplace. È quindi possibile seguire le istruzioni in questo articolo per aggiungere il Kubernetes Cluster in marketplace.

Per rimuovere l'elemento Kubernetes Cluster:

1. Si noti, ad esempio nome dell'elemento corrente, `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Connettersi a Azure Stack con PowerShell.

3. Usare il cmdlet di PowerShell seguente per rimuovere l'elemento:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un Cluster Kubernetes allo Stack di Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Panoramica dell'offerta di servizi di Azure Stack](azure-stack-offer-services-overview.md)