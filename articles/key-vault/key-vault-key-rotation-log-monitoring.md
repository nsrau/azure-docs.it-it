---
title: Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi end-to-end | Documentazione Microsoft
description: Usare questa guida dettagliata per configurare la rotazione delle chiavi e monitorare i log dell'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1f60ce3a23882a48e6008b76c0eedcab99e013b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883458"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi

## <a name="introduction"></a>Introduzione

Dopo aver creato un insieme di credenziali delle chiavi, è possibile iniziare a usarle per archiviare le chiavi e i segreti. Le applicazioni non devono più rendere persistenti le chiavi o i segreti, ma li richiederanno all'insieme di credenziali delle chiavi in base alle esigenze. Un insieme di credenziali delle chiavi consente di aggiornare le chiavi e i segreti senza influire sul comportamento dell'applicazione, che offre una vasta gamma di possibilità per la gestione delle chiavi e dei segreti.

>[!IMPORTANT]
> Gli esempi inclusi in questo articolo vengono forniti solo a scopo illustrativo. Non sono destinati all'uso in produzione. 

Questo articolo illustra:

- Un esempio dell'uso dell'insieme di credenziali delle chiavi di Azure per archiviare un segreto. In questo articolo, il segreto archiviato è la chiave dell'account di archiviazione di Azure a cui accede un'applicazione. 
- Come implementare una rotazione pianificata della chiave dell'account di archiviazione.
- Come monitorare i log di controllo dell'insieme di credenziali delle chiavi e generare avvisi quando vengono eseguite richieste impreviste.

> [!NOTE]
> Questo articolo non illustra in dettaglio la configurazione iniziale dell'insieme di credenziali delle chiavi. Per queste informazioni, vedere [Cos'è Azure Key Vault?](key-vault-overview.md). Per istruzioni sull'interfaccia della riga di comando multipiattaforma, vedere [manage Key Vault using the Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Configurare l'insieme di credenziali delle chiavi

Per consentire a un'applicazione di recuperare un segreto dall'insieme di credenziali delle chiavi, è prima necessario creare il segreto e caricarlo nell'insieme di credenziali.

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

```powershell
Connect-AzAccount
```

