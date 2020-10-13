---
title: Configurare le credenziali di distribuzione
description: Informazioni sui tipi di credenziali di distribuzione presenti nel servizio app di Azure e su come configurarle e usarle.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 50b3cae00110a64e4d95171822bf1d2a282d2cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715414"
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

1. Nel [portale di Azure](https://portal.azure.com), dal menu a sinistra, selezionare **app Services**  >  **\<any_app>**  >  **Deployment Center**  >  **FTP**  >  **Dashboard**.

    ![Mostra come è possibile selezionare il dashboard FTP dal centro distribuzione in app Azure Services.](./media/app-service-deployment-credentials/access-no-git.png)

    In alternativa, se è già stata configurata la distribuzione Git, selezionare **Servizi app** >  **&lt;any_app >**  > **Centro distribuzione** > **FTP/Credenziali**.

    ![Mostra come è possibile selezionare il dashboard FTP da Deployment Center in app Azure Services per la distribuzione git configurata.](./media/app-service-deployment-credentials/access-with-git.png)

2. Selezionare **Credenziali utente**, configurare il nome utente e la password e quindi selezionare **Salva credenziali**.

Dopo aver impostato le credenziali per la distribuzione, è possibile trovare il nome utente per la distribuzione di *GIT* nella pagina **Panoramica** dell'app,

![Mostra come trovare il nome utente della distribuzione git nella pagina Panoramica dell'app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

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

## <a name="disable-basic-authentication"></a>Disabilitare l'autenticazione di base

Alcune organizzazioni devono soddisfare i requisiti di sicurezza e piuttosto disabilitare l'accesso tramite FTP o WebDeploy. In questo modo, i membri dell'organizzazione possono accedere ai propri servizi app solo tramite le API controllate da Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Per disabilitare l'accesso FTP al sito, eseguire il comando dell'interfaccia della riga di comando seguente. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che l'accesso FTP sia bloccato, è possibile provare a eseguire l'autenticazione usando un client FTP come FileZilla. Per recuperare le credenziali di pubblicazione, passare al pannello panoramica del sito e fare clic su Scarica profilo di pubblicazione. Usare il nome host FTP del file, il nome utente e la password per l'autenticazione e verrà restituita una risposta di errore 401, che indica che l'utente non è autorizzato.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Per disabilitare l'accesso con autenticazione di base alla porta WebDeploy e al sito SCM, eseguire il comando dell'interfaccia della riga di comando seguente. Sostituire i segnaposto con il gruppo di risorse e il nome del sito. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Per verificare che le credenziali del profilo di pubblicazione siano bloccate su WebDeploy, provare a [pubblicare un'app Web con Visual Studio 2019](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

### <a name="disable-access-to-the-api"></a>Disabilitare l'accesso all'API

L'API nella sezione precedente è supportata dal controllo degli accessi in base al ruolo di Azure (RBAC di Azure), il che significa che è possibile [creare un ruolo personalizzato](https://docs.microsoft.com/azure/role-based-access-control/custom-roles#steps-to-create-a-custom-role) e assegnare utenti priveldged inferiori al ruolo, in modo che non possano abilitare l'autenticazione di base in tutti i siti. Per configurare il ruolo personalizzato, [seguire queste istruzioni](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

È anche possibile usare [monitoraggio di Azure](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) per controllare le richieste di autenticazione riuscite e usare i [criteri di Azure](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) per applicare questa configurazione per tutti i siti nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare queste credenziali per distribuire l'app da [GIT locale](deploy-local-git.md) o usando [FTP/S](deploy-ftp.md).
