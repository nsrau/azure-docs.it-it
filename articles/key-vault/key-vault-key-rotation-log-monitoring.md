---
title: Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi end-to-end | Documentazione Microsoft
description: Usare questa Guida dettagliata per configurare la rotazione della chiave e monitorare i log dell'insieme di credenziali chiave.
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 785e60ddf54a3772ae7687b9d18477ef04707609
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713686"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurare l'insieme di credenziali delle chiavi di Azure con rotazione e controllo delle chiavi

## <a name="introduction"></a>Introduzione

Dopo aver creato un insieme di credenziali delle chiavi, è possibile iniziare a usarle per archiviare le chiavi e i segreti. Le applicazioni non devono più rendere persistenti le chiavi o i segreti, ma li richiederanno all'insieme di credenziali delle chiavi in base alle esigenze. Un insieme di credenziali delle chiavi consente di aggiornare le chiavi e segreti senza influenzare il comportamento dell'applicazione, si apre così un ampio ventaglio di possibilità per la gestione dei segreti e chiavi.

>[!IMPORTANT]
> Gli esempi inclusi in questo articolo vengono forniti solo a scopo illustrativo. Non è previsti per la produzione. 

Questo articolo illustra:

- Un esempio dell'uso dell'insieme di credenziali delle chiavi di Azure per archiviare un segreto. In questo articolo, il segreto archiviato è la chiave di account di archiviazione di Azure a cui accede un'applicazione. 
- Come implementare una rotazione pianificata della chiave dell'account di archiviazione.
- Come monitorare la chiave dell'insieme di credenziali dei log di controllo e generare avvisi quando vengono effettuate richieste impreviste.

