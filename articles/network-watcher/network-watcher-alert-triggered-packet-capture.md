---
title: Usare l&quot;acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Funzioni di Azure | Microsoft Docs
description: Questo articolo descrive come creare un&quot;acquisizione di pacchetti attivata da un avviso con Azure Network Watcher
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Usare l'acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Funzioni di Azure

Il servizio di acquisizione di pacchetti di Network Watcher crea sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Il file di acquisizione può avere un filtro che viene definito per tenere traccia solo del traffico che si vuole monitorare. Questi dati vengono quindi archiviati in un BLOB di archiviazione o in locale nel computer guest. Questa funzionalità può essere avviata in remoto da altri scenari di automazione, ad esempio Funzioni di Azure. L'acquisizione di pacchetti consente di eseguire acquisizioni proattive in base alle anomalie di rete definite. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora.

Le risorse distribuite in Azure sono in esecuzione 24 ore su 24, 7 giorni su 7. Nessuno può monitorare attivamente lo stato di tutte le risorse 24 ore su 24, 7 giorni su 7. Si pensi a che cosa può accadere se si verifica un problema alle 2 di mattina.

Usando Network Watcher, Creazione di avvisi e Funzioni dall'ecosistema di Azure, è possibile rispondere in modo proattivo alle problematiche della rete con i dati e gli strumenti per risolvere il problema.

## <a name="before-you-begin"></a>Prima di iniziare

In questo esempio la VM invia più segmenti TCP del solito ed è possibile essere avvisati. Come esempio vengono usati i segmenti TCP, ma è possibile usare qualsiasi condizione di avviso. Quando si viene avvisati, è opportuno avere i dati a livello di pacchetto per sapere perché la comunicazione è aumentata e poter eseguire le procedure che consentono di ripristinare la normale comunicazione del computer.
Questo scenario presuppone che esista già un'istanza di Network Watcher e un gruppo di risorse con una macchina virtuale valida da usare.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scenario

Per automatizzare questo processo, nella VM vengono creati e connessi un avviso da attivare quando si verifica l'evento imprevisto e una funzione di Azure per chiamare Network Watcher.

Questo scenario:

* Crea una funzione di Azure che avvia un'acquisizione di pacchetti.
* Crea una regola di avviso in una macchina virtuale.
* Configura la regola di avviso per chiamare la funzione di Azure.

## <a name="creating-an-azure-function-and-overview"></a>Creazione di una funzione di Azure e panoramica

Il primo passaggio è la creazione di una funzione di Azure per elaborare l'avviso e creare un'acquisizione di pacchetti. 

L'elenco seguente è una panoramica del flusso di lavoro effettivo.

1. Nella macchina virtuale viene attivato un avviso.
1. L'avviso chiama la funzione di Azure tramite un webhook.
1. La funzione di Azure elabora l'avviso e avvia una sessione di acquisizione di pacchetti di Network Watcher.
1. L'acquisizione di pacchetti viene eseguita nella macchina virtuale e raccoglie il traffico. 
1. Il file di acquisizione viene caricato in un account di archiviazione per la revisione e diagnosi. 

È possibile creare una funzione di Azure nel portale seguendo le istruzioni contenute in [Creare la prima funzione di Azure](../azure-functions/functions-create-first-azure-function.md). Per questo esempio, viene scelta una funzione di tipo HttpTrigger-PowerShell. Per questo esempio sono richieste alcune personalizzazioni illustrate nei passaggi seguenti:

![esempio di funzioni][functions1]

> [!NOTE]
> Il modello di PowerShell è sperimentale e non dispone del supporto completo.

## <a name="adding-modules"></a>Aggiunta di moduli

Per usare i cmdlet PowerShell di Network Watcher, il modulo di PowerShell più recente deve essere caricato nell'app per le funzioni.

1. Nel computer locale con i moduli di Azure PowerShell più recenti installati, eseguire il seguente comando PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Ciò consente di visualizzare il percorso locale dei moduli di Azure PowerShell. Queste cartelle verranno usate in un passaggio successivo. I moduli usati in questo scenario sono:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![cartelle di PowerShell][functions5]

1. Passare a **Impostazioni dell'app per le funzioni** > **Passa all'editor del servizio app**.

    ![kudu delle funzioni][functions2]

