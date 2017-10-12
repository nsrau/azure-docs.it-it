---
title: Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi end-to-end | Documentazione Microsoft
description: Usare questa procedura per configurare la rotazione delle chiavi e i log di controllo dell'insieme di credenziali delle chiavi.
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
ms.openlocfilehash: f98ba1e2da6924476392948a4d18c807d68e39e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi end-to-end
## <a name="introduction"></a>Introduzione
Dopo aver creato l'insieme di credenziali delle chiavi sarà possibile iniziare a usarlo per archiviare le chiavi e i segreti. Le applicazioni non devono più rendere persistenti le chiavi o i segreti, ma li richiederanno all'insieme di credenziali delle chiavi in base alle esigenze. In questo modo è possibile aggiornare le chiavi e i segreti senza influenzare il comportamento dell'applicazione. Si apre così un ampio ventaglio di possibilità per la gestione di chiavi e segreti.

Questo articolo illustra un esempio di uso dell'insieme di credenziali delle chiavi di Azure per archiviare un segreto, in questo caso una chiave dell'account di archiviazione di Azure a cui accede un'applicazione. Dimostra anche l'implementazione di una rotazione pianificata della chiave dell'account di archiviazione. Illustra infine come monitorare i log di controllo dell'insieme di credenziali delle chiavi e generare avvisi in caso di richieste impreviste.