> [!NOTE]
> Questo articolo non illustra nei dettagli la configurazione iniziale dell'insieme di credenziali delle chiavi. Per queste informazioni, vedere [Cos'è Azure Key Vault?](key-vault-overview.md). Per istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [gestire Key Vault tramite la CLI di Azure](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Configurare l'insieme di credenziali delle chiavi

Per consentire a un'applicazione di recuperare un segreto dall'insieme di credenziali delle chiavi, è prima necessario creare il segreto e caricarlo nell'insieme di credenziali.

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

```powershell
Connect-AzAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password per l'account di Azure. PowerShell recupera tutte le sottoscrizioni associate a questo account e usa la prima per impostazione predefinita.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario specificare quella usata per creare l'insieme di credenziali delle chiavi. Immettere il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzSubscription
```

Per specificare la sottoscrizione che ha associato l'insieme di credenziali di che accesso, immettere:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Dato che questo articolo illustra l'archiviazione di una chiave dell'account di archiviazione come segreto, è necessario ottenere la chiave dell'account di archiviazione.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Dopo aver recuperato il segreto (in questo caso, la chiave dell'account), è necessario convertire tale chiave in una stringa sicura e quindi creare un segreto con quel valore nell'insieme di credenziali delle chiavi.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Ottenere quindi l'URI per il segreto creato. È necessario questo URI in un passaggio successivo per chiamare l'insieme di credenziali delle chiavi e recuperare il segreto. Eseguire il comando PowerShell seguente e prendere nota del valore ID, ovvero URI del segreto:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurare l'applicazione

Ora che avete un segreto archiviato, è possibile usare codice per recuperare e utilizzarlo dopo l'esecuzione di pochi altri passaggi.

In primo luogo, è necessario registrare l'applicazione con Azure Active Directory. Quindi indicare a Key Vault le informazioni sull'applicazione in modo da consentire richieste dall'applicazione.

> [!NOTE]
> L'applicazione deve essere creata nello stesso tenant di Azure Active Directory dell'insieme di credenziali delle chiavi.

1. Aprire **Azure Active Directory**.
2. Selezionare **Registrazioni per l'app**. 
3. Selezionare **registrazione nuova applicazione** per aggiungere un'applicazione ad Azure Active Directory.

    ![Aprire applicazioni in Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Sotto **Create**, lasciare il tipo di applicazione **app Web / API** e assegnare un nome all'applicazione. Assegnare all'applicazione un **Sign-on di URL**. Questo URL può essere qualsiasi valore per questa demo.

    ![Creare una registrazione dell'applicazione](./media/keyvault-keyrotation/create-app.png)

5. Dopo aver aggiunto l'applicazione ad Azure Active Directory, viene visualizzata la pagina dell'applicazione. Selezionare **le impostazioni**, quindi selezionare **proprietà**. Copiare il valore **ID dell'applicazione**. È necessario nei passaggi successivi.

Successivamente, generare una chiave per l'applicazione in modo che possa interagire con Azure Active Directory. Per creare una chiave, selezionare **tasti** sotto **impostazioni**. Prendere nota della chiave appena generata per l'applicazione di Azure Active Directory. Saranno necessarie in un passaggio successivo. La chiave non sarà disponibile dopo aver chiuso questa sezione. 

![Chiavi dell'app Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Prima di definire tutte le chiamate dall'applicazione in key vault, è necessario indicare l'insieme di credenziali delle chiavi sull'applicazione e le relative autorizzazioni. Il comando seguente usa il nome dell'insieme di credenziali e l'ID dell'applicazione dall'app Azure Active Directory per concedere all'applicazione **ottenere** accesso all'insieme di credenziali chiave.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

A questo punto si è pronti iniziare a creare l'applicazione chiama. Nell'applicazione, è necessario installare i pacchetti NuGet necessari per interagire con Azure Key Vault e Azure Active Directory. Immettere i comandi seguenti nella console di Gestione pacchetti di Visual Studio. Al momento della stesura di questo articolo, la versione corrente del pacchetto di Azure Active Directory è 3.10.305231913, quindi verificare la versione più recente e aggiornare in base alle esigenze.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Nel codice dell'applicazione creare una classe che contenga il metodo per l'autenticazione di Azure Active Directory. In questo esempio, la classe è denominata **Utils**. Aggiungere l'istruzione `using` seguente:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Aggiungere quindi il metodo seguente per recuperare il token JWT da Azure Active Directory. Per manutenibilità insufficiente, si potrebbe voler spostare i valori di stringa hardcoded nella configurazione web o applicazione.

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

Aggiungere il codice necessario per chiamare l'insieme di credenziali delle chiavi e recuperare il valore del segreto. In primo luogo, è necessario aggiungere quanto segue `using` istruzione:

```csharp
using Microsoft.Azure.KeyVault;
```

Aggiungere le chiamate di metodo per richiamare l'insieme di credenziali delle chiavi e recuperare il segreto. In questo metodo viene specificato l'URI del segreto salvato in un passaggio precedente. Si noti l'uso del **GetToken** metodo dalle **Utils** classe creata in precedenza.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando si esegue l'applicazione, verrà eseguita l'autenticazione in Azure Active Directory e quindi verrà recuperato il valore del segreto dall'insieme di credenziali delle chiavi di Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotazione delle chiavi con Automazione di Azure

> [!IMPORTANT]
> Runbook di automazione di Azure richiedono comunque l'uso del `AzureRM` modulo.

A questo punto si è pronti configurare una strategia di rotazione per i valori memorizzati come segreti di Key Vault. I segreti possono essere ruotati in diversi modi:

- Come parte di un processo manuale
- A livello di programmazione tramite le chiamate API
- Tramite uno script di automazione di Azure

Ai fini di questo articolo, si userà combinato con automazione di Azure PowerShell per modificare la chiave di accesso di un account di archiviazione di Azure. È possibile aggiornare un segreto dell'insieme di credenziali chiave con la nuova chiave.

Per consentire ad automazione di Azure impostare i valori dei segreti nell'insieme di credenziali delle chiavi, è necessario ottenere l'ID client per la connessione denominata **AzureRunAsConnection**. Questa connessione è stata creata al momento della definizione di istanza di automazione di Azure. Per trovare questo ID, selezionare **asset** dall'istanza di automazione di Azure. Da qui, selezionare **connessioni**, quindi selezionare la **AzureRunAsConnection** entità servizio. Assicurarsi di annotare il **ApplicationId** valore.

![ID client di Automazione di Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Nelle **Assets**, selezionare **moduli**. Selezionare **raccolta**e quindi cercare e importare le versioni aggiornate di ognuno dei moduli seguenti:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Al momento della stesura di questo articolo è necessario aggiornare solo i moduli indicati in precedenza per lo script seguente. Se nel processo di automazione non riesce, verificare di aver importato tutti i moduli necessari e le relative dipendenze.

Dopo aver recuperato l'ID dell'applicazione per la connessione di automazione di Azure, è necessario indicare l'insieme di credenziali delle chiavi che questa applicazione è autorizzata ad aggiornare i segreti nell'insieme di credenziali. Usare il comando PowerShell seguente:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Successivamente, selezionare **runbook** sotto l'istanza di automazione di Azure e quindi selezionare **Aggiungi Runbook**. Selezionare **Creazione rapida**. Denominare il runbook e selezionare **PowerShell** come tipo di runbook. È possibile aggiungere una descrizione. Infine, selezionare **Crea**.

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

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Nel riquadro dell'editor, selezionare **riquadro di Test** per testare lo script. Dopo lo script viene eseguito senza errori, è possibile selezionare **pubblica**, ed è quindi possibile applicare una pianificazione per il runbook nel riquadro di configurazione del runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline di controllo dell'insieme di credenziali delle chiavi

Quando si configura un insieme di credenziali delle chiavi è possibile attivare il controllo per raccogliere log relativi alle richieste di accesso all'insieme di credenziali delle chiavi. Questi log vengono archiviati in un account di archiviazione di Azure designato e possono essere estratti, monitorati e analizzati. Lo scenario seguente Usa funzioni di Azure, App per la logica di Azure e i log di controllo di key vault per creare una pipeline che invia un messaggio di posta elettronica quando un'app che non corrisponde all'ID app dell'app web consente di recuperare i segreti dall'insieme di credenziali.

È prima di tutto necessario abilitare la registrazione per l'insieme di credenziali delle chiavi. Usare i comandi PowerShell seguenti. (È possibile visualizzare i dettagli completi nel [l'articolo relativo al key-vault-logging](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dopo la registrazione è abilitata, i log di controllo Avvia vengono archiviati nell'account di archiviazione designato. Questi log contengono eventi relativi alla modalità di accesso all'insieme di credenziali delle chiavi, a quando avviene l'accesso e a chi lo esegue.

> [!NOTE]
> È possibile accedere alle informazioni di registrazione dopo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, Spesso sarà disponibile più rapidamente rispetto a quello.

Il passaggio successivo consiste nella [creazione di una coda del bus di servizio di Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Questa coda è in cui vengono effettuato il push dei log di controllo di key vault. Quando i messaggi di log di controllo sono nella coda, l'app per la logica li preleva e interviene su di essi. Creare un'istanza del Bus di servizio con i passaggi seguenti:

1. Creare uno spazio dei nomi del Bus di servizio (se hai già uno che si desidera usare, andare al passaggio 2).
2. Passare all'istanza del Bus di servizio nel portale di Azure e selezionare lo spazio dei nomi che si desidera creare la coda.
3. Selezionare **crea una risorsa** > **Enterprise Integration** > **del Bus di servizio**, quindi immettere i dettagli richiesti.
4. Trovare le informazioni di connessione del Bus di servizio selezionando lo spazio dei nomi e quindi selezionando **le informazioni di connessione**. Queste informazioni sarà necessario nella sezione successiva.

Successivamente [creare una funzione di Azure](../azure-functions/functions-create-first-azure-function.md) per il polling dei log dell'insieme di credenziali delle chiavi all'interno dell'account di archiviazione e selezionare i nuovi eventi. Questa funzione verrà attivata una pianificazione.

Per creare un'app per le funzioni di Azure, selezionare **crea una risorsa**, cercare nel marketplace **App per le funzioni**, quindi selezionare **Create**. Durante la creazione è possibile usare un piano di hosting esistente o crearne uno nuovo. È inoltre possibile scegliere per l'hosting dinamico. Per altre informazioni sulle opzioni di hosting per funzioni di Azure, vedere [come ridimensionare funzioni di Azure](../azure-functions/functions-scale.md).

Dopo aver creato l'app per le funzioni di Azure, passare ad esso e selezionare il **Timer** scenario e **C\#**  per la lingua. Quindi selezionare **consente di creare questa funzione**.

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
> Modificare le variabili nel codice precedente in modo che punti all'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali chiave, all'istanza del Bus di servizio creato in precedenza e il percorso specifico dei log di archiviazione key vault.

La funzione seleziona il file del log più recente dall'account di archiviazione in cui vengono scritti i log dell'insieme di credenziali delle chiavi, acquisisce gli eventi più recenti dal file e ne effettua il push in una coda del bus di servizio. 

Poiché un singolo file può avere più eventi, è necessario creare un file sync la funzione esamina per determinare il timestamp dell'ultimo evento selezionato. Uso di questo file assicura che si non esegue il push lo stesso evento più volte. 

Il file sync. txt contiene un timestamp per l'evento rilevato last. Quando vengono caricati i log, è necessario ordinati in base i timestamp per garantire che si sta ordinate correttamente.

Per questa funzione è fare riferimento a due raccolte aggiuntive che non sono disponibili per impostazione predefinita in funzioni di Azure. Per includere queste librerie, abbiamo bisogno di funzioni di Azure le recuperi tramite NuGet. Sotto il **codice** , quindi selezionare **Visualizza file**.

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

Dopo aver selezionato **salvare**, funzioni di Azure scaricherà i file binari necessari.

Passare alla scheda **Integra** e assegnare al parametro timer un nome significativo da usare all'interno della funzione. Nel codice precedente, la funzione prevede che il timer deve essere chiamato *myTimer*. Specificare una [espressione CRON](../app-service/webjobs-create.md#CreateScheduledCRON) relativo al timer come indicato di seguito: `0 * * * * *`. Questa espressione determinerà la funzione eseguire una volta al minuto.

Nella stessa **integra** scheda, aggiungere un input di tipo **archivio Blob di Azure**. Questo input punterà al file sync. txt che contiene il timestamp dell'ultimo evento esaminato dalla funzione. Questo input si accederà all'interno della funzione usando il nome del parametro. Nel codice precedente, l'input di archiviazione Blob di Azure prevede che il nome del parametro sia *inputBlob*. Selezionare l'account di archiviazione in cui sarà possibile individuare il file sync. txt (potrebbe essere lo stesso o un account di archiviazione diverso). Nel campo del percorso specificare il percorso del file nel formato `{container-name}/path/to/sync.txt`.

Aggiungere un output di tipo **archiviazione Blob di Azure**. Questo output punterà al file sync txt che è definito nell'input. Questo output viene utilizzato dalla funzione per scrivere il timestamp dell'ultimo evento esaminato. Il codice precedente prevede che questo parametro sia denominato *outputBlob*.

La funzione è pronta. Assicurarsi di tornare alla scheda **Sviluppo** e salvare il codice. Controllare la finestra di output per individuare eventuali errori di compilazione e correggerle in base alle esigenze. Se il codice viene compilato, quindi il codice dovrebbe ora essere controllando i registri di insieme di credenziali delle chiavi ogni minuto e il push di eventuali nuovi eventi nella coda del Bus di servizio definito. Le informazioni di registrazione verranno scritte nella finestra del log ogni volta che la funzione viene attivata.

### <a name="azure-logic-app"></a>App per la logica di Azure

Successivamente, è necessario creare un'app per la logica di Azure che preleva gli eventi che la funzione effettua il push nella coda del Bus di servizio, analizza il contenuto e invia un messaggio di posta elettronica in base a una condizione viene cercata la corrispondenza.

[Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) selezionando **crea una risorsa** > **integrazione** > **App per la logica**.

Dopo aver creato l'app per la logica, passare ad esso e selezionare **modifica**. Nell'editor delle app per la logica, selezionare **coda del Bus di servizio** e immettere le credenziali del Bus di servizio per connetterlo alla coda.

![Bus di servizio dell'app per la logica di Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selezionare **Aggiungi una condizione**. Nella condizione passare all'editor avanzato e immettere il codice seguente. Sostituire *APP_ID* con l'ID effettivo dell'app dell'app web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Questa espressione restituisce essenzialmente **false** se il *appid* dell'evento in ingresso, ovvero il corpo del messaggio del Bus di servizio, non è la *appid* dell'app.

A questo punto, creare un'azione nel **se NO, non fare nulla**.

![Le App per la logica di Azure scegliere azione](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Per l'azione, selezionare **Office 365 - send email**. Compilare i campi per creare un messaggio di posta elettronica da inviare quando la condizione specificata restituisce **false**. Se non si dispone di Office 365, cercare alternative per ottenere gli stessi risultati.

È ora disponibile una pipeline end-to-end che esegue la ricerca di nuovi log di key vault controllo una volta al minuto. Effettua il push di nuovi log trovati in una coda del Bus di servizio. L'app per la logica viene attivata quando arriva un nuovo messaggio nella coda. Se il *appid* all'interno dell'evento non corrisponde all'ID app dell'applicazione chiamante, invia un messaggio di posta elettronica.