Nella finestra popup del browser immettere il nome utente e la password per l'account Azure. PowerShell recupera tutte le sottoscrizioni associate a questo account e usa la prima per impostazione predefinita.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario specificare quella usata per creare l'insieme di credenziali delle chiavi. Immettere il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzSubscription
```

Per specificare la sottoscrizione associata all'insieme di credenziali delle chiavi da registrare, immettere:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Dato che questo articolo illustra l'archiviazione di una chiave dell'account di archiviazione come segreto, è necessario ottenere la chiave dell'account di archiviazione.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Dopo aver recuperato il segreto, in questo caso la chiave dell'account di archiviazione, è necessario convertire la chiave in una stringa sicura e quindi creare un segreto con quel valore nell'insieme di credenziali delle chiavi.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ottenere quindi l'URI per il segreto creato. Questo URI sarà necessario in un passaggio successivo per chiamare l'insieme di credenziali delle chiavi e recuperare il segreto. Eseguire il comando PowerShell seguente e prendere nota del valore ID, che è l'URI del segreto:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurare l'applicazione

Ora che è stato archiviato un segreto, è possibile usare il codice per recuperarlo e usarlo dopo aver eseguito alcuni passaggi.

Prima di tutto, è necessario registrare l'applicazione con Azure Active Directory. Quindi, indicare Key Vault le informazioni dell'applicazione in modo che possa consentire le richieste dall'applicazione.

> [!NOTE]
> L'applicazione deve essere creata nello stesso tenant di Azure Active Directory dell'insieme di credenziali delle chiavi.

1. Aprire **Azure Active Directory**.
2. Selezionare **Registrazioni per l'app**. 
3. Selezionare **registrazione nuova applicazione** per aggiungere un'applicazione a Azure Active Directory.

    ![Aprire applicazioni in Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. In **Crea**lasciare il tipo di applicazione come **app Web/API** e assegnare un nome all'applicazione. Assegnare all'applicazione un **URL di accesso**. Questo URL può essere qualsiasi elemento desiderato per questa demo.

    ![Creare una registrazione dell'applicazione](./media/keyvault-keyrotation/create-app.png)

5. Una volta aggiunta l'applicazione alla Azure Active Directory, viene visualizzata la pagina applicazione. Selezionare **Impostazioni**, quindi selezionare **Proprietà**. Copiare il valore **ID dell'applicazione**. Sarà necessario nei passaggi successivi.

Successivamente, generare una chiave per l'applicazione in modo che possa interagire con Azure Active Directory. Per creare una chiave, selezionare **chiavi** in **Impostazioni**. Prendere nota della chiave appena generata per l'applicazione Azure Active Directory. Saranno necessarie in un passaggio successivo. La chiave non sarà disponibile dopo l'uscita da questa sezione. 

![Chiavi app Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Prima di stabilire chiamate dall'applicazione nell'insieme di credenziali delle chiavi, è necessario indicare all'insieme di credenziali delle chiavi l'applicazione e le relative autorizzazioni. Il comando seguente usa il nome dell'insieme di credenziali e l'ID applicazione dell'app Azure Active Directory per concedere **all'applicazione l'** accesso a Key Vault.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

A questo punto si è pronti per iniziare a compilare le chiamate dell'applicazione. Nell'applicazione è necessario installare i pacchetti NuGet necessari per interagire con Azure Key Vault e Azure Active Directory. Immettere i comandi seguenti nella console di Gestione pacchetti di Visual Studio. Al momento della stesura di questo articolo, la versione corrente del pacchetto di Azure Active Directory è 3.10.305231913, quindi verificare la versione più recente e aggiornarla in base alle esigenze.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Nel codice dell'applicazione creare una classe che contenga il metodo per l'autenticazione di Azure Active Directory. In questo esempio, la classe è denominata **Utils**. Aggiungere l'istruzione `using` seguente:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Aggiungere quindi il metodo seguente per recuperare il token JWT da Azure Active Directory. Per garantire la gestibilità, potrebbe essere necessario spostare i valori di stringa hardcoded nella configurazione Web o dell'applicazione.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Aggiungere il codice necessario per chiamare l'insieme di credenziali delle chiavi e recuperare il valore del segreto. In primo luogo, è necessario aggiungere `using` l'istruzione seguente:

```csharp
using Microsoft.Azure.KeyVault;
```

Aggiungere le chiamate di metodo per richiamare l'insieme di credenziali delle chiavi e recuperare il segreto. In questo metodo viene specificato l'URI del segreto salvato in un passaggio precedente. Si noti l'uso del metodo **GetToken** dalla classe **utils** creata in precedenza.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando si esegue l'applicazione, verrà eseguita l'autenticazione in Azure Active Directory e quindi verrà recuperato il valore del segreto dall'insieme di credenziali delle chiavi di Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotazione delle chiavi con Automazione di Azure

> [!IMPORTANT]
> Il manuali operativi di automazione di Azure richiede ancora l' `AzureRM` uso del modulo.

A questo punto è possibile configurare una strategia di rotazione per i valori archiviati come segreti Key Vault. I segreti possono essere ruotati in diversi modi:

- Come parte di un processo manuale
- A livello di codice tramite chiamate API
- Tramite uno script di automazione di Azure

Ai fini di questo articolo, si userà PowerShell combinato con automazione di Azure per modificare la chiave di accesso dell'account di archiviazione di Azure. Si aggiornerà quindi un segreto di Key Vault con la nuova chiave.

Per consentire ad automazione di Azure di impostare i valori del segreto nell'insieme di credenziali delle chiavi, è necessario ottenere l'ID client per la connessione denominata **AzureRunAsConnection**. Questa connessione è stata creata al momento della creazione dell'istanza di automazione di Azure. Per trovare questo ID, selezionare **Asset** dall'istanza di automazione di Azure. Da qui selezionare **Connections (connessioni**) e quindi selezionare l'entità servizio **AzureRunAsConnection** . Prendere nota del valore **ApplicationID** .

![ID client di Automazione di Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

In **Asset**selezionare **moduli**. Selezionare **raccolta**, quindi cercare e importare le versioni aggiornate di ognuno dei moduli seguenti:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Al momento della stesura di questo articolo è necessario aggiornare solo i moduli indicati in precedenza per lo script seguente. Se il processo di automazione non riesce, verificare di aver importato tutti i moduli necessari e le relative dipendenze.

Dopo aver recuperato l'ID applicazione per la connessione di automazione di Azure, è necessario indicare all'insieme di credenziali delle chiavi che l'applicazione è autorizzata ad aggiornare i segreti nell'insieme di credenziali. Usare il comando di PowerShell seguente:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selezionare quindi **manuali operativi** nell'istanza di automazione di Azure e quindi selezionare **Aggiungi Runbook**. Selezionare **Creazione rapida**. Assegnare un nome al Runbook e selezionare **PowerShell** come tipo di Runbook. È possibile aggiungere una descrizione. Infine, selezionare **Crea**.

![Creare un runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Incollare lo script di PowerShell seguente nel riquadro dell'editor per il nuovo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Nel riquadro dell'editor selezionare **riquadro di test** per testare lo script. Dopo l'esecuzione dello script senza errori, è possibile selezionare **pubblica**, quindi è possibile applicare una pianificazione per il Runbook nel riquadro di configurazione di Runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline di controllo dell'insieme di credenziali delle chiavi

Quando si configura un insieme di credenziali delle chiavi è possibile attivare il controllo per raccogliere log relativi alle richieste di accesso all'insieme di credenziali delle chiavi. Questi log vengono archiviati in un account di archiviazione di Azure designato e possono essere estratti, monitorati e analizzati. Lo scenario seguente usa funzioni di Azure, app per la logica di Azure e i log di controllo dell'insieme di credenziali delle chiavi per creare una pipeline che invii un messaggio di posta elettronica quando un'app che non corrisponde all'ID app dell'app Web recupera i segreti dall'insieme di credenziali.

È prima di tutto necessario abilitare la registrazione per l'insieme di credenziali delle chiavi. Usare i comandi di PowerShell seguenti. È possibile visualizzare i dettagli completi in [questo articolo su Key-Vault-Logging](key-vault-logging.md).

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dopo aver abilitato la registrazione, i log di controllo vengono archiviati nell'account di archiviazione designato. Questi log contengono eventi relativi alla modalità di accesso all'insieme di credenziali delle chiavi, a quando avviene l'accesso e a chi lo esegue.

> [!NOTE]
> È possibile accedere alle informazioni di registrazione dopo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, Spesso sarà disponibile prima di questo.

Il passaggio successivo consiste nella [creazione di una coda del bus di servizio di Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Questa coda è la posizione in cui vengono inseriti i log di controllo dell'insieme di credenziali chiave. Quando i messaggi di log di controllo si trovano nella coda, l'app per la logica li preleva e agisce su di essi. Creare un'istanza del bus di servizio con i passaggi seguenti:

1. Creare uno spazio dei nomi del bus di servizio (se ne è già presente uno che si vuole usare, andare al passaggio 2).
2. Passare all'istanza del bus di servizio nel portale di Azure e selezionare lo spazio dei nomi in cui si vuole creare la coda.
3. Selezionare **Crea una risorsa** > **integrazione aziendale** > **bus di servizio**e quindi immettere i dettagli richiesti.
4. Trovare le informazioni di connessione del bus di servizio selezionando lo spazio dei nomi e quindi selezionando **informazioni di connessione**. Queste informazioni sono necessarie per la sezione successiva.

Successivamente, [creare una funzione di Azure](../azure-functions/functions-create-first-azure-function.md) per eseguire il polling dei log dell'insieme di credenziali delle chiavi nell'account di archiviazione e selezionare nuovi eventi. Questa funzione verrà attivata in base a una pianificazione.

Per creare un'app per le funzioni di Azure, selezionare **Crea una risorsa**, cercare **app per le funzioni**nel Marketplace e quindi selezionare **Crea**. Durante la creazione è possibile usare un piano di hosting esistente o crearne uno nuovo. È anche possibile scegliere di ospitare dinamicamente. Per altre informazioni sulle opzioni di hosting per funzioni di Azure, vedere [come ridimensionare funzioni di Azure](../azure-functions/functions-scale.md).

Dopo aver creato l'app per le funzioni di Azure, passare a essa e selezionare lo scenario del **timer** e **C\#**  per la lingua. Selezionare quindi **crea questa funzione**.

![Pannello iniziale di Funzioni di Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Nella scheda **Sviluppo** sostituire il codice run.csx con il seguente:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Modificare le variabili nel codice precedente in modo che puntino all'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, all'istanza del bus di servizio creata in precedenza e al percorso specifico dei log di archiviazione dell'insieme di credenziali delle chiavi.

La funzione seleziona il file del log più recente dall'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, acquisisce gli eventi più recenti dal file e ne effettua il push in una coda del bus di servizio. 

Poiché un singolo file può avere più eventi, è necessario creare un file Sync. txt che la funzione esamina anche per determinare il timestamp dell'ultimo evento selezionato. L'uso di questo file garantisce che non si inserisca più volte lo stesso evento. 

Il file Sync. txt contiene un timestamp per l'ultimo evento rilevato. Quando i log vengono caricati, devono essere ordinati in base ai timestamp per assicurarsi che siano ordinati correttamente.

Per questa funzione, si fa riferimento a un paio di librerie aggiuntive non disponibili in funzioni di Azure. Per includere queste librerie, è necessario usare funzioni di Azure per eseguire il pull tramite NuGet. Nella casella **codice** selezionare **Visualizza file**.

![Opzione "Visualizza file"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Aggiungere un file denominato Project. JSON con il contenuto seguente:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Dopo aver selezionato **Salva**, funzioni di Azure scaricherà i file binari necessari.

Passare alla scheda **Integra** e assegnare al parametro timer un nome significativo da usare all'interno della funzione. Nel codice precedente, la funzione prevede che il timer venga chiamato il *timeout*. Specificare un' [espressione cron](../app-service/webjobs-create.md#CreateScheduledCRON) per il timer nel modo seguente `0 * * * * *`:. Questa espressione comporterà l'esecuzione della funzione una volta al minuto.

Nella stessa scheda **integra** aggiungere un input di tipo **archiviazione BLOB di Azure**. Questo input punterà al file Sync. txt che contiene il timestamp dell'ultimo evento esaminato dalla funzione. Questo input sarà accessibile all'interno della funzione utilizzando il nome del parametro. Nel codice precedente, l'input dell'archiviazione BLOB di Azure prevede che il nome del parametro sia *inputBlob*. Selezionare l'account di archiviazione in cui verrà memorizzato il file Sync. txt (potrebbe essere lo stesso account di archiviazione o un account diverso). Nel campo percorso specificare il percorso del file nel formato `{container-name}/path/to/sync.txt`.

Aggiungere un output di tipo **archiviazione BLOB di Azure**. Questo output punterà al file Sync. txt definito nell'input. Questo output viene usato dalla funzione per scrivere il timestamp dell'ultimo evento esaminato. Il codice precedente prevede che questo parametro sia denominato *outputBlob*.

La funzione è ora pronta. Assicurarsi di tornare alla scheda **Sviluppo** e salvare il codice. Controllare la finestra di output per individuare eventuali errori di compilazione e correggerli in base alle esigenze. Se il codice viene compilato, il codice dovrebbe ora controllare i log dell'insieme di credenziali delle chiavi ogni minuto ed effettuare il push di eventuali nuovi eventi nella coda del bus di servizio definita. Le informazioni di registrazione verranno scritte nella finestra del log ogni volta che la funzione viene attivata.

### <a name="azure-logic-app"></a>App per la logica di Azure

Successivamente, è necessario creare un'app per la logica di Azure che preleva gli eventi che la funzione esegue il push nella coda del bus di servizio, analizza il contenuto e invia un messaggio di posta elettronica in base a una condizione corrispondente.

[Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) selezionando **Crea una risorsa** > **integrazione** > app per la**logica**.

Dopo aver creato l'app per la logica, passare a essa e selezionare **modifica**. Nell'Editor app per la logica selezionare **coda del bus di servizio** e immettere le credenziali del bus di servizio per connetterlo alla coda.

![Bus di servizio dell'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selezionare **Aggiungi una condizione**. Nella condizione passare all'editor avanzato e immettere il codice seguente. Sostituire *APP_ID* con l'ID app effettivo dell'app Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Questa espressione sostanzialmente restituisce **false** se l' *AppID* dell'evento in ingresso, ovvero il corpo del messaggio del bus di servizio, non è l' *AppID* dell'app.

A questo punto, creare un'azione in **se no, non eseguire alcuna**operazione.

![App per la logica di Azure scegliere un'azione](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Per l'azione, selezionare **Office 365-Invia messaggio di posta elettronica**. Compilare i campi per creare un messaggio di posta elettronica da inviare quando la condizione specificata restituisce **false**. Se non si dispone di Office 365, cercare le alternative per ottenere gli stessi risultati.

A questo punto è disponibile una pipeline end-to-end che cerca i nuovi registri di controllo dell'insieme di credenziali delle chiavi una volta al minuto. Inserisce i nuovi log che trova in una coda del bus di servizio. L'app per la logica viene attivata quando arriva un nuovo messaggio nella coda. Se l' *AppID* all'interno dell'evento non corrisponde all'ID app dell'applicazione chiamante, invia un messaggio di posta elettronica.
