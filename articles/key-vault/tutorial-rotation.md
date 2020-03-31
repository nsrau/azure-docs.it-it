---
title: Esercitazione sulla rotazione per singolo utente/password
description: Usare questa esercitazione per automatizzare la rotazione di un singolo utente/password
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223358"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatizzare la rotazione di un segreto per le risorse con autenticazione singolo utente/password

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](managed-identity.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o segreti. Le chiavi di accesso o i segreti necessitano di una rotazione periodica.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi con autenticazione singolo utente/password. In particolare, questo scenario consente di ruotare le password di SQL Server archiviate nell'insieme di credenziali delle chiavi usando una funzione attivata da una notifica di Griglia di eventi:

![Diagramma di rotazione](./media/rotate1.png)

1. 30 giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento "In scadenza" in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint App per le funzioni che ha sottoscritto questo evento.
1. L'app per le funzioni riceve le informazioni relative al segreto, genera una nuova password casuale e crea una nuova versione del segreto con una nuova password in Key Vault.
1. L'app per le funzioni aggiorna SQL con la nuova password.

> [!NOTE]
> Potrebbe verificarsi un ritardo tra i passaggi 3 e 4 e durante questo periodo di tempo il segreto in Key Vault non sarà valido per l'autenticazione a SQL. In caso di errore in uno dei passaggi, Griglia di eventi eseguirà nuovi tentativi per 2 ore.

## <a name="setup"></a>Configurazione

## <a name="create-a-key-vault-and-sql-server"></a>Creare un insieme di credenziali delle chiavi e un server SQL

Prima di iniziare, è necessario creare un'istanza di Key Vault, creare un server e un database SQL e archiviare la password amministratore di SQL Server in Key Vault.

Questa esercitazione usa un modello di Azure Resource Manager predefinito per creare i componenti. L'intero codice è disponibile qui: [Esempio di modello di rotazione del segreto di base](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Fare clic sul collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Per "Gruppo di risorse" selezionare "Crea nuovo" e assegnargli il nome "simplerotation".
1. Selezionare "Acquista".

    ![Creare un nuovo gruppo di risorse](./media/rotate2.png)

Al termine della procedura, saranno disponibili un insieme di credenziali delle chiavi, un server SQL e un database SQL. È possibile verificarlo in un terminale dell'interfaccia della riga di comando di Azure eseguendo questo comando:

```azurecli
az resource list -o table
```

Il risultato sarà simile al seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Creare un'app per le funzioni

Creare un'app per le funzioni con un'identità gestita dal sistema, nonché i componenti aggiuntivi necessari: 

L'app per le funzioni richiede i componenti e la configurazione seguenti:
- Piano di servizio app
- Account di archiviazione
- Criteri di accesso per accedere ai segreti in Key Vault tramite l'identità gestita dell'app per le funzioni

1. Fare clic sul collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Per "Gruppo di risorse" selezionare "simplerotation".
1. Selezionare "Acquista".

   ![Schermata di acquisto](./media/rotate3.png)

Dopo aver completato la procedura precedente, saranno disponibili un account di archiviazione, una server farm e un'app per le funzioni.  È possibile verificarlo in un terminale dell'interfaccia della riga di comando di Azure eseguendo questo comando:

```azurecli
az resource list -o table
```

Il risultato sarà simile al seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Per informazioni su come creare un'app per le funzioni e usare l'identità gestita per accedere a Key Vault, vedere [Creare un'app per le funzioni dal portale di Azure](../azure-functions/functions-create-function-app-portal.md) e [Fornire l'autenticazione a Key Vault con un'identità gestita](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Funzione di rotazione e distribuzione
La funzione usa un evento come trigger ed esegue la rotazione di un segreto aggiornando Key Vault e il database SQL.

#### <a name="function-event-trigger-handler"></a>Gestore del trigger di evento della funzione

La funzione seguente legge i dati dell'evento ed esegue la logica di rotazione

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
Questo metodo di rotazione legge le informazioni del database dal segreto, crea una nuova versione del segreto e aggiorna il database con un nuovo segreto.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
L'intero codice è disponibile qui: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Distribuzione della funzione

1. Scaricare il file ZIP dell'app per le funzioni: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Caricare il file simplerotationsample-fn.zip in Azure Cloud Shell.
 
1. Usare il comando dell'interfaccia della riga di comando seguente per distribuire il file ZIP nell'app per le funzioni:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Schermata di acquisto](./media/rotate4.png)

Al termine della distribuzione, saranno disponibili due funzioni in simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Aggiungere la sottoscrizione di eventi per l'evento "SecretNearExpiry"

Copiare la chiave eventgrid_extension dell'app per le funzioni.

![Azure Cloud Shell](./media/rotate6.png)

![Test e verifica](./media/rotate7.png)

Usare la chiave dell'estensione Griglia di eventi copiata e l'ID sottoscrizione nel comando seguente per creare una sottoscrizione di Griglia di eventi per gli eventi SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Aggiungere il segreto a Key Vault
Impostare i criteri di accesso per concedere agli utenti l'autorizzazione "Gestisci segreti".

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

A questo punto, creare un nuovo segreto con tag contenenti l'origine dati del database SQL e l'ID utente, con la data di scadenza impostata su domani.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

La creazione di un segreto con una data di scadenza breve pubblicherà immediatamente un evento SecretNearExpiry, che attiverà a sua volta la funzione di rotazione del segreto.

### <a name="test-and-verify"></a>Test e verifica
Dopo alcuni minuti, il segreto sqluser verrà uotato automaticamente.

Per verificare la rotazione del segreto, passare a Key Vault > Segreti

![Test e verifica](./media/rotate8.png)

Aprire il segreto "sqluser" per visualizzare la versione originale e quella ruotata

![Test e verifica](./media/rotate9.png)

## <a name="create-web-app"></a>Crea app Web

Per verificare le credenziali SQL, creare un'applicazione Web. Questa applicazione Web otterrà il segreto dall'insieme di credenziali delle chiavi, estrarrà le informazioni e le credenziali del database SQL dal segreto e testerà la connessione a SQL.

L'app Web richiede i componenti e la configurazione seguenti:
- App Web con identità gestita dal sistema
- Criteri di accesso per accedere ai segreti in Key Vault tramite l'identità gestita dell'app Web

1. Fare clic sul collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selezionare il gruppo di risorse **simplerotation**
1. Fare clic su Acquista

### <a name="deploy-web-app"></a>Distribuire l'app Web

Il codice sorgente per l'app Web è disponibile in https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp. Per la distribuzione dell'app Web, seguire questa procedura:

1. Scaricare il file ZIP dell'app per le funzioni da https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Caricare il file `simplerotationsample-app.zip` in Azure Cloud Shell.
1. Usare questo comando dell'interfaccia della riga di comando di Azure per distribuire il file ZIP nell'app per le funzioni:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Aprire l'applicazione Web

Passare all'applicazione distribuita e fare clic su "URL":
 
![Test e verifica](./media/rotate10.png)

Il valore del segreto generato verrà visualizzato con la voce Database Connected impostata su true.

## <a name="learn-more"></a>Altre informazioni:

- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](event-grid-overview.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../event-grid/event-schema-key-vault.md)
