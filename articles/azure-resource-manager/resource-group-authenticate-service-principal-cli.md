---
title: "Creare un&quot;identità per un&quot;app Azure con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs"
description: "Descrive come utilizzare l&quot;interfaccia della riga di comando di Azure per creare un&quot;applicazione Azure Active Directory e un&quot;entità servizio e concedere l&quot;accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l&quot;applicazione con una password o un certificato."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/31/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c8a883dedee31d9efab6e2eb4c0ac67b467afe34
ms.lasthandoff: 04/21/2017


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portale](resource-group-create-service-principal-portal.md)
> 
> 

Quando si ha un'app o uno script che deve accedere alle risorse, è possibile configurare un'identità per l'app ed eseguirne l'autenticazione con credenziali specifiche. Questa identità è nota come entità servizio. Questo approccio consente di:

* Assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Usare un certificato per l'autenticazione in caso di esecuzione di uno script automatico.

Questo argomento illustra come usare l'[interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md) per configurare un'applicazione per l'esecuzione con credenziali e identità proprie. Installare la versione più recente dell'[interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md) per assicurarsi che l'ambiente corrisponda agli esempi presentati nell'articolo.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per completare questo argomento è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Azure Active Directory e assegnare l'entità servizio a un ruolo. 

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere l'articolo su come [controllare le autorizzazioni necessarie nel portale](resource-group-create-service-principal-portal.md#required-permissions).

Passare ora alla sezione relativa all'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Creare un'entità servizio con password
In questa sezione si eseguono i passaggi per creare l'applicazione AD protetta da password e assegnare il ruolo di lettura all'entità servizio.

1. Accedere al proprio account.
   
   ```azurecli
   azure login
   ```
2. Per creare un'identità dell'app, specificare il nome dell'app e una password, come illustrato nel comando seguente:
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto. Il GUID indicato in Nomi entità servizio è necessario al momento di eseguire l'accesso. Si tratta dello stesso valore di ID app. Nelle applicazioni di esempio, questo valore viene definito `Client ID`. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo Lettore, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per il parametro Objectid, specificare il valore di ObjectId usato durante la creazione dell'applicazione. Prima di eseguire questo comando, è necessario lasciare che la nuova entità servizio si propaghi in Azure Active Directory. Quando si eseguono questi comandi manualmente, in genere trascorre tempo sufficiente tra le attività. In uno script, è necessario aggiungere un passaggio di sospensione tra i comandi (ad esempio `sleep 15`). Se viene visualizzato un errore indicante che l'entità non esiste nella directory, eseguire nuovamente il comando.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite l'interfaccia della riga di comando di Azure. Se si vogliono usare le credenziali nell'applicazione di codice, non è necessario continuare con questo argomento. È possibile passare ad [Applicazioni di esempio](#sample-applications) per esempi di accesso con l'ID applicazione e la password. 

### <a name="provide-credentials-through-azure-cli"></a>Fornire le credenziali tramite l'interfaccia della riga di comando di Azure
A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory. Per recuperare l'ID tenant per la sottoscrizione attualmente autenticata, usare il comando seguente:
   
   ```azurecli
   azure account show
   ```
   
   Che restituisce:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     Se è necessario ottenere l'ID tenant di un'altra sottoscrizione, usare il comando seguente:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Se è necessario recuperare l'ID client da usare per l'accesso, usare:
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     Il valore da usare per eseguire l'accesso è il GUID nei nomi dell'entità servizio.
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. Accedere come entità servizio.
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    Verrà richiesto di specificare la password. Fornire la password specificata durante la creazione dell'applicazione Active Directory.
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

A questo punto è stata eseguita l'autenticazione come entità servizio per l'entità servizio creata.

In alternativa, è possibile richiamare operazioni REST dalla riga di comando per eseguire l'accesso. Dalla risposta di autenticazione è possibile recuperare il token di accesso da usare con altre operazioni. Per un esempio di come recuperare il token di accesso richiamando operazioni REST, vedere [Generazione di un token di accesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Creare un'entità servizio con certificato
In questa sezione vengono eseguiti i passaggi per:

* Creare un certificato autofirmato
* Creare l'applicazione AD con il certificato e l'entità servizio
* Assegnare il ruolo Lettore all'entità servizio

