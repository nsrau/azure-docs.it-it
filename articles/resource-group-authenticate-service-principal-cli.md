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
   ms.date="09/07/2016"
   ms.author="tomfitz"/>  

# Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)


Quando si ha un'applicazione o uno script che deve accedere alle risorse, è molto probabile che non si voglia eseguire il processo con le proprie credenziali. È possibile che si vogliano usare autorizzazioni diverse per l'applicazione e che si preferisca che l'applicazione non continui a usare le credenziali in caso di cambiamento delle responsabilità dell'utente. Si crea quindi un'identità per l'applicazione che include credenziali di autenticazione e assegnazioni di ruolo. Ogni volta che viene eseguita, l'app eseguirà l'autenticazione con tali credenziali. Questo argomento illustra come usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](xplat-cli-install.md) per configurare un'applicazione per l'esecuzione con credenziali e identità proprie.

Con l'interfaccia della riga di comando di Azure, per l'autenticazione di un'applicazione AD sono disponibili due opzioni:

 - password
 - certificato

Questo argomento illustra come usare entrambe le opzioni nell'interfaccia della riga di comando di Azure. Se si intende accedere ad Azure da un framework di programmazione, ad esempio Python, Ruby o Node.js, l'autenticazione della password può essere la soluzione ottimale. Prima di decidere se usare una password o un certificato, vedere la sezione [Applicazioni di esempio](#sample-applications) per esempi di autenticazione nei diversi framework.

## Concetti relativi ad Active Directory

In questo articolo vengono creati due oggetti: l'applicazione Active Directory (AD) e l'entità servizio. L'applicazione AD è la rappresentazione globale dell'applicazione e contiene le credenziali, ovvero un ID applicazione e una password o un certificato. L'entità servizio è la rappresentazione locale dell'applicazione in un'istanza di Active Directory e contiene l'assegnazione di ruolo. Questo argomento è incentrato su un'applicazione con un tenant singolo in cui è previsto che l'applicazione venga eseguita all'interno del sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione. In un'applicazione con un tenant singolo vengono usate un'app AD e un'entità servizio.

Ci si potrebbe chiedere perché siano necessari entrambi gli oggetti. Questo approccio risulta più opportuno quando si considerano le applicazioni multi-tenant. Le applicazioni multi-tenant vengono in genere usate per applicazioni SaaS (Software-as-a-Service), che prevedono l'esecuzione dell'applicazione in molte sottoscrizioni diverse. Per le applicazioni multi-tenant vengono usate un'app AD e più entità servizio (una in ogni istanza di Active Directory che concede l'accesso all'app). Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

## Autorizzazioni necessarie

Per completare questo argomento è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Active Directory e assegnare l'entità servizio a un ruolo.

In Active Directory, l'account deve avere un ruolo di amministratore (ad esempio **Amministratore globale** o **Amministratore utenti**). Se l'account è assegnato al ruolo **Utente**, è necessario che le autorizzazioni vengano elevate da un amministratore.

Nella sottoscrizione, l'account deve avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se l'account è assegnato al ruolo **Collaboratore**, quando si prova ad assegnare l'entità servizio a un ruolo viene visualizzato un errore. Anche in questo caso, l'amministratore della sottoscrizione deve concedere diritti di accesso sufficienti.

Passare ora alla sezione relativa all'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).

## Creare un'entità servizio con password

In questa sezione vengono eseguiti i passaggi per:

- Creare l'applicazione AD con una password e l'entità servizio
- Assegnare il ruolo Lettore all'entità servizio

Per eseguire rapidamente i passaggi, usare i comandi seguenti.

    azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
    azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

Verranno ora esaminati più attentamente questi passaggi per comprendere meglio il processo.

1. Accedere al proprio account.

        azure config mode arm
        azure login

1. Creare un'entità servizio per l'applicazione. Specificare il nome visualizzato, l'URI di una pagina che descrive l'applicazione, gli URI che identificano l'applicazione e la password per l'identità dell'applicazione. Questo comando crea l'applicazione AD e l'entità servizio.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {your-password}
        
     Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
     
     Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
    
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

2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo **Lettore**, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName**, specificare il valore **ObjectId** usato quando è stata creata l'applicazione.

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account **non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito '/subscriptions/{guid}'**.

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite l'interfaccia della riga di comando di Azure. Se si vogliono usare le credenziali nell'applicazione di codice, non è necessario continuare con questo argomento. È possibile passare ad [Applicazioni di esempio](#sample-applications) per esempi di accesso con l'ID applicazione e la password.

### Fornire le credenziali tramite l'interfaccia della riga di comando di Azure

A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Per recuperare l'ID tenant per la sottoscrizione attualmente autenticata, usare il comando seguente:

        azure account show

     Che restituisce:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se è necessario ottenere l'ID tenant di un'altra sottoscrizione, usare il comando seguente:

        azure account show -s {subscription-id}

3. Accedere come entità servizio.

        azure login -u https://www.contoso.org/example --service-principal --tenant {tenant-id}

    Verrà richiesto di specificare la password. Fornire la password specificata durante la creazione dell'applicazione Active Directory.

        info:    Executing command login
        Password: ********

A questo punto è stata eseguita l'autenticazione come entità servizio per l'entità servizio creata.

## Creare un'entità servizio con certificato

In questa sezione vengono eseguiti i passaggi per:

- Creare un certificato autofirmato
- Creare l'applicazione AD con il certificato e l'entità servizio
- Assegnare il ruolo Lettore all'entità servizio

Per completare i passaggi è necessario aver installato [OpenSSL](http://www.openssl.org/).

1. Creare un certificato autofirmato.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinare le chiavi pubblica e privata.

        cat privkey.pem cert.pem > examplecert.pem

3. Aprire il file **examplecert.pem** e cercare la lunga sequenza di caratteri tra **-----BEGIN CERTIFICATE-----** ed **-----END CERTIFICATE-----**. Copiare i dati del certificato. Questi dati verranno passati come parametri durante la creazione dell'entità servizio.

1. Accedere al proprio account.

        azure config mode arm
        azure login

1. Creare un'entità servizio per l'applicazione. Specificare il nome visualizzato, l'URI di una pagina che descrive l'applicazione, gli URI che identificano l'applicazione e i dati del certificato copiati. Questo comando crea l'applicazione AD e l'entità servizio.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
     Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
     
     Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
    
     Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo **Lettore**, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName**, specificare il valore **ObjectId** usato quando è stata creata l'applicazione.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account **non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito '/subscriptions/{guid}'**.

### Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato

A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Per recuperare l'ID tenant per la sottoscrizione attualmente autenticata, usare il comando seguente:

        azure account show

     Che restituisce:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se è necessario ottenere l'ID tenant di un'altra sottoscrizione, usare il comando seguente:

        azure account show -s {subscription-id}

1. Per recuperare l'identificazione personale del certificato e rimuovere i caratteri non necessari, usare:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Viene restituito un valore di identificazione personale simile a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

1. Accedere come entità servizio.

        azure login --service-principal --tenant {tenant-id} -u https://www.contoso.org/example --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

## Applicazioni di esempio

Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Manage Azure resources and resource groups with .NET (Gestire risorse e gruppi di risorse di Azure con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Getting Started with Resources - Deploy Using ARM Template - in Java (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Getting Started with Resources - Manage Resource Group - in Java (Introduzione alle risorse: gestire un gruppo di risorse in Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Managing Azure Resource and Resource Groups with Python (Gestione di risorse e gruppi di risorse di Azure con Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Manage Azure resources and resource groups with Node.js (Gestire risorse e gruppi di risorse di Azure con Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Managing Azure Resource and Resource Groups with Ruby (Gestione di risorse e gruppi di risorse di Azure con Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Passaggi successivi
  
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
- Per altre informazioni sull'uso dei certificati e dell'interfaccia della riga di comando di Azure, vedere [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Autenticazione basata su certificati con le entità servizio di Azure dalla riga di comando di Linux).

<!---HONumber=AcomDC_0914_2016-->