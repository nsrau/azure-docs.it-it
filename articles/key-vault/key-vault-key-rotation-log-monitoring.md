---
title: Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi end-to-end | Documentazione Microsoft
description: Utilizzare questa guida alle procedure per configurare la rotazione delle chiavi e monitorare i registri dell'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 6962a264787bd8a55b6f6a2ebdb6eeb615c33d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218407"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi

## <a name="introduction"></a>Introduzione

Dopo aver creato un insieme di credenziali delle chiavi, è possibile iniziare a usarle per archiviare le chiavi e i segreti. Le applicazioni non devono più rendere persistenti le chiavi o i segreti, ma li richiederanno all'insieme di credenziali delle chiavi in base alle esigenze. Un insieme di credenziali delle chiavi consente di aggiornare chiavi e segreti senza influire sul comportamento dell'applicazione, che apre un'ampia gamma di possibilità per la gestione di chiavi e segreti.

Questo articolo illustra come implementare una rotazione pianificata delle chiavi dell'account di archiviazione, monitorare i log di controllo dell'insieme di credenziali delle chiavi e generare avvisi quando vengono effettuate richieste impreviste. 

È innanzitutto necessario creare un insieme di credenziali delle chiavi utilizzando il metodo desiderato:

- [Impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure](quick-create-cli.md)
- [Impostare e recuperare un segreto dall'insieme di credenziali delle chiavi di Azure tramite Azure PowerShellSet and retrieve a secret from Azure Key Vault using Azure PowerShell](quick-create-powershell.md)
- [Impostare e recuperare un segreto dall'insieme di credenziali delle chiavi di Azure usando il portale di AzureSet and retrieve a secret from Azure Key Vault using Azure portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Memorizzare un segreto

Per consentire a un'applicazione di recuperare un segreto dall'insieme di credenziali delle chiavi, è prima necessario creare il segreto e caricarlo nell'insieme di credenziali.

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

```powershell
Connect-AzAccount
```

Nella finestra del browser popup immettere il nome utente e la password per l'account Azure.In the pop-up browser window, enter the username and password for your Azure account. PowerShell recupera tutte le sottoscrizioni associate a questo account e usa la prima per impostazione predefinita.

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

Dopo aver recuperato il segreto (in questo caso, la chiave dell'account di archiviazione), è necessario convertire la chiave in una stringa sicura e quindi creare un segreto con tale valore nell'insieme di credenziali delle chiavi.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ottenere quindi l'URI per il segreto creato. Questo URI è necessario in un passaggio successivo per chiamare l'insieme di credenziali delle chiavi e recuperare il segreto. Eseguire il comando di PowerShell seguente e prendere nota del valore ID, ovvero l'URI del segreto:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurare l'applicazione

Ora che si dispone di un segreto archiviato, è possibile utilizzare il codice per recuperarlo e utilizzarlo dopo l'esecuzione di alcuni altri passaggi.

Innanzitutto, è necessario registrare l'applicazione con Azure Active Directory.First, you must register your application with Azure Active Directory. Indicare quindi a Key Vault le informazioni sull'applicazione in modo che possa consentire richieste dall'applicazione.

> [!NOTE]
> L'applicazione deve essere creata nello stesso tenant di Azure Active Directory dell'insieme di credenziali delle chiavi.

1. Aprire **Azure Active Directory**.
2. Selezionare **Registrazioni app**. 
3. Selezionare Nuova registrazione applicazione per aggiungere un'applicazione ad Azure Active Directory.Select **New application registration** to add an application to Azure Active Directory.

    ![Aprire applicazioni in Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. In **Crea**, lasciare il tipo di applicazione come **App Web /API** e assegnare un nome all'applicazione. Assegnare all'applicazione un **URL di accesso**. Questo URL può essere qualsiasi cosa tu voglia per questa demo.

    ![Creare una registrazione dell'applicazione](./media/keyvault-keyrotation/create-app.png)

5. Dopo aver aggiunto l'applicazione ad Azure Active Directory, viene visualizzata la pagina dell'applicazione. Selezionare **Impostazioni**, quindi **Proprietà**. Copiare il valore **ID dell'applicazione**. Sarà necessario nei passaggi successivi.

Successivamente, generare una chiave per l'applicazione in modo che possa interagire con Azure Active Directory.Next, generate a key for your application so it can interact with Azure Active Directory. Per creare una chiave, selezionare **Chiavi** in **Impostazioni**. Prendere nota della chiave appena generata per l'applicazione Azure Active Directory. Saranno necessarie in un passaggio successivo. La chiave non sarà disponibile dopo aver lasciato questa sezione. 

![Chiavi dell'app di Azure Active DirectoryAzure Active Directory app keys](./media/keyvault-keyrotation/create-key.png)

Prima di stabilire le chiamate dall'applicazione all'insieme di credenziali delle chiavi, è necessario informare l'insieme di credenziali delle chiavi dell'applicazione e delle relative autorizzazioni. Il comando seguente usa il nome dell'insieme di credenziali e l'ID applicazione dell'app Azure Active Directory per concedere **all'applicazione** l'accesso all'insieme di credenziali delle chiavi.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

È ora possibile iniziare a compilare le chiamate dell'applicazione. Nell'applicazione è necessario installare i pacchetti NuGet necessari per interagire con Azure Key Vault e Azure Active Directory. Immettere i comandi seguenti nella console di Gestione pacchetti di Visual Studio. Al momento della stesura di questo articolo, la versione corrente del pacchetto di Azure Active Directory è 3.10.305231913, quindi confermare la versione più recente e l'aggiornamento in base alle esigenze.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Nel codice dell'applicazione creare una classe che contenga il metodo per l'autenticazione di Azure Active Directory. In questo esempio, tale classe è denominata **Utils**. Aggiungere l'istruzione `using` seguente:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Aggiungere quindi il metodo seguente per recuperare il token JWT da Azure Active Directory. Per la manutenibilità, è possibile spostare i valori stringa hardcoded nella configurazione Web o dell'applicazione.

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

Aggiungere il codice necessario per chiamare l'insieme di credenziali delle chiavi e recuperare il valore del segreto. In primo luogo, `using` è necessario aggiungere la seguente istruzione:

```csharp
using Microsoft.Azure.KeyVault;
```

Aggiungere le chiamate di metodo per richiamare l'insieme di credenziali delle chiavi e recuperare il segreto. In questo metodo viene specificato l'URI del segreto salvato in un passaggio precedente. Si noti l'utilizzo del metodo **GetToken** dalla classe **Utils** creata in precedenza.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando si esegue l'applicazione, verrà eseguita l'autenticazione in Azure Active Directory e quindi verrà recuperato il valore del segreto dall'insieme di credenziali delle chiavi di Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotazione delle chiavi con Automazione di Azure

> [!IMPORTANT]
> I runbook di Automazione `AzureRM` di Azure richiedono comunque l'uso del modulo.

È ora possibile impostare una strategia di rotazione per i valori archiviati come segreti dell'insieme di chiavi. I segreti possono essere ruotati in diversi modi:

- Nell'ambito di un processo manuale
- A livello di codice tramite chiamate APIProgrammatically by using API calls
- Tramite uno script di Automazione di Azure

Ai fini di questo articolo, si userà PowerShell combinato con Automazione di Azure per modificare la chiave di accesso di un account di archiviazione di Azure.For the purposes of this article, you'll use PowerShell combined with Azure Automation to change an Azure storage account's access key. Si aggiornerà quindi un segreto dell'insieme di credenziali delle chiavi con la nuova chiave.

Per consentire ad Automazione di Azure di impostare valori segreti nell'insieme di credenziali delle chiavi, è necessario ottenere l'ID client per la connessione **denominata AzureRunAsConnection**. Questa connessione è stata creata quando è stata stabilita l'istanza di Automazione di Azure.This connection was created when you established your Azure Automation instance. Per trovare questo ID, selezionare Asset dall'istanza di Automazione di Azure.To find this ID, select **Assets** from your Azure Automation instance. Selezionare Connessioni e quindi selezionare l'entità servizio **AzureRunAsConnection.From** there, select **Connections**, and then select the AzureRunAsConnection service principal. Prendere nota del valore **ApplicationId.**

![ID client di Automazione di Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

In **Asset**, selezionare **Moduli**. Selezionare **Raccolta**, quindi cercare e importare le versioni aggiornate di ciascuno dei moduli seguenti:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Al momento della stesura di questo articolo è necessario aggiornare solo i moduli indicati in precedenza per lo script seguente. Se il processo di automazione non riesce, verificare di aver importato tutti i moduli necessari e le relative dipendenze.

Dopo aver recuperato l'ID applicazione per la connessione di Automazione di Azure, è necessario indicare all'insieme di credenziali delle chiavi che l'applicazione dispone dell'autorizzazione per aggiornare i segreti nell'insieme di credenziali. Usare il seguente comando PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selezionare quindi **Runbooks** nell'istanza di Automazione di Azure e quindi **Aggiungi Runbook**. Selezionare **Creazione rapida**. Assegnare un nome al runbook e selezionare **PowerShell** come tipo di runbook. È possibile aggiungere una descrizione. Infine, selezionare **Crea**.

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

Nel riquadro dell'editor selezionare **Riquadro di test** per testare lo script. Dopo che lo script viene eseguito senza errori, è possibile selezionare **Pubblica**e quindi applicare una pianificazione per il runbook nel riquadro di configurazione del runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline di controllo dell'insieme di credenziali delle chiavi

Quando si configura un insieme di credenziali delle chiavi è possibile attivare il controllo per raccogliere log relativi alle richieste di accesso all'insieme di credenziali delle chiavi. Questi log vengono archiviati in un account di archiviazione di Azure designato e possono essere estratti, monitorati e analizzati. Lo scenario seguente usa funzioni di Azure, app per la logica di Azure e log di controllo dell'insieme di chiavi per creare una pipeline che invia un messaggio di posta elettronica quando un'app che non corrisponde all'ID app dell'app Web recupera i segreti dall'insieme di credenziali.

È prima di tutto necessario abilitare la registrazione per l'insieme di credenziali delle chiavi. Usare i comandi di PowerShell seguenti. (È possibile visualizzare i dettagli completi in [questo articolo sulla registrazione di key-vault](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dopo aver abilitato la registrazione, i log di controllo iniziano ad essere archiviati nell'account di archiviazione designato. Questi log contengono eventi relativi alla modalità di accesso all'insieme di credenziali delle chiavi, a quando avviene l'accesso e a chi lo esegue.

> [!NOTE]
> È possibile accedere alle informazioni di registrazione dopo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, Spesso sarà disponibile prima di questo.

Il passaggio successivo consiste nella [creazione di una coda del bus di servizio di Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Questa coda è dove vengono inseriti i registri di controllo dell'insieme di credenziali delle chiavi. Quando i messaggi del log di controllo sono nella coda, l'app per la logica li preleva e agisce su di essi. Creare un'istanza del bus di servizio con i passaggi seguenti:Create a Service Bus instance with the following steps:

1. Creare uno spazio dei nomi del bus di servizio (se si dispone già di uno che si desidera utilizzare, andare al passaggio 2).
2. Passare all'istanza del bus di servizio nel portale di Azure e selezionare lo spazio dei nomi in cui si vuole creare la coda.
3. Selezionare **Create a resource** > **Enterprise Integration** > **Service Bus**, quindi immettere i dettagli necessari.
4. Trovare le informazioni di connessione del bus di servizio selezionando lo spazio dei nomi e quindi **Informazioni di connessione**. Queste informazioni sono necessarie per la sezione successiva.

Successivamente, [creare una funzione](../azure-functions/functions-create-first-azure-function.md) di Azure per eseguire il polling dei log dell'insieme di credenziali delle chiavi all'interno dell'account di archiviazione e raccogliere nuovi eventi. Questa funzione verrà attivata in base a una pianificazione.

Per creare un'app per le funzioni di Azure, selezionare **Crea una risorsa**, cercare App **per**le funzioni nel marketplace e quindi selezionare **Crea**. Durante la creazione è possibile usare un piano di hosting esistente o crearne uno nuovo. Puoi anche optare per l'hosting dinamico. Per altre informazioni sulle opzioni di hosting per Funzioni di Azure, vedere Come ridimensionare funzioni di Azure.For more information about the hosting options for Azure Functions, see [How to scale Azure Functions](../azure-functions/functions-scale.md).

Dopo aver creato l'app per le funzioni di Azure, passare a essa e selezionare lo scenario **Timer** e **C\# ** per la lingua. Quindi selezionare **Crea questa funzione**.

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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
> Modificare le variabili nel codice precedente in modo che puntino all'account di archiviazione in cui sono scritti i registri dell'insieme di credenziali delle chiavi, all'istanza del bus di servizio creata in precedenza e al percorso specifico dei log di archiviazione dell'insieme di credenziali delle chiavi.

La funzione seleziona il file del log più recente dall'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, acquisisce gli eventi più recenti dal file e ne effettua il push in una coda del bus di servizio. 

Poiché un singolo file può avere più eventi, è necessario creare un file sync.txt che la funzione esamina anche per determinare il timestamp dell'ultimo evento che è stato prelevato. L'utilizzo di questo file garantisce che non si essepre più volte lo stesso evento. 

Il file sync.txt contiene un timestamp per l'ultimo evento rilevato. Quando i registri vengono caricati, devono essere ordinati in base ai relativi timestamp per garantire che vengano ordinati correttamente.

Per questa funzione, si fa riferimento a un paio di librerie aggiuntive che non sono disponibili in Funzioni di Azure.For this function, we reference a couple additional libraries that aren't available out of the box in Azure Functions. To include these libraries, we need Azure Functions to pull them by using NuGet. Nella casella **Codice** selezionare **Visualizza file**.

![Opzione "Visualizza file"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Aggiungere un file denominato project.json con il contenuto seguente:Add a file called project.json with the following content:

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

Dopo aver selezionato **Salva**, Funzioni di Azure scaricherà i file binari necessari.

Passare alla scheda **Integra** e assegnare al parametro timer un nome significativo da usare all'interno della funzione. Nel codice precedente, la funzione prevede che il timer venga chiamato *myTimer*. Specificare [un'espressione CRON](../app-service/webjobs-create.md#CreateScheduledCRON) per `0 * * * * *`il timer come segue: . Questa espressione causerà l'esecuzione della funzione una volta al minuto.

Nella stessa scheda **Integra** aggiungere un input del tipo **Archiviazione BLOB di Azure**. Questo input punterà al file sync.txt che contiene il timestamp dell'ultimo evento esaminato dalla funzione. Questo input sarà accessibile all'interno della funzione utilizzando il nome del parametro. Nel codice precedente, l'input di archiviazione BLOB di Azure prevede che il nome del parametro sia *inputBlob*. Selezionare l'account di archiviazione in cui si troverà il file sync.txt (potrebbe essere lo stesso account o un account di archiviazione diverso). Nel campo percorso specificare il percorso del `{container-name}/path/to/sync.txt`file nel formato .

Aggiungere un output di tipo **Archiviazione BLOB**di Azure . Questo output punterà al file sync.txt definito nell'input. Questo output viene utilizzato dalla funzione per scrivere il timestamp dell'ultimo evento esaminato. Il codice precedente prevede che questo parametro sia denominato *outputBlob*.

La funzione è ora pronta. Assicurarsi di tornare alla scheda **Sviluppo** e salvare il codice. Controllare la finestra di output per eventuali errori di compilazione e correggerli in base alle esigenze. Se il codice viene compilato, il codice dovrebbe ora controllare i registri dell'insieme di credenziali delle chiavi ogni minuto ed eseguire il push di nuovi eventi nella coda del bus di servizio definita. Le informazioni di registrazione verranno scritte nella finestra del log ogni volta che la funzione viene attivata.

### <a name="azure-logic-app"></a>App per la logica di Azure

Successivamente, è necessario creare un'app per la logica di Azure che preleva gli eventi che la funzione esegue il push nella coda del bus di servizio, analizza il contenuto e invia un messaggio di posta elettronica in base a una condizione corrispondente.

[Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) selezionando **Crea una risorsa** > **App per la logica**di**integrazione** > .

Dopo aver creato l'app per la logica, passare ad essa e selezionare **Modifica**. Nell'editor dell'app per la logica selezionare **Coda bus di servizio** e immettere le credenziali del bus di servizio per connetterla alla coda.

![Bus di servizio dell'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selezionare **Aggiungi una condizione**. Nella condizione, passare all'editor avanzato e immettere il codice seguente. Sostituire *APP_ID* con l'ID app effettivo dell'app Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Questa espressione restituisce essenzialmente **false** se *l'appid* dall'evento in ingresso (che è il corpo del messaggio del bus di servizio) non è *l'appid* dell'app.

A questo punto, creare un'azione in **SE NO, DO NOTHING**.

![Le app per la logica di Azure scelgono l'azioneAzure Logic Apps choose action](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Per l'azione, selezionare **Office 365 - inviare posta elettronica**. Compilare i campi per creare un messaggio di posta elettronica da inviare quando la condizione specificata restituisce **false**. Se non si dispone di Office 365, cercare alternative per ottenere gli stessi risultati.

Si dispone ora di una pipeline end-to-end che cerca i nuovi registri di controllo dell'insieme di chiavi una volta al minuto. Invia nuovi registri trovati in una coda del bus di servizio. L'app per la logica viene attivata quando arriva un nuovo messaggio nella coda. Se *l'appid* all'interno dell'evento non corrisponde all'ID app dell'applicazione chiamante, invia un messaggio di posta elettronica.
