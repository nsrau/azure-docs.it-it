---
title: Esercitazione sulla rotazione per utente singolo/password singola
description: Questa esercitazione illustra come automatizzare la rotazione di un segreto per le risorse che usano l'autenticazione utente singolo/password singola.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9c0dca29d173eb2c7893a20b2ab41dd31522e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183212"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatizzare la rotazione di un segreto per le risorse con autenticazione utente singolo/password singola

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](../general/managed-identity.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o segreti. Le chiavi di accesso o i segreti necessitano di una rotazione periodica.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi con autenticazione utente singolo/password singola. In particolare, questo scenario consente di ruotare le password di SQL Server archiviate in Azure Key Vault usando una funzione attivata da una notifica di Griglia di eventi di Azure:

![Diagramma della soluzione di rotazione](../media/rotate1.png)

1. 30 giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento "In scadenza" in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint app per le funzioni che ha sottoscritto questo evento.
1. L'app per le funzioni riceve le informazioni relative al segreto, genera una nuova password casuale e crea una nuova versione del segreto con una nuova password in Key Vault.
1. L'app per le funzioni aggiorna SQL Server con la nuova password.

> [!NOTE]
> Tra i passaggi 3 e 4 potrebbe verificarsi un ritardo. Durante questo periodo, il segreto in Key Vault non potrà eseguire l'autenticazione a SQL Server. In caso di errore in uno dei passaggi, Griglia di eventi eseguirà nuovi tentativi per 2 ore.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Creare un insieme di credenziali delle chiavi e un'istanza di SQL Server

Il primo passaggio prevede la creazione di un insieme di credenziali delle chiavi, di un'istanza di SQL Server e di un database e l'archiviazione della password amministratore di SQL Server in Key Vault.

Questa esercitazione usa un modello di Azure Resource Manager esistente per creare i componenti. Il codice è disponibile qui: [Esempio di modello di rotazione del segreto di base](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Selezionare il collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. In **Gruppo di risorse** selezionare **Crea nuovo**. Assegnare al gruppo il nome **simplerotation**.
1. Selezionare **Acquisto**.

    ![Creare un gruppo di risorse](../media/rotate2.png)

A questo punto saranno disponibili un insieme di credenziali delle chiavi, un'istanza di SQL Server e un database SQL. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az resource list -o table
```

Il risultato sarà simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Successivamente, verrà creata un'app per le funzioni con un'identità gestita dal sistema, nonché gli altri componenti necessari.

L'app per le funzioni richiede questi componenti:
- Un piano di servizio app di Azure
- Un account di archiviazione
- Un criterio di accesso per accedere ai segreti in Key Vault tramite l'identità gestita dell'app per le funzioni

1. Selezionare il collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Nell'elenco **Gruppo di risorse** selezionare **simplerotation**.
1. Selezionare **Acquisto**.

   ![Selezionare Acquista](../media/rotate3.png)

Dopo aver completato la procedura precedente, saranno disponibili un account di archiviazione, una server farm e un'app per le funzioni. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az resource list -o table
```

Il risultato sarà simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Per informazioni su come creare un'app per le funzioni e usare l'identità gestita per accedere a Key Vault, vedere [Creare un'app per le funzioni dal portale di Azure](../../azure-functions/functions-create-function-app-portal.md) e [Fornire l'autenticazione a Key Vault con un'identità gestita](../general/managed-identity.md).

### <a name="rotation-function"></a>Funzione di rotazione
La funzione usa un evento per attivare la rotazione di un segreto aggiornando Key Vault e il database SQL.

#### <a name="function-trigger-event"></a>Evento di attivazione della funzione

Questa funzione legge i dati dell'evento ed esegue la logica di rotazione:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logica di rotazione del segreto
Questo metodo di rotazione legge le informazioni del database dal segreto, crea una nuova versione del segreto e aggiorna il database con il nuovo segreto:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Il codice completo è disponibile in [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Distribuzione della funzione

1. Scaricare il file ZIP dell'app per le funzioni da [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Caricare il file simplerotationsample-fn.zip in Azure Cloud Shell.

   ![Caricare il file](../media/rotate4.png)
1. Usare questo comando dell'interfaccia della riga di comando di Azure per distribuire il file ZIP nell'app per le funzioni:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Una volta distribuita la funzione, verranno visualizzate due funzioni in simplerotation-fn:

![Funzioni SimpleRotation e SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Aggiungere una sottoscrizione di eventi per l'evento SecretNearExpiry

Copiare la chiave `eventgrid_extension` dell'app per le funzioni:

   ![Selezionare Impostazioni dell'app per le funzioni](../media/rotate6.png)

   ![chiave eventgrid_extension](../media/rotate7.png)

Usare la chiave `eventgrid_extension` copiata e l'ID sottoscrizione nel comando seguente per creare una sottoscrizione di Griglia di eventi per gli eventi `SecretNearExpiry`:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Aggiungere il segreto a Key Vault
Impostare il criterio di accesso per concedere agli utenti l'autorizzazione *Gestisci segreti*:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Creare un nuovo segreto con i tag contenenti l'origine dati del database SQL e l'ID utente. Includere una data di scadenza impostata per domani.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

La creazione di un segreto con una data di scadenza breve pubblicherà immediatamente un evento `SecretNearExpiry`, che attiverà a sua volta la funzione di rotazione del segreto.

## <a name="test-and-verify"></a>Test e verifica
Dopo alcuni minuti, il segreto `sqluser` verrà ruotato automaticamente.

Per verificare l'avvenuta rotazione del segreto, passare a **Key Vault** > **Segreti**:

![Passare a segreti](../media/rotate8.png)

Aprire il segreto **sqluser** per visualizzare la versione originale e quella ruotata:

![Aprire il segreto sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Creare un'app Web

Per verificare le credenziali SQL, creare un'app Web. Questa app Web otterrà il segreto da Key Vault, estrarrà le informazioni e le credenziali del database SQL dal segreto e testerà la connessione a SQL Server.

L'app Web richiede questi componenti:
- Un'app Web con identità gestita dal sistema
- Un criterio di accesso per accedere ai segreti in Key Vault tramite l'identità gestita dell'app Web

1. Selezionare il collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selezionare il gruppo di risorse **simplerotation**.
1. Selezionare **Acquisto**.

### <a name="deploy-the-web-app"></a>Distribuire l'app Web

Il codice sorgente per l'app Web è disponibile in [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Per distribuire l'app Web, seguire questa procedura:

1. Scaricare il file ZIP dell'app per le funzioni da [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Caricare il file simplerotationsample-app.zip in Azure Cloud Shell.
1. Usare questo comando dell'interfaccia della riga di comando di Azure per distribuire il file ZIP nell'app per le funzioni:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Aprire l'app Web

Passare all'applicazione distribuita e selezionare l'URL:
 
![Selezionare l'URL](../media/rotate10.png)

Quando l'applicazione viene aperta nel browser, verrà visualizzato il **valore del segreto generato** con un valore di **database connesso** impostato su *true*.

## <a name="learn-more"></a>Altre informazioni

- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](../general/event-grid-overview.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](../general/event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../../event-grid/event-schema-key-vault.md)
