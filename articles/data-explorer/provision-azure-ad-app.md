---
title: Creare un'applicazione Azure AD in Azure Data ExplorerCreate an Azure AD application in Azure Data Explorer
description: Informazioni su come creare un'applicazione Azure AD in Azure Data Explorer.Learn how to create an Azure AD application in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550513"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Creare una registrazione dell'applicazione Azure Active Directory in Azure Data ExplorerCreate an Azure Active Directory application registration in Azure Data Explorer

L'autenticazione dell'applicazione Azure Active Directory (Azure AD) viene usata per le applicazioni, ad esempio un servizio automatico o un flusso pianificato, che devono accedere ad Azure Data Explorer senza che sia presente un utente. Se ci si connette a un database di Azure Data Explorer usando un'applicazione, ad esempio un'app Web, è consigliabile eseguire l'autenticazione tramite l'autenticazione dell'entità servizio. Questo articolo descrive in dettaglio come creare e registrare un'entità servizio di Azure AD e quindi autorizzarla ad accedere a un database di Azure Data Explorer.This article details how to create and register an Azure AD service principal and then authorize it to access an Azure Data Explorer database.

## <a name="create-azure-ad-application-registration"></a>Creare la registrazione dell'applicazione Azure ADCreate Azure AD application registration

L'autenticazione delle applicazioni di Azure AD richiede la creazione e la registrazione di un'applicazione con Azure AD. Un'entità servizio viene creata automaticamente quando viene creata la registrazione dell'applicazione in un tenant di Azure AD. 

