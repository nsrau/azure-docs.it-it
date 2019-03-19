---
title: Configurare le credenziali per la distribuzione - Servizio app di Azure | Microsoft Docs
description: Informazioni su come usare le credenziali per la distribuzione del Servizio app di Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/10/2019
ms.author: cephalin;byvinyal
ms.custom: seodec18
ms.openlocfilehash: df874ab77c88f05b048b1f9d10873943b7bebf36
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884388"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Il [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) supporta due tipi di credenziali per la [distribuzione di GIT locale](deploy-local-git.md) e la [distribuzione FTP/S](deploy-ftp.md). Queste credenziali non corrispondono alle credenziali di Azure Active Directory.

* **Credenziali a livello di utente**: un insieme di credenziali per tutto l'account Azure. Può essere usato per distribuire il Servizio app per qualsiasi app, in tutte le sottoscrizioni a cui l'account di Azure è autorizzato ad accedere. Corrisponde al set predefinito indicato nell'interfaccia utente grafica del portale, ad esempio nelle aree **Panoramica** e **Proprietà** della [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) dell'app. Quando a un utente viene concesso l'accesso all'app tramite il controllo degli accessi in base al ruolo o le autorizzazioni di coamministratore, tale utente può usare le proprie credenziali a livello di utente fino a quando non viene revocato l'accesso. Non condividere queste credenziali con altri utenti di Azure.

* **Credenziali a livello di applicazione**: un insieme di credenziali per ogni applicazione. Può essere usato per distribuire solo in quella app. Le credenziali per ogni app vengono generate automaticamente al momento della creazione dell'app. Non possono essere configurate manualmente, ma possono essere reimpostate in qualsiasi momento. Per ottenere l'accesso alle credenziali a livello di app tramite il controllo degli accessi in base al ruolo, l'utente deve avere il ruolo di Collaboratore o un ruolo superiore per l'app. I lettori non sono autorizzati alla pubblicazione e quindi non possono accedere a queste credenziali.

## <a name="userscope"></a>Impostare e reimpostare le credenziali a livello di utente

È possibile configurare le credenziali a livello di utente nella [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) di ogni app. Indipendentemente dall'app, le credenziali configurate si applicano a tutte le app e a tutte le sottoscrizioni nell'account di Azure dell'utente. 

Per configurare le credenziali a livello di utente:

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, fare clic su **Servizi app** > **&lt;qualsiasi_app >** > **Centro distribuzione** > **Credenziali distribuzione**.

    È necessario disporre di almeno un'app nel portale per poter accedere alla pagina delle credenziali per la distribuzione. Tuttavia, con l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) è possibile configurare le credenziali a livello di utente senza un'app esistente.

2. Fare clic su **Credenziali utente**, configurare il nome utente e la password e quindi fare clic su **Salva credenziali**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella **Panoramica** dell'app,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

oltre a un nome utente per la distribuzione *FTP* nell'area **Proprietà** dell'app.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure non mostra la password di distribuzione a livello di utente. Se si dimentica la password, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
>  

## <a name="use-user-level-credentials-with-ftpftps"></a>Usare le credenziali a livello di utente con FTP/FTPS

L'autenticazione a un endpoint FTP/FTPS con requirers credenziali a livello di utente un nome utente nel formato seguente: `<app-name>\<user-name>`

Poiché le credenziali a livello di utente sono collegate all'utente e non una risorsa specifica, il nome utente deve essere nel formato per indirizzare l'azione di accesso per l'endpoint dell'app a destra.

## <a name="appscope"></a>Ottenere e reimpostare le credenziali a livello di utente
Per ottenere le credenziali a livello di app:

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, fare clic su **Servizi app** > **&lt;qualsiasi_app >** > **Centro distribuzione** > **Credenziali distribuzione**.

2. Fare clic su **Credenziali dell'app** e fare clic sul collegamento **Copia** per copiare nome utente o password.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Per reimpostare le credenziali a livello di app, fare clic su **Reimposta credenziali** nella stessa finestra di dialogo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
