<properties
	pageTitle="Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end | Microsoft Azure"
	description="Usare questa procedura per configurare la rotazione delle chiavi e i log di controllo dell'insieme di credenziali delle chiavi"
	services="key-vault"
	documentationCenter=""
	authors="swgriffith"
	manager=""
	tags=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="jodehavi;stgriffi"/>
#Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end

##Introduzione

Dopo aver creato l'insieme di credenziali delle chiavi di Azure sarà possibile iniziare a usare questo insieme di credenziali per archiviare le chiavi e i segreti. Le applicazioni non devono più rendere persistenti le chiavi o i segreti, ma li richiederanno all'insieme di credenziali delle chiavi in base alle esigenze. In questo modo sarà possibile aggiornare le chiavi e i segreti senza influenzare il comportamento dell'applicazione; si apre così un ampio ventaglio di possibilità per la gestione del comportamento di chiavi e segreti.

Questo articolo illustra un esempio di uso dell'insieme di credenziali delle chiavi di Azure per archiviare un segreto, in questo caso una chiave dell'account di archiviazione di Azure cui accede un'applicazione. Dimostra anche l'implementazione di una rotazione pianificata della chiave dell'account di archiviazione. Illustra infine come monitorare i log di controllo dell'insieme di credenziali delle chiavi e generare avvisi in caso di richieste impreviste.

> [AZURE.NOTE] Questa esercitazione non illustra nei dettagli la configurazione iniziale dell'insieme di credenziali delle chiavi di Azure. Per altre informazioni, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md). In alternativa, per le istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [questa esercitazione equivalente](key-vault-manage-with-cli.md).

##Configurazione dell'insieme di credenziali delle chiavi

Per consentire a un'applicazione di recuperare un segreto dall'insieme di credenziali delle chiavi di Azure, è prima necessario creare il segreto e caricarlo nell'insieme di credenziali. Questa operazione può essere facilmente eseguita con PowerShell come illustrato di seguito.

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

```powershell
Login-AzureRmAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzureRmSubscription
```

Per specificare quindi la sottoscrizione associata all'insieme di credenziali delle chiavi da registrare, digitare:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Questo articolo illustra l'archiviazione di una chiave dell'account di archiviazione come segreto, quindi è necessario ottenere la chiave dell'account di archiviazione.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Dopo aver recuperato il segreto, in questo caso la chiave dell'account di archiviazione, sarà necessario convertirlo in una stringa sicura e quindi creare un segreto con quel valore nell'insieme di credenziali delle chiavi.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Sarà quindi necessario ottenere l'URI per il segreto appena creato. L'URI verrà usato in un secondo momento, quando si chiama l'insieme di credenziali delle chiavi per recuperare il segreto. Eseguire questo comando PowerShell e prendere nota del valore 'Id', che rappresenta l'URI del segreto.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##Configurazione dell'applicazione

Ora che il segreto è stato archiviato sarà necessario recuperarlo e usarlo dal codice. Sono necessari pochi passaggi, il primo e più importante è registrare l'applicazione con Azure Active Directory e quindi indicare le informazioni sull'applicazione nell'insieme di credenziali delle chiavi di Azure in modo da consentire richieste dall'applicazione.

> [AZURE.NOTE] L'applicazione deve essere creata nello stesso tenant di Azure Active Directory dell'insieme di credenziali delle chiavi.

Passare prima alla scheda Applicazioni di Azure Active Directory