1. Accedere al [portale](https://portal.azure.com) di `Azure Active Directory` Azure e aprire il pannello

    ![Selezionare Azure Active Directory dal menu del portale](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Selezionare il pannello **Registrazioni app** e selezionare **Nuova registrazione**

    ![Avviare una nuova registrazione dell'app](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Specificare le informazioni seguenti: 

    * **Nome** 
    * **Tipi di account supportati**
    * **Reindirizzamento URI** > **Web**
        > [!IMPORTANT] 
        > Il tipo di applicazione deve essere **Web**. In questo caso, l'URI è facoltativo e viene lasciato vuoto.
    * Seleziona **Registrazione**

    ![Registrare la registrazione di una nuova app](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Selezionare il pannello **Panoramica** e copiare **l'ID applicazione.**

    > [!NOTE]
    > È necessario l'ID applicazione per autorizzare l'entità servizio ad accedere al database.

    ![Copiare l'ID di registrazione dell'app](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Nel pannello **Certificati & segreti** selezionare Nuovo **segreto client**

    ![Avviare la creazione del segreto clientStart creation of client secret](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > In questo articolo viene descritto l'utilizzo di un segreto client per le credenziali dell'applicazione.  È anche possibile usare un certificato X509 per autenticare l'applicazione. Selezionare **Carica certificato** e seguire le istruzioni per caricare la parte pubblica del certificato.

1. Immettere una descrizione, una scadenza e selezionare **Aggiungi**

    ![Immettere i parametri del segreto client](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Copiare il valore della chiave.

    > [!NOTE]
    > Quando si esce da questa pagina, il valore della chiave non sarà accessibile.  È necessaria la chiave per configurare le credenziali client per il database.

    ![Copia valore chiave segreto client](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

L'applicazione viene creata. Se è necessario solo l'accesso a una risorsa di Azure Data Explorer autorizzata, ad esempio nell'esempio a livello di codice seguente, ignorare la sezione successiva. Per il supporto delle autorizzazioni delegate, vedere [Configurare le autorizzazioni delegate per la registrazione dell'applicazione](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Configurare le autorizzazioni delegate per la registrazione dell'applicazione

Se l'applicazione deve accedere ad Azure Data Explorer usando le credenziali dell'utente chiamante, configurare le autorizzazioni delegate per la registrazione dell'applicazione. Ad esempio, se si sta creando un'API Web per accedere ad Azure Data Explorer e si vuole eseguire l'autenticazione usando le credenziali dell'utente che *chiama* l'API.  

1. Nel pannello **Autorizzazioni API** selezionare **Aggiungi un'autorizzazione.**
1. Selezionare API utilizzate **dall'organizzazione**. Cercare e selezionare **Azure Data Explorer**.

    ![Aggiungere l'autorizzazione dell'API di Azure Data ExplorerAdd Azure Data Explorer API permission](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. In **Autorizzazioni delegate**selezionare le **caselle di user_impersonation** e Aggiungi **autorizzazioni**

    ![Selezionare le autorizzazioni delegate con la rappresentazione utente](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Concedere all'entità servizio l'accesso a un database di Azure Data ExplorerGrant the service principal access to an Azure Data Explorer database

Ora che è stata creata la registrazione dell'applicazione dell'entità servizio, è necessario concedere l'accesso dell'entità servizio corrispondente al database di Azure Data Explorer.Now that your service principal application registration is created, you need to grant the corresponding service principal access to your Azure Data Explorer database. 

1. [Nell'interfaccia utente Web](https://dataexplorer.azure.com/)connettersi al database e aprire una scheda di query.

1. Eseguire il comando seguente:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Ad esempio:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    L'ultimo parametro è una stringa che viene visualizzata come notori quando si esegue una query sui ruoli associati a un database.
    
    > [!NOTE]
    > Dopo aver creato la registrazione dell'applicazione, potrebbe verificarsi un ritardo di diversi minuti fino a quando Azure Data Explorer può farvi riferimento. Se, durante l'esecuzione del comando, viene visualizzato un errore che indica che l'applicazione non viene trovata, attendere e riprovare.

Per ulteriori informazioni, vedere Gestione dei [ruoli di sicurezza](/azure/kusto/management/security-roles) e autorizzazioni di [inserimento](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md).  

## <a name="using-application-credentials-to-access-a-database"></a>Utilizzo delle credenziali dell'applicazione per accedere a un databaseUsing application credentials to access a database

Usare le credenziali dell'applicazione per accedere a livello di codice al database tramite la libreria client di [Azure Data Explorer.](/azure/kusto/api/netfx/about-kusto-data.md)

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Specificare l'ID applicazione e la chiave della registrazione dell'applicazione (entità servizio) creata in precedenza.

Per altre informazioni, vedere [Eseguire l'autenticazione con Azure AD per l'accesso ad Azure Data Explorer](/azure/kusto/management/access-control/how-to-authenticate-with-aad) e usare l'insieme di credenziali delle chiavi di Azure con [l'app Web .NET Core.For](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)more information, see authenticate with Azure AD for Azure Data Explorer access and use Azure Key Vault with .NET Core web app .

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="invalid-resource-error"></a>Errore di risorsa non valida

Se l'applicazione viene usata per autenticare utenti o applicazioni per l'accesso ad Azure Data Explorer, è necessario configurare le autorizzazioni delegate per l'applicazione di servizio Azure Data Explorer.If your application is used to authenticate users or applications for Azure Data Explorer access, you must set up delegated permissions for Azure Data Explorer service application. Dichiarare che l'applicazione può autenticare utenti o applicazioni per l'accesso ad Azure Data Explorer.Declare your application can authenticate users or applications for Azure Data Explorer access. In caso contrario, si verifica un errore simile al seguente, quando viene effettuato un tentativo di autenticazione:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

È necessario seguire le istruzioni sulla configurazione delle [autorizzazioni delegate per l'applicazione](#configure-delegated-permissions-for-the-application-registration)di servizio Azure Data Explorer.

### <a name="enable-user-consent-error"></a>Abilita errore di consenso dell'utente

L'amministratore tenant di Azure AD può attuare criteri che impediscono agli utenti tenant di concedere il consenso alle applicazioni. Questa situazione comporterà un errore simile al seguente, quando un utente tenta di accedere all'applicazione:This situation will result in an error similar to the following, when a user tries to log in to your application:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

È necessario contattare l'amministratore di Azure AD per concedere il consenso a tutti gli utenti nel tenant o abilitare il consenso dell'utente per l'applicazione specifica.

## <a name="next-steps"></a>Passaggi successivi

* Vedere Stringhe di [connessione Kusto](/azure/kusto/api/connection-strings/kusto.md) per l'elenco delle stringhe di connessione supportate.
