---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere all'API Graph di Azure AD
description: Esercitazione che descrive in modo dettagliato l'uso di un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere all'API Graph di Azure AD.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 57e719c6ef75b08d8c188d2d2d344867bbf590b1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623102"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere all'API Graph di Azure AD

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Questa esercitazione descrive come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere all'API Graph di Azure AD in modo da recuperarne le appartenenze ai gruppi. Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice.  

Per questa esercitazione si eseguiranno query sull'appartenenza dell'identità della macchina virtuale in gruppi di Azure AD. Le informazioni sui gruppi vengono spesso usate nelle decisioni per l'autorizzazione. In background, l'identità gestita della macchina virtuale è rappresentata da un'**entità servizio** in Azure AD. 

> [!div class="checklist"]
> * Connessione ad Azure AD
> * Aggiungere l'identità della macchina virtuale a un gruppo in Azure AD 
> * Concedere all'identità della macchina virtuale l'accesso ad Azure AD Graph 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure AD Graph

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Per concedere a un'identità della macchina virtuale l'accesso ad Azure AD Graph, all'account deve essere assegnato il ruolo di **amministratore globale** in Azure AD.

## <a name="connect-to-azure-ad"></a>Connessione ad Azure AD

È necessario connettersi ad Azure AD per assegnare la macchina virtuale a un gruppo, nonché concedere alla macchina virtuale l'autorizzazione per recuperare le proprie appartenenze ai gruppi.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Aggiungere l'identità della macchina virtuale a un gruppo in Azure AD

Quando è stata abilitata l'identità gestita assegnata dal sistema nella macchina virtuale Linux, è stata creata un'entità servizio in Azure AD.  È necessario aggiungere la macchina virtuale a un gruppo. Vedere l'articolo seguente per istruzioni su come aggiungere la macchina virtuale a un gruppo in Azure AD:

- [Aggiungere membri del gruppo](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Concedere alla macchina virtuale l'accesso all'API Graph di Azure AD

Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD. L'API Graph di Microsoft Azure AD supporta l'autenticazione di Azure AD. In questo passaggio si concederà all'entità servizio dell'identità della macchina virtuale l'accesso ad Azure AD Graph in modo da eseguire query sulle appartenenze ai gruppi. Alle entità servizio viene concesso l'accesso a Microsoft Graph o ad Azure AD Graph tramite **autorizzazioni dell'applicazione**. Il tipo di autorizzazione dell'applicazione che è necessario concedere dipende dall'entità cui si vuole accedere in Microsoft Graph o Azure AD Graph.

Per questa esercitazione, si concederà all'identità della macchina virtuale la possibilità di eseguire query sull'appartenenza ai gruppi usando l'autorizzazione dell'applicazione `Directory.Read.All`. Per concedere questa autorizzazione, è necessario un account utente cui sia assegnato il ruolo di amministratore globale di Azure AD. In genere si concede un'autorizzazione dell'applicazione visitando la registrazione dell'applicazione nel portale di Azure e aggiungendo l'autorizzazione da qui. Tuttavia, le identità gestite per le risorse di Azure non registrano oggetti dell'applicazione in Azure AD, ma solo entità servizio. Per registrare l'autorizzazione dell'applicazione, è necessario usare lo strumento della riga di comando Azure AD PowerShell. 

Azure AD Graph:
- ID app dell'entità servizio (usato quando si concede l'autorizzazione dell'app): 00000002-0000-0000-c000-000000000000
- ID risorsa (usato quando si richiede il token di accesso da identità gestite per risorse di Azure): https://graph.windows.net
- Riferimento all'ambito di autorizzazione: [riferimento alle autorizzazioni di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Concedere autorizzazioni dell'applicazione tramite CURL

1. Recuperare un token per effettuare richieste CURL:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Sarà necessario recuperare e annotare il valore di `objectId` della macchina virtuale. Questo valore verrà usato nei passaggi successivi per concedere le autorizzazioni alla macchina virtuale per la lettura delle appartenenze ai gruppi. Sostituire il valore di `<ACCESS TOKEN>` con il token di accesso recuperato nel passaggio precedente.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Usando l'ID app di Azure AD Graph 00000002-0000-0000-c000-000000000000, recuperare e annotare il valore di `objectId` per `odata.type: Microsoft.DirectoryServices.ServicePrincipal` e di `id` per l'autorizzazione del ruolo dell'app `Directory.Read.All`.  Sostituire `<ACCESS TOKEN>` con il token di accesso recuperato in precedenza.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Risposta:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. A questo punto, concedere all'entità servizio della macchina virtuale l'accesso in lettura a oggetti directory di Azure AD usando l'API Graph di Azure AD.  Il valore di `id` è il valore per l'autorizzazione del ruolo dell'app `Directory.Read.All`, mentre il valore di `resourceId` è l'oggetto `objectId` per l'entità servizio `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (valori annotati nel passaggio precedente).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Ottenere un token di accesso usando l'identità della macchina virtuale per chiamare Azure AD Graph 

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**.  
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per effettuare una richiesta alle identità gestite locali per l'endpoint delle risorse di Azure per ottenere un token di accesso per Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   La risposta include il token di accesso necessario per accedere ad Azure AD Graph.

   Risposta:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Usando l'ID oggetto dell'entità servizio della macchina virtuale (valore recuperato nei passaggi precedenti), è possibile eseguire query sull'API Graph di Azure AD per recuperare le appartenenze ai gruppi. Sostituire `<OBJECT-ID>` con l'ID oggetto dell'entità servizio della macchina virtuale e `<ACCESS-TOKEN>` con il token di accesso ottenuto in precedenza:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Risposta:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure AD Graph.  Per altre informazioni su Azure AD Graph, vedere:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)