---
title: Credenziali per la distribuzione del Servizio app di Azure | Microsoft Docs
description: Informazioni su come usare le credenziali per la distribuzione del Servizio app di Azure.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Il [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) supporta due tipi di credenziali per la [distribuzione di GIT locale](app-service-deploy-local-git.md) e la [distribuzione FTP/S](app-service-deploy-ftp.md). Queste credenziali non corrispondono alle credenziali di Azure Active Directory.

* **Credenziali a livello di utente**: un insieme di credenziali per tutto l'account Azure. Può essere usato per distribuire il Servizio app per qualsiasi app, in tutte le sottoscrizioni a cui l'account di Azure è autorizzato ad accedere. Si tratta dell'insieme di credenziali predefinito configurabile in **Servizi app** > **&lt;nome_app>** > **Credenziali per la distribuzione**. Corrisponde anche al set predefinito indicato nell'interfaccia utente grafica del portale, ad esempio nelle aree **Panoramica** e **Proprietà** della [pagina delle risorse](../azure-resource-manager/resource-group-portal.md#manage-resources) dell'app.

    > [!NOTE]
    > Quando si delega l'accesso alle risorse di Azure tramite controllo degli accessi in base al ruolo o autorizzazioni di coamministratore, ogni utente Azure che riceve l'accesso a un'app può usare le sue credenziali a livello utente fino a quando l'accesso non viene revocato. Queste credenziali di distribuzione non devono essere condivise con altri utenti di Azure.
    >
    >

* **Credenziali a livello di applicazione**: un insieme di credenziali per ogni applicazione. Può essere usato per distribuire solo in quella app. Le credenziali per ogni app sono generate automaticamente alla creazione dell'app stessa e si trovano nel suo profilo di pubblicazione. Non è possibile configurare manualmente le credenziali per un'applicazione, ma è possibile reimpostarle in qualsiasi momento.

    > [!NOTE]
    > Per consentire a un utente di accedere a queste credenziali tramite il controllo degli accessi in base al ruolo, è necessario assegnare all'utente il ruolo di collaboratore o un ruolo superiore per l'app Web. Poiché non hanno l'autorizzazione per la pubblicazione, i lettori non possono accedere a queste credenziali.
    >
    >

## <a name="userscope"></a>Impostare e reimpostare le credenziali a livello di utente

È possibile configurare le credenziali a livello di utente nella [pagina delle risorse](../azure-resource-manager/resource-group-portal.md#manage-resources) di ogni app. Indipendentemente dall'app, le credenziali configurate si applicano a tutte le app e a tutte le sottoscrizioni nell'account di Azure dell'utente. 

Per configurare le credenziali a livello di utente:

1. Nel [portale di Azure](https://portal.azure.com), fare clic su Servizio app > **&lt;qualsiasi_app>** > **Credenziali per la distribuzione**.

    > [!NOTE]
    > È necessario disporre di almeno un'app nel portale per poter accedere alla pagina delle credenziali per la distribuzione. Tuttavia, con l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set) è possibile configurare le credenziali a livello di utente senza un'app esistente.

2. Configurare il nome utente e la password e quindi fare clic su **Salva**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella **Panoramica** dell'app,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

oltre a un nome utente per la distribuzione *FTP* nell'area **Proprietà** dell'app.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure non mostra la password di distribuzione a livello di utente. Se si dimentica la password, non è possibile recuperarla. Tuttavia, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
>  

## <a name="appscope"></a>Ottenere e reimpostare le credenziali a livello di utente
Per ogni app nel servizio app, le credenziali a livello di app vengono archiviate nel file XML del profilo di pubblicazione.

Per ottenere le credenziali a livello di app:

1. Nel [portale di Azure](https://portal.azure.com), fare clic su Servizio app >  **&lt;qualsiasi_app>** > **Panoramica**.

2. Fare clic su **...More** (...Altro) > **Recupera profilo di pubblicazione** per avviare il download di un file .PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Aprire il file .PublishSettings e trovare il tag `<publishProfile>` con l'attributo `publishMethod="FTP"`. Quindi, ottenere i relativi attributi `userName` e `password`.
Si tratta delle credenziali a livello di app.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Come per le credenziali a livello di utente, il nome utente di distribuzione dell'FTP è nel formato `<app_name>\<username>`, mentre quello della distribuzione è Git è solo `<username>` senza `<app_name>\` a precedere.

Per reimpostare le credenziali a livello di app:

1. Nel [portale di Azure](https://portal.azure.com), fare clic su Servizio app >  **&lt;qualsiasi_app>** > **Panoramica**.

2. Fare clic su **...More** (...Altro) > **Reimposta profilo di pubblicazione**. Fare clic su **Sì** per confermare la reimpostazione.

    L'operazione di reimpostazione invalida qualsiasi file .PublishSettings scaricato in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](app-service-deploy-local-git.md) o usando [FTP/S](app-service-deploy-ftp.md).
