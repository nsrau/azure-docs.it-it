---
title: "Esercitazione: Accesso dell'app Web a Microsoft Graph come app | Azure"
description: Questa esercitazione illustra come accedere ai dati in Microsoft Graph usando le identità gestite.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a7b8ca309bf5710ddbd88413935bef5e97a1ed9f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999672"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Esercitazione: Accedere a Microsoft Graph da un'app protetta come app

Informazioni su come accedere a Microsoft Graph da un'app Web in esecuzione nel Servizio app di Azure.

:::image type="content" alt-text="Diagramma che mostra l'accesso a Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Si vuole chiamare Microsoft Graph per l'app Web. Un modo sicuro per concedere all'app Web l'accesso ai dati consiste nell'usare un'[identità gestita assegnata dal sistema](/azure/active-directory/managed-identities-azure-resources/overview). Un'identità gestita di Azure Active Directory consente al servizio app di accedere alle risorse tramite il controllo degli accessi in base al ruolo, senza richiedere le credenziali dell'app. Dopo aver assegnato un'identità gestita all'app Web, Azure si occupa della creazione e della distribuzione di un certificato. Non occorre preoccuparsi di gestire i segreti o le credenziali dell'app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un'identità gestita assegnata dal sistema in un'app Web.
> * Aggiungere le autorizzazioni dell'API Microsoft Graph a un'identità gestita.
> * Chiamare Microsoft Graph da un'app Web usando identità gestite.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione Web in esecuzione nel Servizio app di Azure con il [modulo di autenticazione/autorizzazione del servizio app abilitato](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Abilitare l'identità gestita nell'app

Se si crea e si pubblica l'app Web tramite Visual Studio, l'identità gestita è già stata abilitata automaticamente nell'app. Nel servizio app selezionare **Identità** nel riquadro sinistro e quindi selezionare **Assegnata dal sistema**. Verificare che lo **Stato** sia impostato su **Sì**. In caso contrario, selezionare **Salva** e quindi **Sì** per abilitare l'identità gestita assegnata dal sistema. Una volta abilitata l'identità gestita, lo stato è impostato su **Sì** e l'ID oggetto è disponibile.

Prendere nota del valore di **ID oggetto**, che sarà necessario nel passaggio successivo.

:::image type="content" alt-text="Screenshot che mostra l'identità assegnata dal sistema." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Concedere l'accesso a Microsoft Graph

Quando si accede a Microsoft Graph, l'identità gestita deve disporre delle autorizzazioni appropriate per l'operazione da eseguire. Attualmente, non è possibile assegnare tali autorizzazioni tramite il portale di Azure. Lo script seguente aggiungerà le autorizzazioni dell'API Microsoft Graph necessarie all'oggetto entità servizio dell'identità gestita.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Dopo aver eseguito lo script, è possibile verificare nel [portale di Azure](https://portal.azure.com) che le autorizzazioni API necessarie siano assegnate all'identità gestita.

Passare ad **Azure Active Directory** e quindi selezionare **Applicazioni aziendali**. Questo riquadro mostra tutte le entità servizio presenti nel tenant. In **Tutte le applicazioni** selezionare l'entità servizio per l'identità gestita. 

Se si segue questa esercitazione, sono disponibili due entità servizio con lo stesso nome visualizzato, ad esempio SecureWebApp2020094113531. L'entità servizio con **URL della home page** rappresenta l'app Web nel tenant. L'entità servizio senza **URL della home page** rappresenta l'identità gestita assegnata dal sistema per l'app Web. Il valore di **ID oggetto** per l'identità gestita corrisponde all'ID oggetto dell'identità gestita creata in precedenza.

Selezionare l'entità servizio per l'identità gestita.

:::image type="content" alt-text="Screenshot che mostra l'opzione Tutte le applicazioni." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

In **Panoramica** selezionare **Autorizzazioni** per visualizzare le autorizzazioni aggiunte per Microsoft Graph.

:::image type="content" alt-text="Screenshot che mostra il riquadro Autorizzazioni." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Chiamare Microsoft Graph (.NET)

La classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) viene usata per ottenere le credenziali del token affinché il codice autorizzi le richieste a Microsoft Graph. Creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), che usa l'identità gestita per recuperare i token e collegarli al client del servizio. L'esempio di codice seguente ottiene le credenziali del token autenticato e le usa per creare un oggetto client del servizio, che recupera gli utenti nel gruppo.

### <a name="install-the-microsoftgraph-client-library-package"></a>Installare il pacchetto della libreria client Microsoft.Graph

Installare il [pacchetto NuGet Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph) nel progetto usando l'interfaccia della riga di comando di .NET Core o la console di Gestione pacchetti in Visual Studio.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Aprire una riga di comando e passare alla directory che contiene il file di progetto.

Eseguire i comandi di installazione.

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[Gestione pacchetti](#tab/package-manager)

Aprire il progetto o la soluzione in Visual Studio, quindi aprire la console selezionando **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

Eseguire i comandi di installazione.
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>Esempio

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'esercitazione è stata completata e l'app Web o le risorse associate non sono più necessarie, [pulire le risorse create](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare un'identità gestita assegnata dal sistema in un'app Web.
> * Aggiungere le autorizzazioni dell'API Microsoft Graph a un'identità gestita.
> * Chiamare Microsoft Graph da un'app Web usando identità gestite.

Informazioni su come connettere un'[app .NET Core](tutorial-dotnetcore-sqldb-app.md), un'[app Python](tutorial-python-postgresql-app.md), un'[app Java](tutorial-java-spring-cosmosdb.md) o un'[app Node.js](tutorial-nodejs-mongodb-app.md) a un database.