Per completare i passaggi è necessario aver installato [OpenSSL](http://www.openssl.org/) .

1. Creare un certificato autofirmato.
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. Nel passaggio precedente sono stati creati due file: privkey.pem e cert.pem. Combinare le chiavi pubblica e privata in un singolo file.

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. Aprire il file **examplecert.pem** e cercare la lunga sequenza di caratteri tra **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**. Copiare i dati del certificato. Questi dati verranno passati come parametri durante la creazione dell'entità servizio.

4. Accedere al proprio account.

   ```azurecli
   azure login
   ```
5. Per creare l'entità servizio, fornire il nome dell'app e i dati del certificato, come illustrato nel comando seguente:
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto. Il GUID indicato in Nomi entità servizio è necessario al momento di eseguire l'accesso. Si tratta dello stesso valore di ID app. Nelle applicazioni di esempio, questo valore viene definito come ID client. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo Lettore, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per il parametro Objectid, specificare il valore di ObjectId usato durante la creazione dell'applicazione. Prima di eseguire questo comando, è necessario lasciare che la nuova entità servizio si propaghi in Azure Active Directory. Quando si eseguono questi comandi manualmente, in genere trascorre tempo sufficiente tra le attività. In uno script, è necessario aggiungere un passaggio di sospensione tra i comandi (ad esempio `sleep 15`). Se viene visualizzato un errore indicante che l'entità non esiste nella directory, eseguire nuovamente il comando.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato
A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory. Per recuperare l'ID tenant per la sottoscrizione attualmente autenticata, usare il comando seguente:
   
   ```azurecli
   azure account show
   ```
   
   Che restituisce:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   Se è necessario ottenere l'ID tenant di un'altra sottoscrizione, usare il comando seguente:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Per recuperare l'identificazione personale del certificato e rimuovere i caratteri non necessari, usare:
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   Viene restituito un valore di identificazione personale simile a:
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. Se è necessario recuperare l'ID client da usare per l'accesso, usare:
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   Il valore da usare per eseguire l'accesso è il GUID nei nomi dell'entità servizio.
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. Accedere come entità servizio.
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

A questo punto, è stata eseguita l'autenticazione come entità servizio per l'applicazione di Azure Active Directory creata.

## <a name="change-credentials"></a>Modificare le credenziali

Per modificare le credenziali per un'app di Active Directory, a causa di una violazione della protezione o di credenziali scadute, usare `azure ad app set`.

Per modificare una password, usare:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

Per modificare un valore del certificato, usare:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>Debug

Durante la creazione di un'entità servizio, è possibile riscontrare gli errori seguenti:

* **"Authentication_Unauthorized"** o **"Nessuna sottoscrizione trovata nel contesto".** - Questo errore viene visualizzato quando l'account non ha le [autorizzazioni necessarie](#required-permissions) in Azure Active Directory per registrare un'app. In genere, l'errore si verifica quando solo gli utenti amministratori di Azure Active Directory possono registrare le app e l'account in uso non è un account di amministratore. Chiedere all'amministratore di essere assegnati a un ruolo di amministratore oppure di consentire agli utenti di registrare le app.

* L'account **"non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' nell'ambito '/subscriptions/{guid}'."** - Questo errore viene visualizzato quando l'account non dispone di autorizzazioni sufficienti per assegnare un ruolo a un'identità. Chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente.

## <a name="sample-applications"></a>Applicazioni di esempio
Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

* [Deploy an SSH Enabled VM with a Template with .NET (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Manage Azure resources and resource groups with .NET (Gestire risorse e gruppi di risorse di Azure con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Getting Started with Resources - Deploy Using ARM Template - in Java (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Getting Started with Resources - Manage Resource Group - in Java (Introduzione alle risorse: gestire un gruppo di risorse in Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Deploy an SSH Enabled VM with a Template in Python (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Managing Azure Resource and Resource Groups with Python (Gestione di risorse e gruppi di risorse di Azure con Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Deploy an SSH Enabled VM with a Template in Node.js (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Manage Azure resources and resource groups with Node.js (Gestire risorse e gruppi di risorse di Azure con Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Managing Azure Resource and Resource Groups with Ruby (Gestione di risorse e gruppi di risorse di Azure con Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per altre informazioni sull'uso dei certificati e dell'interfaccia della riga di comando di Azure, vedere [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)(Autenticazione basata su certificati con le entità servizio di Azure dalla riga di comando di Linux). 


