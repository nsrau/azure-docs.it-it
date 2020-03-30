---
title: Impossibile accedere ai file di archiviazione di Data Lake in Azure HDInsightUnable to access Data Lake storage files in Azure HDInsight
description: Impossibile accedere ai file di archiviazione di Data Lake in Azure HDInsightUnable to access Data Lake storage files in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894157"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Impossibile accedere ai file di archiviazione di Data Lake in Azure HDInsightUnable to access Data Lake storage files in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue-acl-verification-failed"></a>Problema: verifica ACL non riuscita

Viene visualizzato un messaggio di errore simile al:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Causa

L'utente potrebbe aver revocato le autorizzazioni dell'entità servizio (SP) per file e cartelle.

### <a name="resolution"></a>Risoluzione

1. Verificare che il SP disponga delle autorizzazioni 'x' da attraversare lungo il percorso. Per altre informazioni, vedere [Autorizzazioni](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Esempio di comando dfs per controllare l'accesso a file/cartelle nell'account di archiviazione di Data Lake:Sample dfs command to check access to files/folders in Data Lake storage account:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Impostare le autorizzazioni necessarie per accedere al percorso in base all'operazione di lettura/scrittura eseguita. Vedere qui per le autorizzazioni necessarie per le varie operazioni del file system.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problema: scadenza del certificato dell'entità servizioIssue: Service principal certificate expiry

Viene visualizzato un messaggio di errore simile al:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Causa

Il certificato fornito per l'accesso all'entità servizio potrebbe essere scaduto.

1. SSH in nodo frontale. Controllare l'accesso all'account di archiviazione usando il comando dfs seguente:Check access to storage account using following dfs command:

    ```
    hdfs dfs -ls /
    ```

1. Verificare che il messaggio di errore sia simile al seguente:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Ottenere uno degli URL `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`da .

1. Eseguire il comando curl seguente per recuperare il token OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. L'output per un'entità servizio valida deve essere simile al aspetto che è:The output for a valid service principal should be something like:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Se il certificato dell'entità servizio è scaduto, l'output sarà simile al seguente:If the service principal certificate has expired, the output will look something like this:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Qualsiasi altro errore correlato ad Azure Active Directory/certificato può essere riconosciuto eseguendo il ping dell'URL del gateway per ottenere il token OAuth.

1. Se viene visualizzato il seguente errore quando si tenta di accedere ad ADLS dal Cluster HDI. Verificare se il certificato è scaduto seguendo la procedura descritta in precedenza.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Risoluzione

Creare un nuovo certificato o assegnare un certificato esistente usando lo script di PowerShell seguente:Create a new Certificate or assign existing Certificate using the following PowerShell script:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Per assegnare un certificato esistente, creare un certificato, avere il file con estensione pfx e la password pronti. Associare il certificato all'entità servizio con cui è stato creato il cluster e preparare l'AppId.

Eseguire il comando PowerShell dopo aver sostituito i parametri con i valori effettivi.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
