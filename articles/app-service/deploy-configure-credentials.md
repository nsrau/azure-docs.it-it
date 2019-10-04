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
ms.topic: article
ms.date: 08/14/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: fc9445b64baae0e625b62356fee381329b01e8fd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098489"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Il [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) supporta due tipi di credenziali per la [distribuzione di GIT locale](deploy-local-git.md) e la [distribuzione FTP/S](deploy-ftp.md). Queste credenziali non corrispondono alle credenziali della sottoscrizione di Azure.

* **Credenziali a livello di utente**: un insieme di credenziali per tutto l'account Azure. Può essere usato per distribuire il Servizio app per qualsiasi app, in tutte le sottoscrizioni a cui l'account di Azure è autorizzato ad accedere. Corrisponde al set predefinito indicato nell'interfaccia utente grafica del portale, ad esempio nelle aree **Panoramica** e **Proprietà** della [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) dell'app. Quando a un utente viene concesso l'accesso alle app tramite il controllo degli accessi in base al ruolo o le autorizzazioni di coamministratore, l'utente può usare le proprie credenziali a livello di utente fino a quando l'accesso non viene revocato. Non condividere queste credenziali con altri utenti di Azure.

* **Credenziali a livello di applicazione**: un insieme di credenziali per ogni applicazione. Può essere usato per distribuire solo in quella app. Le credenziali per ogni app vengono generate automaticamente al momento della creazione dell'app. Non possono essere configurate manualmente, ma possono essere reimpostate in qualsiasi momento. Per ottenere l'accesso alle credenziali a livello di app tramite il controllo degli accessi in base al ruolo, l'utente deve avere il ruolo di Collaboratore o un ruolo superiore per l'app. I lettori non sono autorizzati alla pubblicazione e quindi non possono accedere a queste credenziali.

## <a name="userscope"></a>Configurare le credenziali a livello di utente

È possibile configurare le credenziali a livello di utente nella [pagina delle risorse](../azure-resource-manager/manage-resources-portal.md#manage-resources) di ogni app. Indipendentemente dall'app, le credenziali configurate si applicano a tutte le app e a tutte le sottoscrizioni nell'account di Azure dell'utente. 

### <a name="in-the-cloud-shell"></a>Nella Cloud Shell

Per configurare l'utente della distribuzione nella [cloud Shell](https://shell.azure.com), eseguire il comando [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "\@". 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

### <a name="in-the-portal"></a>Nel portale

Nel portale di Azure è necessario disporre di almeno un'app prima di poter accedere alla pagina delle credenziali di distribuzione. Per configurare le credenziali a livello di utente:

1. Nel menu a sinistra del [portale di Azure](https://portal.azure.com)selezionare **Servizi** >  >  >  **\<app any_app >** **Dashboard** **FTP** > del**centro distribuzione**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    In alternativa, se è già stata configurata la distribuzione git, selezionare **Servizi** >  **&lt;app any_app >**  > **Deployment Center** > **ftp/Credentials**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selezionare **credenziali utente**, configurare il nome utente e la password e quindi selezionare **Salva credenziali**.

Dopo aver impostato le credenziali di distribuzione, è possibile trovare il nome utente della distribuzione *git* nella pagina **Panoramica** dell'app,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se è configurata la distribuzione git, nella pagina viene visualizzato un **nome utente git/Deployment**. in caso contrario, un **nome utente FTP/distribuzione**.

> [!NOTE]
> Azure non mostra la password di distribuzione a livello di utente. Se si dimentica la password, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Usare le credenziali a livello di utente con FTP/FTPS

L'autenticazione a un endpoint FTP/FTPS usando le credenziali a livello di utente richiede un nome utente nel formato seguente:`<app-name>\<user-name>`

Poiché le credenziali a livello di utente sono collegate all'utente e non a una risorsa specifica, il nome utente deve essere in questo formato per indirizzare l'azione di accesso all'endpoint dell'app corretto.

## <a name="appscope"></a>Ottenere e reimpostare le credenziali a livello di utente
Per ottenere le credenziali a livello di app:

1. Nel [portale di Azure](https://portal.azure.com), dal menu a sinistra, selezionare **Servizi** >  >  >  **&lt;app any_app >** **Deployment Center** **ftp/Credentials**.

2. Selezionare **credenziali dell'app**e selezionare il collegamento **copia** per copiare il nome utente o la password.

Per reimpostare le credenziali a livello di app, selezionare **Reimposta credenziali** nella stessa finestra di dialogo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
