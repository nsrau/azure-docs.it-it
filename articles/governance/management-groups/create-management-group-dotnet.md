---
title: 'Avvio rapido: Creare un gruppo di gestione con .NET Core'
description: In questo argomento di avvio rapido si usa .NET Core per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 138998fdc23fd8a296ca50093e2952017888041f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604564"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Avvio rapido: Creare un gruppo di gestione con .NET Core

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Un'entità servizio di Azure, tra cui _clientId_ e _clientSecret_. Se non si hanno entità servizio da usare con Criteri di Azure o se ne vuole creare una nuova, vedere [Librerie di gestione di Azure per l'autenticazione .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignorare il passaggio per installare i pacchetti di .NET Core perché questa operazione verrà descritta nei passaggi successivi.

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Configurazione dell'applicazione

Per consentire a .NET Core di gestire i gruppi di gestione, creare una nuova applicazione console e installare i pacchetti richiesti.

1. Controllare che sia installata l'ultima versione di .NET Core (almeno la **3.1.8**). Se non è ancora installata, scaricarla dal sito [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inizializzare una nuova applicazione console .NET Core denominata "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Cambiare le directory nella nuova cartella di progetto e installare i pacchetti richiesti per Criteri di Azure:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Sostituire il file `program.cs` predefinito con il codice seguente e salvare il file aggiornato:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Compilare e pubblicare l'applicazione console `mgCreate`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Creare il gruppo di gestione

In questo argomento di avvio rapido viene creato un nuovo gruppo di gestione nel gruppo di gestione radice.

1. Passare alla directory `{run-folder}` definita con il comando `dotnet publish` precedente.

1. Immettere il comando seguente nel terminale:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

I comandi precedenti usano le informazioni seguenti:

- `{tenantId}`: sostituire con il proprio ID tenant
- `{clientId}`: sostituire con l'ID client dell'entità servizio
- `{clientSecret}`: sostituire con il segreto client dell'entità servizio
- `{groupID}`: sostituire con l'ID del nuovo gruppo di gestione
- `{displayName}`: sostituire con il nome descrittivo del nuovo gruppo di gestione

Il risultato è un nuovo gruppo di gestione nel gruppo di gestione radice.

## <a name="clean-up-resources"></a>Pulire le risorse

- Eliminare il nuovo gruppo di gestione tramite il portale.

- Per rimuovere l'applicazione console .NET Core e i pacchetti installati, eliminare la cartella di progetto `mgCreate`.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> [Gestire le risorse con i gruppi di gestione](./manage.md)