> [!NOTE]
> Questa esercitazione non illustra nei dettagli la configurazione iniziale dell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md). Per le istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [Gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Configurare l'insieme di credenziali delle chiavi
Per consentire a un'applicazione di recuperare un segreto dall'insieme di credenziali delle chiavi, è prima necessario creare il segreto e caricarlo nell'insieme di credenziali. Per eseguire queste operazioni, avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

```powershell
Login-AzureRmAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. PowerShell recupera tutte le sottoscrizioni associate a questo account e usa la prima per impostazione predefinita.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario specificare quella usata per creare l'insieme di credenziali delle chiavi. Immettere il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzureRmSubscription
```

Per specificare la sottoscrizione associata all'insieme di credenziali delle chiavi da registrare, immettere:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Dato che questo articolo illustra l'archiviazione di una chiave dell'account di archiviazione come segreto, è necessario ottenere la chiave dell'account di archiviazione.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Dopo aver recuperato il segreto, in questo caso la chiave dell'account di archiviazione, è necessario convertirlo in una stringa sicura e quindi creare un segreto con quel valore nell'insieme di credenziali delle chiavi.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Ottenere quindi l'URI per il segreto creato. L'URI viene usato in un secondo momento, quando si chiama l'insieme di credenziali delle chiavi per recuperare il segreto. Eseguire questo comando PowerShell e prendere nota del valore ID, che rappresenta l'URI del segreto:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurare l'applicazione
Ora che il segreto è stato archiviato è possibile usare il codice per recuperarlo e usarlo. Per ottenere questo risultato, sono necessari alcuni passaggi. Il primo e più importante è registrare l'applicazione in Azure Active Directory e quindi indicare le informazioni sull'applicazione All'insieme di credenziali delle chiavi in modo da consentire richieste dall'applicazione.

> [!NOTE]
> L'applicazione deve essere creata nello stesso tenant di Azure Active Directory dell'insieme di credenziali delle chiavi.
>
>

Aprire la scheda Applicazioni di Azure Active Directory.

![Aprire applicazioni in Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Scegliere **Aggiungi** per aggiungere un'applicazione in Azure Active Directory.

![Scegliere Aggiungi](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Lasciare il tipo di applicazione **Applicazione Web e/o API Web** e immettere un nome per l'applicazione.

![Assegnare un nome all'applicazione](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Immettere **URL accesso** e **URI ID app** per l'applicazione. È possibile usare qualsiasi valore per questa demo. Sarà comunque possibile modificarli in un secondo momento se necessario.

![Specificare gli URI necessari](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Dopo aver aggiunto l'applicazione ad Azure Active Directory verrà visualizzata la pagina dell'applicazione. Fare clic sulla scheda **Configura** e quindi trovare e copiare il valore **ID client**. Annotare l'ID client per i passaggi successivi.

Generare quindi una chiave per l'applicazione in modo che possa interagire con Azure Active Directory. La chiave può essere creata nella sezione **Chiavi** della scheda **Configurazione**. Prendere nota della chiave appena generata dall'applicazione di Azure Active Directory per usarla in un secondo momento.

![Chiavi delle applicazioni di Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Prima di stabilire chiamate dall'applicazione nell'insieme di credenziali delle chiavi è necessario fornire informazioni sull'applicazione e le relative autorizzazioni all'insieme di credenziali delle chiavi. Il comando seguente recupera il nome dell'insieme di credenziali e l'ID client dall'app di Azure Active Directory e concede l'accesso **Get** all'insieme di credenziali delle chiavi per l'applicazione.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

A questo punto è possibile iniziare a creare le chiamate dell'applicazione. Nell'applicazione è necessario installare i pacchetti NuGet necessari per interagire con l'insieme di credenziali delle chiavi di Azure e Azure Active Directory. Immettere i comandi seguenti nella console di Gestione pacchetti di Visual Studio. Al momento della stesura di questo articolo la versione corrente del pacchetto di Azure Active Directory è 3.10.305231913, quindi si consiglia di verificare la versione più recente e aggiornare di conseguenza.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Nel codice dell'applicazione creare una classe che contenga il metodo per l'autenticazione di Azure Active Directory. In questo esempio, la classe è denominata **Utils**. Aggiungere l'istruzione using seguente:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Aggiungere quindi il metodo seguente per recuperare il token JWT da Azure Active Directory. Per ottimizzare la gestione, è consigliabile trasferire i valori di stringa hardcoded nella configurazione Web o dell'applicazione.

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

Aggiungere il codice necessario per chiamare l'insieme di credenziali delle chiavi e recuperare il valore del segreto. Aggiungere prima di tutto l'istruzione using seguente:

```csharp
using Microsoft.Azure.KeyVault;
```

Aggiungere le chiamate di metodo per richiamare l'insieme di credenziali delle chiavi e recuperare il segreto. In questo metodo viene specificato l'URI del segreto salvato in un passaggio precedente. Si noti l'uso del metodo **GetToken** dalla classe **Utils** creata in precedenza.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando si esegue l'applicazione, verrà eseguita l'autenticazione in Azure Active Directory e quindi verrà recuperato il valore del segreto dall'insieme di credenziali delle chiavi di Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotazione delle chiavi con Automazione di Azure
Sono disponibili diverse opzioni per l'implementazione di una strategia di rotazione per i valori memorizzati come segreti dell'insieme di credenziali delle chiavi di Azure. La rotazione dei segreti può essere eseguita nell'ambito di un processo manuale, a livello di codice usando chiamate API oppure con uno script di automazione. In questo articolo verrà usato Azure PowerShell insieme ad Automazione di Azure per modificare una chiave di accesso dell'account di archiviazione di Azure. Verrà quindi aggiornato un segreto dell'insieme di credenziali delle chiavi con la nuova chiave.

Per consentire ad Automazione di Azure di impostare i valori del segreto nell'insieme di credenziali delle chiavi, è necessario ottenere l'ID client per la connessione denominata AzureRunAsConnection, creata al momento della definizione dell'istanza di Automazione di Azure. È possibile trovare l'ID scegliendo **Asset** dall'istanza di Automazione di Azure. Scegliere quindi **Connessioni** e selezionare l'entità servizio **AzureRunAsConnection**. Prendere nota del valore di **ID applicazione**.

![ID client di Automazione di Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

In **Asset** scegliere **Moduli**. In **Moduli** selezionare **Raccolta** e quindi cercare e **importare** le versioni aggiornate di ognuno dei moduli seguenti:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Al momento della stesura di questo articolo è necessario aggiornare solo i moduli indicati in precedenza per lo script seguente. Se si verificano errori nel processo di automazione, verificare di avere importato tutti i moduli necessari e le relative dipendenze.
>
>

Dopo aver recuperato l'ID applicazione per la connessione di Automazione di Azure è necessario indicare all'insieme di credenziali delle chiavi che l'applicazione è autorizzata ad aggiornare i segreti dell'insieme di credenziali. A tale scopo è possibile usare il comando PowerShell seguente:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selezionare quindi **Runbook** nell'istanza di Automazione di Azure e quindi selezionare **Aggiungi runbook**. Selezionare **Creazione rapida**. Assegnare un nome al runbook e selezionare **PowerShell** come tipo di runbook. È possibile aggiungere una descrizione. Fare infine clic su **Crea**.

![Creare un runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Incollare lo script di PowerShell seguente nel riquadro dell'editor per il nuovo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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

#Optionally you may set the following as parameters
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

Nel riquadro dell'editor scegliere **Riquadro di test** per testare lo script. Dopo aver eseguito lo script senza errori è possibile selezionare **Pubblica** e quindi applicare una pianificazione per il runbook nel riquadro di configurazione del runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline di controllo dell'insieme di credenziali delle chiavi
Quando si configura un insieme di credenziali delle chiavi è possibile attivare il controllo per raccogliere log relativi alle richieste di accesso all'insieme di credenziali delle chiavi. Questi log vengono archiviati in un apposito account di archiviazione di Azure e possono essere estratti, monitorati e analizzati. Lo scenario seguente usa le funzioni di Azure, le app per la logica di Azure e i log di controllo dell'insieme di credenziali delle chiavi per creare una pipeline per l'invio di un messaggio di posta elettronica quando i segreti dell'insieme di credenziali vengono recuperati da un'app che corrisponde all'ID app dell'app Web.

È prima di tutto necessario abilitare la registrazione per l'insieme di credenziali delle chiavi. Questa operazione può essere eseguita tramite i comandi di PowerShell seguenti (per i dettagli completi, vedere [key-vault-logging](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Dopo aver abilitato la registrazione, i log di controllo iniziano la raccolta di informazioni nell'account di archiviazione designato. Questi log contengono eventi relativi alla modalità di accesso all'insieme di credenziali delle chiavi, a quando avviene l'accesso e a chi lo esegue.

> [!NOTE]
> È possibile accedere alle informazioni di registrazione dopo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma in genere saranno disponibili anche prima.
>
>

Il passaggio successivo consiste nella [creazione di una coda del bus di servizio di Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Questa è la posizione in cui verrà eseguito il push dei log di controllo dell'insieme di credenziali delle chiavi. Quando i messaggi del log di controllo sono nella coda, l'app per la logica li preleva e interviene su di essi. Creare un bus di servizio seguendo questa procedura:

1. Creare uno spazio dei nomi del bus di servizio. Se è già presente uno spazio dei nomi da usare a questo scopo, andare direttamente al passaggio 2.
2. Passare al bus di servizio nel portale di Azure e selezionare lo spazio dei nomi nel quale creare la coda.
3. Selezionare **Nuovo**, scegliere **Bus di servizio -> Coda** e immettere i dettagli necessari.
4. Selezionare le informazioni di connessione del bus di servizio scegliendo lo spazio dei nomi e facendo clic su **Informazioni di connessione**. Queste informazioni saranno necessarie per la sezione successiva.

[Creare una funzione di Azure](../azure-functions/functions-create-first-azure-function.md) per eseguire il polling dei log dell'insieme di credenziali delle chiavi nell'account di archiviazione e selezionare i nuovi eventi. Questa funzione verrà attivata in base alla pianificazione.

Per creare una funzione di Azure, scegliere **Nuovo -> App per le funzioni** nel portale di Azure. Durante la creazione è possibile usare un piano di hosting esistente o crearne uno nuovo. È anche possibile optare per l'hosting dinamico. Altre informazioni sulle opzioni di hosting delle funzioni sono disponibili in [Scalabilità di Funzioni di Azure](../azure-functions/functions-scale.md).

Dopo aver creato la funzione di Azure, passare alla funzione e scegliere una funzione timer e C\#, quindi fare clic su **Creare questa funzione**.

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

            //required to order by time as they may not be in the file
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
> Assicurarsi di sostituire le variabili nel codice precedente in modo che puntino all'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, al bus di servizio creato in precedenza e al percorso specifico dei log di archiviazione dell'insieme di credenziali delle chiavi.
>
>

La funzione seleziona il file del log più recente dall'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, acquisisce gli eventi più recenti dal file e ne effettua il push in una coda del bus di servizio. Dato che un singolo file può avere più eventi, è necessario creare un file sync.txt anch'esso esaminato dalla funzione per determinare il timestamp dell'ultimo evento selezionato. In questo modo è possibile assicurarsi di non effettuare il push dello stesso evento più volte. Questo file sync.txt contiene un timestamp per l'ultimo evento rilevato. I log, quando caricati, devono essere ordinati in base al timestamp per far sì che vengano ordinati correttamente.

Per questa funzione si fa riferimento a un paio di altre librerie non disponibili per impostazione predefinita in Funzioni di Azure. Per includerle è necessario che Funzioni di Azure le recuperi tramite NuGet. Scegliere l'opzione **Visualizza file**.

![Opzione Visualizza file](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Aggiungere poi un file denominato project.json con il contenuto seguente:

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
Dopo aver fatto clic su **Salva** , Funzioni di Azure scaricherà i file binari necessari.

Passare alla scheda **Integra** e assegnare al parametro timer un nome significativo da usare all'interno della funzione. Nel codice precedente è previsto che il timer si chiami *myTimer*. Specificare un'[espressione CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) come segue: 0 \* \* \* \* \* per il timer che attiverà l'esecuzione della funzione una volta al minuto.

Nella stessa scheda **Integra** aggiungere un input di tipo **Archiviazione BLOB di Azure**. Questo punterà al file sync.txt che contiene il timestamp dell'ultimo evento esaminato dalla funzione. L'input sarà disponibile all'interno della funzione in base al nome del parametro. Nel codice precedente, l'input Archiviazione BLOB di Azure prevede che il nome del parametro sia *inputBlob*. Scegliere l'account di archiviazione in cui risiederà il file sync.txt. Può essere lo stesso account di archiviazione o un account diverso. Nel campo del percorso specificare il percorso in cui si trova il file, nel formato {nome-contenitore}/path/to/sync.txt.

Aggiungere un output di tipo *Archiviazione BLOB di Azure*. Anche questo punterà al file sync.txt appena definito nell'input. Viene usato dalla funzione per scrivere il timestamp dell'ultimo evento esaminato. Il codice precedente prevede che questo parametro sia denominato *outputBlob*.

A questo punto la funzione è pronta. Assicurarsi di tornare alla scheda **Sviluppo** e salvare il codice. Verificare se nella finestra di output sono presenti errori di compilazione ed eventualmente correggerli. Dopo la compilazione, il codice controllerà i log dell'insieme di credenziali delle chiavi ogni minuto, effettuando il push di eventuali nuovi eventi nella coda del bus di servizio definito. Le informazioni di registrazione verranno scritte nella finestra del log ogni volta che la funzione viene attivata.

### <a name="azure-logic-app"></a>App per la logica di Azure
A questo punto è necessario creare un'app per la logica di Azure che seleziona gli eventi di cui la funzione effettua il push nella coda del bus di servizio, analizza il contenuto e invia un messaggio di posta elettronica in base alla soddisfazione di una condizione.

Per [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) passare a **Nuovo -> App per la logica**.

Dopo averla creata, passare all'app per la logica e scegliere **modifica**. Nell'editor di app per la logica scegliere **Coda del bus di servizio** e immettere le credenziali del bus di servizio per connetterlo alla coda.

![Bus di servizio dell'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Scegliere quindi **Aggiungi una condizione**. Nella condizione passare all'editor avanzato e immettere il codice seguente, sostituendo APP_ID con l'effettivo APP_ID dell'app Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Questa espressione restituisce essenzialmente **false** se il valore*appid* dell'evento in ingresso, ovvero il corpo del messaggio del bus di servizio, non è l'*appid* dell'app.

Creare ora un'azione in **Se no, non fare nulla** .

![Scelta dell'azione per l'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Per l'azione, scegliere **Office 365 - send email**. Compilare i campi per creare un messaggio di posta elettronica da inviare quando la condizione specificata restituisce **false**. Se non si ha Office 365 è possibile valutare alternative per ottenere lo stesso risultato.

A questo punto è disponibile una pipeline end-to-end che controlla se sono disponibili nuovi log di controllo dell'insieme di credenziali delle chiavi ogni minuto. La pipeline effettua il push dei nuovi log trovati in una coda del bus di servizio. L'app per la logica viene attivata quando arriva un nuovo messaggio nella coda. Se l'*appid* all'interno dell'evento non corrisponde all'ID app dell'applicazione chiamante, invia un messaggio di posta elettronica.