1. Fare clic con il pulsante destro del mouse sulla cartella AlertPacketCapturePowershell e creare una cartella denominata **azuremodules**. Continuare a creare sottocartelle per ogni modulo necessario.

    ![kudu delle funzioni][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Fare clic con il pulsante destro del mouse sulla sottocartella **AzureRM.Network** e scegliere **Carica file**. Passare alla cartella in cui sono installati i moduli di Azure e nella cartella AzureRM.Network locale selezionare tutti i file presenti e fare clic su **OK**.  Ripetere questi passaggi per AzureRM.Profile e AzureRM.Resources.

    ![caricamento dei file][functions6]

1. Al termine, ogni cartella dovrebbe includere i file dei moduli di PowerShell dal computer locale.

    ![file di PowerShell][functions7]

## <a name="authentication"></a>Autenticazione

Per usare i cmdlet PowerShell, è necessario eseguire l'autenticazione, che deve essere configurata nell'app per le funzioni. A tale scopo, vengono configurate le variabili di ambiente e un file di chiave crittografata deve essere caricato nell'app per le funzioni.

> [!note]
> Questo scenario fornisce solo un esempio di come implementare l'autenticazione con Funzioni di Azure; questa operazione può essere eseguita anche in altri modi.

### <a name="encrypted-credentials"></a>Credenziali crittografate

Il seguente script di PowerShell crea un file di chiave denominato **PassEncryptKey.key** e fornisce una versione crittografata della password fornita.  Questa password è la stessa che viene definita per l'applicazione di Azure AD usata per l'autenticazione.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Nell'editor del servizio app dell'app per le funzioni creare una cartella denominata **keys** in **AlertPacketCapturePowerShell** e caricare il file **PassEncryptKey.key** creato nell'esempio precedente di PowerShell.

![chiavi delle funzioni][functions8]

### <a name="retrieving-values-for-environment-variables"></a>Recupero dei valori per le variabili di ambiente

La configurazione finale richiesta consente di configurare le variabili di ambiente necessarie per accedere ai valori per l'autenticazione. Di seguito è riportato un elenco delle variabili di ambiente che vengono create.

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

L'ID client è l'ID applicazione di un'applicazione in Azure Active Directory.

1. Se non si dispone già di un'applicazione da usare, eseguire l'esempio seguente per crearne una.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > La password usata per la creazione dell'applicazione deve essere la stessa creata in precedenza durante il salvataggio del file di chiave.

1. Nel portale di Azure passare a **Sottoscrizioni** > scegliere la sottoscrizione da usare > **Controllo di accesso (IAM)**.

    ![IAM delle funzioni][functions9]

1. Scegliere l'account da usare e fare clic su Proprietà. Copiare l'ID applicazione.

    ![ID applicazione delle funzioni][functions10]

#### <a name="azuretenant"></a>AzureTenant

L'ID tenant viene ottenuto eseguendo il seguente esempio di PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Il valore della variabile di ambiente AzureCredPassword è il valore ottenuto eseguendo il seguente esempio di PowerShell. Si tratta dello stesso esempio riportato nella sezione precedente **Credenziali crittografate**. Il valore richiesto è l'output della variabile `$Encryptedpassword`.  Si tratta della password dell'entità servizio crittografata tramite lo script di PowerShell.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>Archiviazione delle variabili di ambiente

1. Passare all'app per le funzioni, fare clic su **Impostazioni dell'app per le funzioni** > **Configura le impostazioni dell'app**.

    ![configurazione delle impostazioni dell'app][functions11]

1. Aggiungere le variabili di ambiente e i relativi valori alle impostazioni dell'app e fare clic su **Salva**.

    ![app settings][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Elaborazione dell'avviso e avvio di una sessione di acquisizione di pacchetti

A questo punto è necessario chiamare Network Watcher dalla funzione di Azure. L'implementazione di questa funzione è diversa a seconda dei requisiti. Il flusso generale del codice è tuttavia il seguente:

1. Elaborare i parametri di input
2. Effettuare una query delle acquisizioni di pacchetti esistenti, verificare i limiti e risolvere i conflitti di nomi
3. Creare un'acquisizione di pacchetti con i parametri appropriati
4. Eseguire regolarmente il polling dell'acquisizione di pacchetti fino al completamento
5. Notificare all'utente che la sessione di acquisizione di pacchetti è completa

L'esempio seguente è codice PowerShell che può essere usato nella funzione di Azure. È necessario sostituire i valori per subscriptionId, resourceGroupName e storageAccountName.

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

Dopo avere creato la funzione, è necessario configurare l'avviso per chiamare l'URL associato alla funzione. Per ottenere questo valore, fare clic su **</> Get function URL** (Ottenere l'URL della funzione). 

![ricerca dell'URL della funzione 1][functions13]

Copiare l'URL della funzione per l'app per le funzioni.

![ricerca dell'URL della funzione 2][2]

Se sono necessarie proprietà personalizzate nel payload della richiesta POST del webhook, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurare un avviso in una VM

Si possono configurare avvisi per notificare alle singole persone quando una metrica specifica supera una soglia assegnata. In questo esempio l'avviso è sui segmenti TCP inviati, ma può essere attivato per molte altre metriche. In questo esempio viene configurato un avviso per chiamare un webhook per chiamare la funzione.

### <a name="create-the-alert-rule"></a>Creare la regola di avviso

Passare a una macchina virtuale esistente e aggiungere una regola di avviso. Per informazioni più dettagliate sulla configurazione di avvisi, vedere [Usare il portale di Azure per creare avvisi per i servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![Aggiungere una regola di avviso di macchina virtuale a una macchina virtuale][1]

> [!NOTE]
> Alcune metriche non sono abilitate per impostazione predefinita. Per altre informazioni su come abilitare metriche aggiuntive, vedere [Abilitare il monitoraggio e la diagnostica](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

Incollare infine l'URL del passaggio precedente nella casella di testo del webhook nell'avviso. Fare clic su **OK** per salvare la regola di avviso.

![Incollare l'URL nella regola di avviso][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Downloading e visualizzazione del file di acquisizione

Se si salva l'acquisizione in un account di archiviazione, il file di acquisizione può essere scaricato tramite il portale o a livello di codice. Se il file di acquisizione è archiviato in locale, viene recuperato accedendo alla macchina virtuale. 

Per istruzioni sul download di file dall'account di archiviazione di Azure, consultare [Introduzione all'archivio BLOB di Azure con .NET](../storage/storage-dotnet-how-to-use-blobs.md). Un altro strumento che può essere usato è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

Dopo il download dell'acquisizione, è possibile visualizzarla usando qualsiasi strumento per la lettura di un file **CAP**. I seguenti sono collegamenti a due di questi strumenti:

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare le acquisizioni di pacchetti, vedere [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md) (Analisi delle acquisizioni di pacchetti con Wireshark)

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