![Aprire Applicazioni in Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Fare clic su 'Aggiungi' per aggiungere una nuova applicazione ad Azure AD

![Scegliere Aggiungi applicazione](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Lasciare il tipo di applicazione 'Applicazione Web e/o API Web' e immettere un nome per l'applicazione.

![Assegnare un nome all'applicazione](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Immettere un 'URL accesso' e un 'URI ID app' per l'applicazione. Può essere usato qualsiasi valore per questa demo; sarà possibile modificarli in un secondo momento se necessario.

![Specificare gli URI necessari](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Dopo aver aggiunto l'applicazione ad Azure AD verrà visualizzata la pagina dell'applicazione. Fare clic sulla scheda 'Configura' e quindi trovare e copiare il valore 'ID Client'. Annotare l'ID client per i passaggi successivi.

Sarà quindi necessario generare una chiave per consentire all'applicazione di interagire con Azure AD. La chiave può essere creata nella sezione 'Chiavi' della scheda 'Configurazione'. Annotare la chiave appena generata dall'applicazione di Azure AD per usarla in un secondo momento.

![Chiavi dell'app di Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Prima di stabilire chiamate dall'applicazione nell'insieme di credenziali delle chiavi è necessario indicare informazioni sull'applicazione e le relative autorizzazioni nell'insieme di credenziali delle chiavi. Il comando seguente recupera il nome dell'insieme di credenziali e l'ID client dall'app di Azure AD e concede l'accesso 'Get' all'insieme di credenziali delle chiavi per l'applicazione.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

A questo punto è possibile iniziare a compilare le chiamate dell'applicazione. Nell'applicazione sarà prima necessario installare i pacchetti NuGet per interagire con l'insieme di credenziali delle chiavi di Azure e Azure Active Directory. Immettere i comandi seguenti nella console di Gestione pacchetti di Visual Studio. Si noti che alla stesura di questo articolo la versione corrente del pacchetto di Active Directory è 3.10.305231913, quindi si consiglia di verificare la versione più recente e aggiornare di conseguenza.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Nel codice dell'applicazione, creare una classe che contenga il metodo per l'autenticazione di Active Directory. In questo esempio, la classe è denominata 'Utils'. Sarà quindi necessario aggiungere l'istruzione using seguente.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Aggiungere quindi il metodo seguente per recuperare il token JWT da Azure AD. Per ottimizzare la gestione è consigliabile trasferire i valori di stringa hardcoded nella configurazione Web o dell'applicazione.

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

È infine possibile aggiungere il codice necessario per chiamare l'insieme di credenziali delle chiavi e recuperare il valore del segreto. Aggiungere prima l'istruzione using seguente.

```csharp
using Microsoft.Azure.KeyVault;
```

Aggiungere quindi le chiamate di metodo per richiamare l'insieme di credenziali delle chiavi e recuperare il segreto. In questo metodo viene specificato l'URI del segreto salvato in un passaggio precedente. Si noti l'uso del metodo GetToken dalla classe Utils creata in precedenza.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando si esegue l'applicazione, verrà eseguita l'autenticazione ad Azure Active Directory e quindi recuperato il valore del segreto dall'insieme di credenziali delle chiavi di Azure.

##Rotazione delle chiavi con Automazione di Azure

Sono disponibili diverse opzioni per l'implementazione di una strategia di rotazione per i valori memorizzati come segreti dell'insieme di credenziali delle chiavi di Azure. I segreti possono essere ruotati nell'ambito di un processo manuale, a livello di codice sfruttando le chiamate API oppure con uno script di automazione. In questo articolo verrà usato Azure PowerShell insieme ad Automazione di Azure per modificare una chiave di accesso dell'account di archiviazione di Azure, quindi un secreto dell'insieme di credenziali delle chiavi verrà aggiornato con la nuova chiave.

Per consentire ad Automazione di Azure di impostare i valori del segreto nell'insieme di credenziali delle chiavi sarà necessario ottenere l'ID client per la connessione denominata 'AzureRunAsConnection', creata quando è stata definita l'istanza di Automazione di Azure. L'ID può essere ottenuto scegliendo 'Asset' dall'istanza di Automazione di Azure. Scegliere quindi 'Connessioni' e selezionare l'entità servizio 'AzureRunAsConnection'. Prendere nota del valore di 'ID applicazione'.

![ID client di Automazione di Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Sempre nella finestra Asset scegliere 'Moduli'. Nei moduli selezionare 'Raccolta' e quindi cercare e importare le versioni aggiornate di ognuno dei moduli seguenti.

	Azure
	Azure.Storage	
	AzureRM.Profile
	AzureRM.KeyVault
	AzureRM.Automation
	AzureRM.Storage
	
> [AZURE.NOTE] Alla stesura di questo articolo è necessario aggiornare solo i moduli indicati in precedenza per lo script condiviso di seguito. Se si verificano errori nel processo di automazione è necessario verificare che tutti i moduli necessari e le relative dipendenze siano stati importati.

Dopo aver recuperato l'ID applicazione per la connessione di Automazione di Azure sarà necessario specificare nell'insieme di credenziali delle chiavi di Azure che l'applicazione è autorizzata ad aggiornare i segreti dell'insieme di credenziali. A tale scopo è possibile usare il comando PowerShell seguente.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Selezionare quindi la risorsa 'Runbook' nell'istanza di Automazione di Azure e quindi 'Aggiungi runbook'. Selezionare 'Creazione rapida'. Assegnare un nome al runbook e selezionare 'PowerShell' come tipo di runbook. Aggiungere eventualmente una descrizione. Fare infine clic su 'Crea'.

![Creare un runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Nel riquadro dell'editor per il nuovo runbook incollare lo script di PowerShell seguente.

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
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Nel riquadro dell'editor è possibile scegliere 'Riquadro di test' per provare lo script. Dopo aver eseguito lo script senza errori è possibile selezionare l'opzione 'Pubblica' e quindi applicare una pianificazione per il runbook nel riquadro di configurazione del runbook.

##Pipeline del controllo dell'insieme di credenziali delle chiavi

Quando si configura un insieme di credenziali delle chiavi di Azure è possibile attivare il controllo per raccogliere log relativi alle richieste di accesso all'insieme di credenziali delle chiavi. Questi log vengono archiviati in un apposito account di archiviazione di Azure e possono essere quindi estratti, monitorati e analizzati. Di seguito viene illustrato uno scenario che sfrutta Funzioni di Azure, le app per la logica di Azure e i log di controllo dell'insieme di credenziali delle chiavi per creare una pipeline per l'invio di un messaggio di posta elettronica quando i segreti dell'insieme di credenziali vengono recuperati da un'applicazione che corrisponde all'ID app dell'app Web.

Sarà prima necessario abilitare la registrazione per l'insieme di credenziali delle chiavi. Questa operazione può essere eseguita tramite i comandi di PowerShell seguenti. Per i dettagli completi, vedere [qui](key-vault-logging.md):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Dopo aver abilitato la registrazione, i log di controllo inizieranno la raccolta di informazioni nell'account di archiviazione designato. Questi log conterranno eventi relativi alla modalità e al momento in cui si accede all'insieme di credenziali delle chiavi e a chi esegue l'accesso.

> [AZURE.NOTE] È possibile accedere alle informazioni di registrazione dopo un massimo di 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma nella maggior parte dei casi si potrà farlo prima.

Il passaggio successivo consiste nella [creazione di una coda del bus di servizio di Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Qui verranno inseriti i log di controllo dell'insieme di credenziali delle chiavi. Quando i log si trovano nella coda, l'app per la logica li seleziona ed esegue operazioni su di essi. Creare un bus di servizio è relativamente semplice e di seguito sono riportati i passaggi generali:

1. Creare uno spazio dei nomi del bus di servizio. Se è già presente uno spazio dei nomi da usare a questo scopo, andare direttamente al passaggio 2.
2. Passare al bus di servizio nel portale e selezionare lo spazio dei nomi nel quale creare la coda.
3. Scegliere Nuovo, scegliere Bus di servizio -> Coda e immettere i dettagli necessari.
4. Recuperare le informazioni di connessione del bus di servizio selezionando lo spazio dei nomi e facendo clic su _Informazioni di connessione_. Queste informazioni saranno necessarie per la parte successiva.

[Creare una funzione di Azure](../azure-functions/functions-create-first-azure-function.md) per eseguire il poll dei log dell'insieme di credenziali delle chiavi nell'account di archiviazione e selezionare i nuovi eventi. Questa funzione verrà attivata in base alla pianificazione.

Creare una funzione di Azure scegliendo Nuovo -> App per le funzioni nel portale. Durante la creazione è possibile usare un piano di hosting esistente o crearne uno nuovo. È anche possibile optare per l'hosting dinamico. Altre informazioni sulle opzioni di hosting delle funzioni sono disponibili [qui](../azure-functions/functions-scale.md).

Dopo aver creato la funzione di Azure, passare alla funzione e scegliere una funzione timer C#, quindi fare clic su **Crea** nella schermata iniziale.

![Pannello iniziale di Funzioni di Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Nella scheda _Sviluppo_ sostituire il codice run.csx con il seguente:

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
> [AZURE.NOTE] Sostituire le variabili nel codice precedente in modo che puntino all'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, al bus di servizio creato in precedenza e al percorso specifico dei log di archiviazione dell'insieme di credenziali delle chiavi.

La funzione seleziona il file del log più recente dall'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, acquisisce gli eventi più recenti dal file e li inserisce in una coda del bus di servizio. Un singolo file può avere più eventi, ad esempio nell'arco di un'ora, quindi viene creato un file _sync.txt_ che la funzione esamina per determinare il timestamp dell'ultimo evento selezionato. In questo modo, lo stesso evento non verrà inserito più volte. Questo file _sync.txt_ contiene semplicemente un timestamp per l'ultimo evento rilevato. I log, quando caricati, devono essere ordinati in base al timestamp per far sì che vengano ordinati correttamente.

Per questa funzione si fa riferimento a un paio di altre librerie non disponibili per impostazione predefinita in Funzioni di Azure. Per includerle è necessario che Funzioni di Azure le recuperi tramite NuGet. Scegliere l'opzione _Visualizza file_

![Opzione Visualizza file](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

e aggiungere un nuovo file denominato _project.json_ con il contenuto seguente:

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
Dopo aver fatto clic su _Salva_, Funzioni di Azure scaricherà i file binari necessari.

Passare alla scheda **Integra** e assegnare al parametro timer un nome significativo da usare all'interno della funzione. Nel codice precedente è previsto che il timer si chiami _myTimer_. Specificare un'[espressione CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) come segue: 0 * * * * * per il timer che attiverà l'esecuzione della funzione una volta al minuto.

Nella stessa scheda **Integra** aggiungere un input che sarà di tipo _Archivio BLOB di Azure_. Punterà al file _sync.txt_ che contiene il timestamp dell'ultimo evento esaminato dalla funzione. L'input sarà reso disponibile all'interno della funzione dal nome del parametro. Nel codice precedente, l'input dell'archivio BLOB di Azure prevede che il nome del parametro sia _inputBlob_. Scegliere l'account di archiviazione in cui risiederà il file _sync.txt_. Può essere lo stesso account di archiviazione o un account diverso. Nel campo del percorso specificare il percorso in cui si trova il file, nel formato {nome-contenitore}/path/to/sync.txt.

Aggiungere un output che sarà di tipo _Archivio BLOB di Azure_. Anche questo punterà al file _sync.txt_ appena definito nell'input. Verrà usato dalla funzione per scrivere il timestamp dell'ultimo evento esaminato. Il codice precedente prevede che questo parametro sia denominato _outputBlob_.

A questo punto la funzione è pronta. Tornare alla scheda **Sviluppo** e _salvare_ il codice. Verificare se nella finestra di output sono presenti errori di compilazione ed eventualmente correggerli. Dopo la compilazione, il codice sarà in esecuzione e verificherà i log dell'insieme di credenziali delle chiavi ogni minuto, inserendo eventuali nuovi eventi nella coda del bus di servizio definito. Le informazioni di registrazione verranno scritte nella finestra del log ogni volta che la funzione viene attivata.

###App per la logica di Azure

Sarà quindi necessario creare un'app per la logica di Azure che selezionerà gli eventi che la funzione inserisce nella coda del bus di servizio, ne analizzerà il contenuto e invierà un messaggio di posta elettronica in base alla soddisfazione di una condizione.

[Creare un app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) passando a Nuovo -> App per la logica.

Dopo averla creata, passare all'app per la logica e scegliere _modifica_. Nell'editor di app per la logica scegliere l'API gestita _Coda del bus di servizio_ e immettere le credenziali del bus di servizio per connetterlo alla coda.

![Bus di servizio dell'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Scegliere quindi _Aggiungi una condizione_. Nella condizione, passare all'_editor avanzato_ e immettere il codice seguente, sostituendo l'APP\_ID con l'effettivo APP\_ID dell'app Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Questa espressione restituirà essenzialmente **false** se la proprietà **appid** dell'evento in ingresso, ovvero il corpo del messaggio del bus di servizio, non è l'**appid** dell'app.

Creare ora un'azione nell'opzione _Se no, non fare nulla_.

![Scelta dell'azione per l'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Per l'azione, scegliere _Office 365 - send email_. Compilare i campi per creare un messaggio di posta elettronica da inviare quando la condizione specificata restituisce false. Se non si ha Office 365 è possibile valutare alternative per ottenere lo stesso risultato.

A questo punto è disponibile una pipeline end-to-end che, una volta al minuto, cercherà nuovi log di controllo dell'insieme di credenziali delle chiavi. I nuovi log trovati verranno inseriti in una coda del bus di servizio. L'app per la logica verrà attivata non appena arriva un nuovo messaggio nella coda e se l'appid all'interno dell'evento non corrisponde all'id app dell'applicazione chiamante verrà inviato un messaggio di posta elettronica.

<!---HONumber=AcomDC_0928_2016-->