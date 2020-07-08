---
title: Configurare le credenziali di distribuzione
description: Informazioni sui tipi di credenziali di distribuzione presenti nel servizio app di Azure e su come configurarle e usarle.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649139"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurazione delle credenziali per la distribuzione del Servizio app di Azure
Il [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) supporta due tipi di credenziali per la [distribuzione di GIT locale](deploy-local-git.md) e la [distribuzione FTP/S](deploy-ftp.md). Queste credenziali sono diverse dalle credenziali della sottoscrizione di Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Configurare le credenziali a livello di utente

È possibile configurare le credenziali a livello di utente nella [pagina delle risorse](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) di ogni app. Indipendentemente dall'app, le credenziali configurate si applicano a tutte le app e a tutte le sottoscrizioni nell'account di Azure dell'utente. 

### <a name="in-the-cloud-shell"></a>In Cloud Shell

Per configurare l'utente della distribuzione in [Cloud Shell](https://shell.azure.com), eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "\@". 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

### <a name="in-the-portal"></a>Nel portale

È necessario disporre di almeno un'app nel portale di Azure per poter accedere alla pagina delle credenziali per la distribuzione. Per configurare le credenziali a livello di utente:

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, selezionare **Servizi app** >  **\<any_app >**  > **Centro distribuzione** > **FTP** > **Dashboard**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    In alternativa, se è già stata configurata la distribuzione Git, selezionare **Servizi app** >  **&lt;any_app >**  > **Centro distribuzione** > **FTP/Credenziali**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selezionare **Credenziali utente**, configurare il nome utente e la password e quindi selezionare **Salva credenziali**.

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella pagina **Panoramica** dell'app,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se è configurata la distribuzione Git, nella pagina viene visualizzato un **nome utente Git/distribuzione**; in caso contrario, un **nome utente FTP/distribuzione**.

> [!NOTE]
> Azure non mostra la password di distribuzione a livello di utente. Se si dimentica la password, è possibile reimpostare le credenziali seguendo i passaggi descritti in questa sezione.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Usare le credenziali a livello di utente con FTP/FTPS

L'autenticazione a un endpoint FTP/FTPS con le credenziali a livello di utente richiede un nome utente nel formato seguente: `<app-name>\<user-name>`

Poiché le credenziali a livello di utente sono collegate all'utente e non a una risorsa specifica, il nome utente deve essere in questo formato per indirizzare l'azione di accesso all'endpoint corretto dell'app.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Ottenere e reimpostare le credenziali a livello di utente
Per ottenere le credenziali a livello di app:

1. Nel [portale di Azure](https://portal.azure.com), nel menu a sinistra, selezionare **Servizi app** >  **&lt;any_app >**  > **Centro distribuzione** > **FTP/Credenziali**.

2. Fare clic su **Credenziali dell'app** e selezionare il collegamento **Copia** per copiare nome utente o password.

Per reimpostare le credenziali a livello di app, selezionare **Reimposta credenziali** nella stessa finestra di dialogo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
