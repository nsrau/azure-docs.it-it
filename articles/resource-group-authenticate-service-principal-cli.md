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
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)

Quando si ha un'applicazione o uno script che deve accedere alle risorse, è probabile che si voglia eseguire il processo con le credenziali dell'utente. L'utente può avere autorizzazioni diverse da quelle che si vogliono assegnare al processo e responsabilità dell'utente relative al processo potrebbero cambiare. In alternativa, è possibile creare un'identità per l'applicazione che includa le credenziali di autenticazione e le assegnazioni di ruolo. L'applicazione esegue l'accesso con tale identità ogni volta che viene eseguita. Questo argomento illustra come usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](xplat-cli-install.md) per impostare tutte le informazioni necessarie a un'applicazione per l'esecuzione con credenziali e identità proprie.

In questo articolo si creeranno due oggetti: l'applicazione Active Directory (AD) e l'entità servizio. L'applicazione AD include le credenziali, ovvero un ID applicazione e una password o un certificato. L'entità servizio include l'assegnazione di ruolo. Dall'applicazione Active Directory è possibile creare molte entità servizio. Questo argomento è incentrato su un'applicazione con un tenant singolo in cui è previsto che l'applicazione venga eseguita all'interno del sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione. Quando l'applicazione deve essere eseguita in molte organizzazioni, è anche possibile creare applicazioni multi-tenant. Le applicazioni multi-tenant si usano in genere per le applicazioni software come un servizio (SaaS, Software-as-a-Service). Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

Sono molti i concetti che è opportuno comprendere quando si lavora con Active Directory. Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](./active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Con l'interfaccia della riga di comando di Azure sono disponibili 2 opzioni per l'autenticazione dell'applicazione AD:

 - password
 - certificato

Se, dopo avere impostato l'applicazione AD, si intende accedere ad Azure da un altro framework di programmazione, ad esempio Python, Ruby o Node. js, l'autenticazione della password può essere la soluzione ottimale. Prima di decidere se usare una password o un certificato, vedere la sezione [Applicazioni di esempio](#sample-applications) per esempi di autenticazione nei diversi framework.

## Ottenere l'ID tenant

Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Poiché questo valore sarà necessario per l'autenticazione della password o del certificato, si dovrà ottenerlo.

1. Accedere al proprio account.

        azure config mode arm
        azure login

1. Se si recupera l'ID tenant per la sottoscrizione attualmente autenticata, non è necessario specificare l'ID sottoscrizione come parametro. L'opzione **-r** recupera il valore senza le virgolette.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

Passare ora a una sezione seguente per l'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).


## Creare un'entità servizio con password

In questa sezione si eseguiranno i passaggi per creare l'entità servizio con una password e assegnarla a un ruolo.

1. Creare un'entità servizio per l'applicazione. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione. Questo comando crea l'applicazione AD e l'entità servizio.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto. Il nome dell'entità servizio è necessario per eseguire l'accesso.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). Per assegnare un ruolo, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite l'interfaccia della riga di comando di Azure. Tuttavia, se si vogliono usare le credenziali dell'applicazione di codice, non è necessario continuare con questo argomento. È possibile passare ad [Applicazioni di esempio](#sample-applications) per esempi di accesso con l'ID applicazione e la password.

### Fornire le credenziali tramite l'interfaccia della riga di comando di Azure

1. Per il nome utente usare il nome dell'entità servizio restituito durante la creazione dell'entità servizio. Se è necessario recuperare l'ID dell'applicazione, usare il comando seguente. Specificare il nome dell'applicazione Active Directory nel parametro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Accedere come entità servizio:

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    Verrà richiesto di specificare la password. Fornire la password specificata durante la creazione dell'applicazione Active Directory.

        info:    Executing command login
        Password: ********

A questo punto è stata eseguita l'autenticazione come entità servizio per l'entità servizio creata.

## Creare un'entità servizio con certificato

In questa sezione si eseguiranno i passaggi per creare un'entità servizio per un'entità servizio che usa un certificato per l'autenticazione. Per completare la procedura è necessario avere installato [OpenSSL](http://www.openssl.org/).

1. Creare un certificato autofirmato.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinare le chiavi pubblica e privata.

        cat privkey.pem cert.pem > examplecert.pem

3. Aprire il file **examplecert.pem** e copiare i dati del certificato. Cercare la lunga sequenza di caratteri tra **-----BEGIN CERTIFICATE-----** ed **-----END CERTIFICATE-----**.

1. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). Per assegnare un ruolo, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato

Per autenticarsi con l'interfaccia della riga di comando di Azure, fornire l'identificazione personale del certificato, il file del certificato, l'ID dell'applicazione e l'ID tenant.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

Se è necessario recuperare l'identificazione personale del certificato e rimuovere i caratteri non necessari, usare:

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
Viene restituito un valore di identificazione personale simile a:

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

Se è necessario recuperare l'ID applicazione, usare:

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

Se è necessario recuperare l'ID tenant in seguito, vedere [Ottenere l'ID tenant](#get-tenant-id).


## Applicazioni di esempio

Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con .NET)

**Java**

- [Getting Started with Resources - Deploy Using ARM Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Introduzione alle risorse: gestire un gruppo di risorse in Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Ruby)


## Passaggi successivi
  
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
- Per ottenere altre informazioni sull'uso dei certificati e dell'interfaccia della riga di comando di Azure, vedere il blog [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Autenticazione basata su certificati con le entità servizio di Azure dalla riga di comando di Linux)

<!---HONumber=AcomDC_0720_2016-->