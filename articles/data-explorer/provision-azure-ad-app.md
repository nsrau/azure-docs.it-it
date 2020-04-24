---
title: Creare un'applicazione Azure AD in Azure Esplora dati
description: Informazioni su come creare un'applicazione Azure AD in Azure Esplora dati.
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
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Creare una registrazione dell'applicazione Azure Active Directory in Azure Esplora dati

L'autenticazione dell'applicazione Azure Active Directory (Azure AD) viene usata per le applicazioni, ad esempio un servizio automatico o un flusso pianificato, che devono accedere a Esplora dati di Azure senza che sia presente un utente. Se ci si connette a un database di Azure Esplora dati usando un'applicazione, ad esempio un'app Web, è necessario eseguire l'autenticazione con l'autenticazione basata su entità servizio. Questo articolo illustra come creare e registrare un'entità servizio Azure AD e come autorizzarla ad accedere a un database di Esplora dati di Azure.

## <a name="create-azure-ad-application-registration"></a>Creare Azure AD registrazione dell'applicazione

Azure AD l'autenticazione dell'applicazione richiede la creazione e la registrazione di un'applicazione con Azure AD. Un'entità servizio viene creata automaticamente quando viene creata la registrazione dell'applicazione in un tenant Azure AD. 

1. Accedere a [portale di Azure](https://portal.azure.com) e aprire il `Azure Active Directory` pannello

    ![Selezionare Azure Active Directory dal menu del portale](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Selezionare il pannello **registrazioni app** e selezionare **nuova registrazione**

    ![Avvia una nuova registrazione dell'app](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Specificare le informazioni seguenti: 

    * **Nome** 
    * **Tipi di account supportati**
    * **Reindirizza URI** > **Web**
        > [!IMPORTANT] 
        > Il tipo di applicazione deve essere **Web**. L'URI è facoltativo e viene lasciato vuoto in questo caso.
    * Seleziona **Registro**

    ![Registrare una nuova registrazione per l'app](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Selezionare il pannello **Panoramica** e copiare l' **ID applicazione**.

    > [!NOTE]
    > È necessario l'ID applicazione per autorizzare l'entità servizio ad accedere al database.

    ![Copia ID registrazione app](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Nel pannello **certificati & segreti** selezionare **nuovo segreto client**

    ![Avvia creazione del segreto client](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Questo articolo descrive l'uso di un segreto client per le credenziali dell'applicazione.  È anche possibile usare un certificato X509 per autenticare l'applicazione. Selezionare **Carica certificato** e seguire le istruzioni per caricare la parte pubblica del certificato.

1. Immettere una descrizione, la scadenza e selezionare **Aggiungi**

    ![Immettere i parametri del segreto client](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Copiare il valore della chiave.

    > [!NOTE]
    > Quando si esce dalla pagina, il valore della chiave non sarà accessibile.  È necessario disporre della chiave per configurare le credenziali client nel database.

    ![Copia valore chiave privata client](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Viene creata l'applicazione. Se è necessario accedere solo a una risorsa di Azure Esplora dati autorizzata, come nell'esempio di codice riportato di seguito, ignorare la sezione successiva. Per il supporto delle autorizzazioni delegate, vedere [configurare le autorizzazioni delegate per la registrazione dell'applicazione](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Configurare le autorizzazioni delegate per la registrazione dell'applicazione

Se l'applicazione deve accedere ad Azure Esplora dati usando le credenziali dell'utente chiamante, configurare le autorizzazioni delegate per la registrazione dell'applicazione. Ad esempio, se si sta creando un'API Web per accedere ad Azure Esplora dati e si vuole eseguire l'autenticazione usando le credenziali dell'utente che *chiama* l'API.  

1. Nel pannello **autorizzazioni API** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare **le API utilizzate dall'organizzazione**. Cercare e selezionare **Esplora dati di Azure**.

    ![Aggiungere l'autorizzazione per l'API Esplora dati di Azure](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. In **autorizzazioni delegate**selezionare la casella di **user_impersonation** e **aggiungere le autorizzazioni**

    ![Selezionare le autorizzazioni delegate con la rappresentazione utente](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Concedere all'entità servizio l'accesso a un database Esplora dati di Azure

Ora che è stata creata la registrazione dell'applicazione dell'entità servizio, è necessario concedere all'entità servizio corrispondente l'accesso al database di Esplora dati di Azure. 

1. Nell' [interfaccia utente Web](https://dataexplorer.azure.com/)connettersi al database e aprire una scheda query.

1. Eseguire il comando seguente:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Ad esempio:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    L'ultimo parametro è una stringa che viene visualizzata come note quando si esegue una query sui ruoli associati a un database.
    
    > [!NOTE]
    > Dopo la creazione della registrazione dell'applicazione, è possibile che si verifichi un ritardo di alcuni minuti fino a quando Azure Esplora dati possa farvi riferimento. Se, quando si esegue il comando, viene visualizzato un messaggio di errore che indica che l'applicazione non è stata trovata, attendere e riprovare.

Per altre informazioni, vedere [gestione dei ruoli di sicurezza](/azure/kusto/management/security-roles) e [autorizzazioni](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)di inserimento.  

## <a name="using-application-credentials-to-access-a-database"></a>Utilizzo delle credenziali dell'applicazione per accedere a un database

Usare le credenziali dell'applicazione per accedere a livello di codice al database usando la [libreria client di Azure Esplora dati](/azure/kusto/api/netfx/about-kusto-data.md).

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

Per altre informazioni, vedere [eseguire l'autenticazione con Azure ad per l'accesso Esplora dati di Azure](/azure/kusto/management/access-control/how-to-authenticate-with-aad) e [usare Azure Key Vault con l'app Web .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="invalid-resource-error"></a>Errore di risorsa non valido

Se l'applicazione viene usata per autenticare gli utenti o le applicazioni per l'accesso Esplora dati di Azure, è necessario configurare le autorizzazioni delegate per l'applicazione di servizio Esplora dati di Azure. Dichiarare che l'applicazione può autenticare utenti o applicazioni per l'accesso Esplora dati di Azure. In caso contrario, verrà generato un errore simile al seguente, quando viene eseguito un tentativo di autenticazione:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

È necessario seguire le istruzioni nella pagina relativa alla [configurazione delle autorizzazioni delegate per l'applicazione di servizio Esplora dati di Azure](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Abilita errore di consenso dell'utente

L'amministratore tenant di Azure AD può applicare un criterio che impedisce agli utenti del tenant di concedere il consenso alle applicazioni. Questa situazione genera un errore simile al seguente quando un utente tenta di accedere all'applicazione:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

È necessario contattare l'amministratore di Azure AD per concedere il consenso per tutti gli utenti del tenant o consentire l'autorizzazione dell'utente per l'applicazione specifica.

## <a name="next-steps"></a>Passaggi successivi

* Per l'elenco delle stringhe di connessione supportate, vedere [kusto Connection Strings](/azure/kusto/api/connection-strings/kusto.md) .
