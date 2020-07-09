---
title: Connettersi con l'identità gestita - Database di Azure per PostgreSQL - Server singolo
description: Informazioni su come connettersi ed eseguire l'autenticazione usando l'identità gestita con Database di Azure per PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: ec9e53ecaa95f6407a00c149abb6ed7e4a671d74
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102294"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Connettersi con l'identità gestita a Database di Azure per PostgreSQL

Questo articolo illustra come usare un'identità assegnata dall'utente per una macchina virtuale (VM) di Azure per accedere a un server di Database di Azure per PostgreSQL. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso della macchina virtuale a un server di Database di Azure per PostgreSQL
> * Creare un utente nel database che rappresenta l'identità assegnata dall'utente della macchina virtuale
> * Ottenere un token di accesso tramite l'identità della VM e usarlo per eseguire query su un server di Database di Azure per PostgreSQL
> * Implementare il recupero dei token in un'applicazione C# di esempio

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con la funzionalità delle identità gestite per le risorse di Azure, vedere questa [panoramica](../../articles/active-directory/managed-identities-azure-resources/overview.md). Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le attività richieste di creazione delle risorse e gestione dei ruoli, l'account deve avere le autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- È necessaria una macchina virtuale di Azure, ad esempio che esegue Ubuntu Linux, da usare per l'accesso al database tramite l'identità gestita
- È necessario un server di Database di Azure per PostgreSQL con l'[autenticazione di Azure AD](howto-configure-sign-in-aad-authentication.md) configurata
- Per seguire l'esempio in C#, completare prima di tutto la procedura per [connettersi con C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Creazione di un'identità gestita assegnata dall'utente per la macchina virtuale

Creare un'identità nella sottoscrizione usando il comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). È possibile usare lo stesso gruppo di risorse in cui viene eseguita la macchina virtuale o un altro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Per configurare l'identità nei passaggi seguenti, usare il comando [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) per archiviare l'ID risorsa e l'ID client dell'entità in variabili.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

È ora possibile assegnare l'identità assegnata dall'utente alla macchina virtuale con il comando [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Per completare la configurazione, visualizzare il valore dell'ID cliente, che sarà necessario nei passaggi successivi:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Creazione di un utente di PostgreSQL per l'identità gestita

A questo punto, connettersi come utente amministratore di Azure AD al database PostgreSQL ed eseguire le istruzioni SQL seguenti:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

L'identità gestita ha ora accesso quando esegue l'autenticazione con il nome utente `myuser` (sostituire con un nome a scelta).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Recupero del token di accesso dal servizio metadati dell'istanza di Azure

L'applicazione può ora recuperare un token di accesso dal servizio metadati dell'istanza di Azure e usarlo per l'autenticazione con il database.

Il recupero di questo token viene eseguito effettuando una richiesta HTTP a `http://169.254.169.254/metadata/identity/oauth2/token` e passando i parametri seguenti:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (recuperato in precedenza)

Si otterrà un risultato JSON che contiene un campo `access_token`: questo valore di testo lungo è il token di accesso dell'identità gestita, da usare come password per connettersi al database.

A scopo di test, è possibile eseguire i comandi seguenti nella shell. Si noti che è necessario che siano installati `curl`, `jq` e il client `psql`.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

A questo punto si è connessi al database configurato in precedenza.

## <a name="connecting-using-managed-identity-in-c"></a>Connessione tramite identità gestita in C#

Questa sezione illustra come ottenere un token di accesso usando l'identità gestita assegnata dall'utente della VM e usarlo per chiamare Database di Azure per PostgreSQL. Database di Azure per PostgreSQL supporta in modalità nativa l'autenticazione di Azure AD, per cui può accettare direttamente i token di accesso ottenuti tramite identità gestite per le risorse di Azure. Quando si crea una connessione a PostgreSQL, si passa il token di accesso nel campo della password.

Ecco un esempio di codice .NET per l'apertura di una connessione a PostgreSQL tramite token di accesso. Questo codice deve essere eseguito nella macchina virtuale per poter accedere all'endpoint dell'identità gestita assegnata dall'utente della macchina virtuale. Per usare il metodo del token di accesso, è necessario .NET Framework 4.6 o versione successiva oppure .NET Core 2.2 o versione successiva. Sostituire i valori di HOST, USER, DATABASE e CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Quando viene eseguito, questo comando restituirà un output simile al seguente:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per l'[autenticazione di Azure Active Directory con Database di Azure per PostgreSQL](concepts-aad-authentication.md)
