<properties
   pageTitle="Creare un'entità servizio con l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Descrive come usare l'interfaccia della riga di comando di Azure per creare un'applicazione Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con una password o un certificato."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)

Questo argomento illustra come usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](xplat-cli-install.md) per creare un'applicazione Active Directory (AD), ad esempio un processo automatizzato, un'applicazione o servizio che possa accedere ad altre risorse nella sottoscrizione. Con Azure Resource Manager è possibile usare il controllo degli accessi in base al ruolo per concedere azioni consentite nell'applicazione.

In questo articolo si creeranno due oggetti, ovvero l'applicazione Active Directory e l'entità servizio. L'applicazione Active Directory risiede nel tenant dove viene registrata l'app e definisce l'esecuzione del processo. L'entità servizio contiene l'identità dell'applicazione Active Directory e viene usata per l'assegnazione delle autorizzazioni. Dall'applicazione Active Directory è possibile creare molte entità servizio. Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](./active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Sono disponibili 2 opzioni per l'autenticazione dell'applicazione:

 - password: adatta quando un utente vuole accedere in modo interattivo durante l'esecuzione
 - certificato: adatta per script automatici che devono autenticarsi senza interazione dell'utente
 
## Creare l'applicazione Active Directory con password

In questa sezione si eseguiranno i passaggi per creare l'applicazione Active Directory con una password.

1. Passare al modulo Gestione risorse di Azure e accedere al proprio account.

        azure config mode arm
        azure login

2. Creare una nuova applicazione AD eseguendo il comando **azure ad app create**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Viene restituita l'applicazione Active Directory. La proprietà AppId è necessaria per la creazione di entità servizio e l'acquisizione di token di accesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Creare l'entità servizio e assegnarla al ruolo

1. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fornire manualmente le credenziali tramite l'interfaccia della riga di comando di Azure

È stata creata un'applicazione Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto è necessario accedere come applicazione per eseguire operazioni. È possibile fornire manualmente le credenziali per l'apèplicazione durante l'esecuzione di comandi o di script su richiesta.

1. Determinare il **TenantId** per la sottoscrizione che contiene l'entità servizio. Se si recupera l'ID tenant per la sottoscrizione attualmente autenticata, non è necessario specificare l'ID sottoscrizione come parametro. L'opzione **-r** recupera il valore senza le virgolette.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Come nome utente usare l'**AppId** usato durante la creazione dell'entità servizio. Se è necessario recuperare l'ID dell'applicazione, usare il comando seguente. Specificare il nome dell'applicazione Active Directory nel parametro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Accedere come entità servizio:

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    Verrà richiesto di specificare la password. Fornire la password specificata durante la creazione dell'applicazione Active Directory.

        info:    Executing command login
        Password: ********

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione Active Directory creata.

## Creare l'applicazione Active Directory con certificato

In questa sezione si eseguiranno i passaggi per creare un'entità servizio per un'applicazione Active Directory che usa un certificato per l'autenticazione. Per completare la procedura è necessario avere installato [OpenSSL](http://www.openssl.org/).

1. Creare un certificato autofirmato.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinare le chiavi pubblica e privata.

        cat privkey.pem cert.pem > examplecert.pem

3. Aprire il file **examplecert.pem** e copiare i dati del certificato. Cercare la lunga sequenza di caratteri tra **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**.

4. Creare una nuova applicazione Active Directory eseguendo il comando **azure ad app create** e fornire i dati del certificato copiati nel passaggio precedente come valore della chiave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Viene restituita l'applicazione di Active Directory. La proprietà AppId è necessaria per la creazione di entità servizio e l'acquisizione di token di accesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Creare l'entità servizio e assegnarla al ruolo

1. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Preparare i valori per lo script

Nello script si passeranno tre valori necessari per accedere come entità servizio. Saranno necessari:

- ID applicazione
- tenant id
- Identificazione personale del certificato

L'ID applicazione e l'identificazione personale del certificato sono stati visualizzati nei passaggi precedenti. Tuttavia, se occorre recuperare questi valori in un secondo momento, di seguito sono visualizzati i comandi necessari, insieme al comando per ottenere l'ID tenant.

1. Per recuperare l'identificazione personale del certificato e rimuovere i caratteri non necessari, usare:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Viene restituito un valore di identificazione personale simile a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Per recuperare l'ID tenant, usare:

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. Per recuperare l'ID applicazione, usare:

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale.

Per autenticarsi con l'interfaccia della riga di comando di Azure, fornire l'identificazione personale del certificato, il file del certificato, l'ID dell'applicazione e l'ID tenant.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

## Passaggi successivi
  
- Per esempi di autenticazione .NET, vedere [Azure Resource Manager SDK per .NET](resource-manager-net-sdk.md).
- Per esempi di autenticazione Java, vedere [Azure Resource Manager SDK per Java](resource-manager-java-sdk.md).
- Per esempi di autenticazione Python, vedere l'articolo relativo all'[autenticazione di gestione risorse per Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html).
- Per esempi di autenticazione REST, vedere [API REST di Resource Manager](resource-manager-rest-api.md).
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori sull'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
- Per ottenere altre informazioni sull'uso dei certificati e dell'interfaccia della riga di comando di Azure, vedere il blog relativo all'[autenticazione basata su certificati con le entità servizio di Azure dalla riga di comando di Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

<!---HONumber=AcomDC_0629_2016-->