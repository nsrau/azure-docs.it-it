---
title: Esercitazione sulla rotazione per le risorse con un set di credenziali di autenticazione archiviate in Azure Key Vault
description: Questa esercitazione illustra come automatizzare la rotazione di un segreto per le risorse che usano un unico set di credenziali di autenticazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 67fe36cf86c886f9d67d98cc8d34a090db4a71cb
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373011"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatizzare la rotazione di un segreto per le risorse che usano un unico set di credenziali di autenticazione

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](../general/managed-identity.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o segreti. Le chiavi di accesso o i segreti necessitano di una rotazione periodica.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi che usano un unico set di credenziali di autenticazione. In particolare, questo scenario consente di ruotare le password di SQL Server archiviate in Azure Key Vault usando una funzione attivata da una notifica di Griglia di eventi di Azure:

![Diagramma della soluzione di rotazione](../media/rotate1.png)

1. 30 giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento "In scadenza" in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint app per le funzioni che ha sottoscritto questo evento.
1. L'app per le funzioni riceve le informazioni relative al segreto, genera una nuova password casuale e crea una nuova versione del segreto con una nuova password in Key Vault.
1. L'app per le funzioni aggiorna SQL Server con la nuova password.

> [!NOTE]
> Tra i passaggi 3 e 4 potrebbe verificarsi un ritardo. Durante questo periodo, il segreto in Key Vault non potrà eseguire l'autenticazione a SQL Server. In caso di errore in uno dei passaggi, Griglia di eventi eseguirà nuovi tentativi per 2 ore.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Insieme di credenziali chiave di Azure
* SQL Server

Se non si hanno a disposizione istanze esistenti di Key Vault e SQL Server, è possibile usare il collegamento alla distribuzione seguente:

<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure"/></a>
1. In **Gruppo di risorse** selezionare **Crea nuovo**. Assegnare al gruppo il nome **akvrotation**.
1. In **Account di accesso amministratore SQL** digitare il nome dell'account di accesso dell'amministratore SQL. 
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**

    ![Creare un gruppo di risorse](../media/rotate2.png)

A questo punto saranno disponibili le istanze di Key Vault e di SQL Server. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az resource list -o table
```

Il risultato sarà simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Creare e distribuire la funzione di rotazione delle password di SQL Server

Successivamente, creare un'app per le funzioni con un'identità gestita dal sistema, oltre ad altri componenti necessari, e distribuire le funzioni di rotazione delle password di SQL Server

L'app per le funzioni richiede questi componenti:
- Un piano di servizio app di Azure
- Un'app per le funzioni con le funzioni di rotazione delle password di SQL con trigger di evento e trigger HTTP 
- Un account di archiviazione necessario per la gestione dei trigger dell'app per le funzioni
- Un criterio di accesso per consentire all'identità dell'app per le funzioni di accedere ai segreti in Key Vault
- Una sottoscrizione di EventGrid per l'evento **SecretNearExpiry**

1. Selezionare il collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure"/></a>
1. Nell'elenco **Gruppo di risorse** selezionare **akvrotation**.
1. In **Nome di SQL Server** digitare il nome dell'istanza di SQL Server di cui ruotare la password
1. In **Nome dell'insieme di credenziali delle chiavi** digitare il nome dell'insieme di credenziali delle chiavi
1. In **Nome dell'app per le funzioni** digitare il nome dell'app per le funzioni
1. In **Nome del segreto** digitare il nome del segreto in cui verrà archiviata la password
1. In **URL del repository** digitare la posizione in GitHub del codice della funzione ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

   ![Selezionare Rivedi e crea.](../media/rotate3.png)

Dopo aver completato la procedura precedente, saranno disponibili un account di archiviazione, una server farm e un'app per le funzioni. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli
az resource list -o table
```

Il risultato sarà simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Per informazioni su come creare un'app per le funzioni e usare l'identità gestita per accedere a Key Vault, vedere [Creare un'app per le funzioni dal portale di Azure](../../azure-functions/functions-create-function-app-portal.md) e [Fornire l'autenticazione a Key Vault con un'identità gestita](../general/managed-identity.md).

### <a name="rotation-function"></a>Funzione di rotazione
La funzione distribuita nel passaggio precedente usa un evento per attivare la rotazione di un segreto aggiornando Key Vault e il database SQL. 

#### <a name="function-trigger-event"></a>Evento di attivazione della funzione

Questa funzione legge i dati dell'evento ed esegue la logica di rotazione:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logica di rotazione del segreto
Questo metodo di rotazione legge le informazioni del database dal segreto, crea una nuova versione del segreto e aggiorna il database con il nuovo segreto:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Il codice completo è disponibile in [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Aggiungere il segreto a Key Vault
Impostare il criterio di accesso per concedere agli utenti l'autorizzazione *Gestisci segreti*:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Creare un nuovo segreto con tag che contengono l'ID risorsa di SQL Server, il nome dell'accesso di SQL Server e un periodo di validità, in giorni, per il segreto. Specificare il nome del segreto e la password iniziale del database SQL (in questo esempio "Simple123"), quindi includere una data di scadenza impostata per il giorno successivo.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

La creazione di un segreto con una data di scadenza breve pubblicherà entro 15 minuti un evento `SecretNearExpiry`, che attiverà a sua volta la funzione di rotazione del segreto.

## <a name="test-and-verify"></a>Test e verifica

Per verificare l'avvenuta rotazione del segreto, passare a **Key Vault** > **Segreti**:

![Passare a segreti](../media/rotate8.png)

Aprire il segreto **sqlPassword** per visualizzare la versione originale e quella ruotata:

![Aprire il segreto sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Creare un'app Web

Per verificare le credenziali SQL, creare un'app Web. Questa app Web otterrà il segreto da Key Vault, estrarrà le informazioni e le credenziali del database SQL dal segreto e testerà la connessione a SQL Server.

L'app Web richiede questi componenti:
- Un'app Web con identità gestita dal sistema
- Un criterio di accesso per accedere ai segreti in Key Vault tramite l'identità gestita dell'app Web

1. Selezionare il collegamento alla distribuzione modelli di Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure"/></a>
1. Selezionare il gruppo di risorse **akvrotation**.
1. In **Nome di SQL Server** digitare il nome dell'istanza di SQL Server di cui ruotare la password
1. In **Nome dell'insieme di credenziali delle chiavi** digitare il nome dell'insieme di credenziali delle chiavi
1. In **Nome del segreto** digitare il nome del segreto in cui è archiviata la password
1. In **URL del repository** digitare la posizione in GitHub del codice del'app Web ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.


### <a name="open-the-web-app"></a>Aprire l'app Web

Passare all'URL dell'applicazione distribuita:
 
https://akvrotation-app.azurewebsites.net/

Quando l'applicazione viene aperta nel browser, verrà visualizzato il **valore del segreto generato** con un valore di **database connesso** impostato su *true*.

## <a name="learn-more"></a>Altre informazioni

- Esercitazione: [Rotazione per le risorse con due set di credenziali](tutorial-rotation-dual.md)
- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](../general/event-grid-overview.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](../general/event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../../event-grid/event-schema-key-vault.md)
