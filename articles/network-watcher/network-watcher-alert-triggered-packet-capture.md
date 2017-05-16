---
title: Utilizzare l&quot;acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Avvisi e Funzioni di Azure | Microsoft Docs
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
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5fd017b6f7645220ee7572e50c02265de41e938c
ms.lasthandoff: 04/27/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Utilizzare l'acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Avvisi e Funzioni di Azure

Il servizio di acquisizione di pacchetti di Network Watcher crea sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Il file di acquisizione può avere un filtro che viene definito per tenere traccia solo del traffico che si vuole monitorare. Questi dati vengono quindi archiviati in un BLOB di archiviazione o in locale nel computer guest.

Questa funzionalità può essere avviata in remoto da altri scenari di automazione, ad esempio Funzioni di Azure. L'acquisizione di pacchetti consente di eseguire acquisizioni proattive in base alle anomalie di rete definite. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora.

Le risorse distribuite in Azure sono in esecuzione 24 ore su 24, 7 giorni su 7. Nessuno può monitorare attivamente lo stato di tutte le risorse 24 ore su 24, 7 giorni su 7. Si pensi a che cosa può accadere se si verifica un problema alle 2 di mattina.

Usando Network Watcher, Creazione di avvisi e Funzioni dall'ecosistema di Azure, è possibile rispondere in modo proattivo alle problematiche della rete con i dati e gli strumenti per risolvere il problema.

![scenario][scenario]

## <a name="prerequisites"></a>Prerequisiti

* Installare la versione più recente di [Azure PowerShell](/powershell/azure/install-azurerm-ps)
* Avere un’istanza preesistente di Network Watcher o [creare un’istanza di Network Watcher](network-watcher-create.md)
* Avere una macchina virtuale preesistente nella stessa regione del precedente Network Watcher con [estensione Windows](../virtual-machines/windows/extensions-nwa.md) o [estensione della macchina virtuale Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenario

In questo esempio la VM invia più segmenti TCP del solito ed è possibile essere avvisati. Come esempio qui vengono usati i segmenti TCP, ma è possibile usare qualsiasi condizione di avviso.

Una volta ricevuto l’avviso, sono necessari i dati a livello di pacchetto per comprendere il motivo per cui la comunicazione è aumentata. In questo modo, è possibile intervenire per riportare la macchina in condizioni di normale comunicazione.

Questo scenario presuppone che esista già un'istanza di Network Watcher e un gruppo di risorse con una macchina virtuale valida da utilizzare.

In questo esempio la VM invia più segmenti TCP del solito ed è possibile essere avvisati. Come esempio vengono usati i segmenti TCP, ma è possibile usare qualsiasi condizione di avviso. Quando si viene avvisati, è opportuno avere i dati a livello di pacchetto per sapere perché la comunicazione è aumentata e poter eseguire le procedure che consentono di ripristinare la normale comunicazione del computer.

L'elenco seguente è una panoramica del flusso di lavoro effettivo:

1. Nella macchina virtuale viene attivato un avviso.
1. L'avviso chiama la funzione di Azure tramite un webhook.
1. La funzione di Azure elabora l'avviso e avvia una sessione di acquisizione di pacchetti di Network Watcher.
1. L'acquisizione di pacchetti viene eseguita nella macchina virtuale e raccoglie il traffico.
1. Il file di acquisizione pacchetto viene caricato in un account di archiviazione per revisione e diagnosi.

Per automatizzare questo processo, nella VM vengono creati e connessi un avviso da attivare quando si verifica l'evento imprevisto e una funzione per chiamare Network Watcher.

Questo scenario prevede le seguenti operazioni:

* Crea una funzione di Azure che avvia un'acquisizione di pacchetti.
* Crea una regola di avviso su una macchina virtuale e configura la regola di avviso in modo da chiamare la funzione di Azure.

## <a name="creating-an-azure-function"></a>Creazione di una funzione di Azure

Il primo passaggio è la creazione di una funzione di Azure per elaborare l'avviso e creare un'acquisizione di pacchetti.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Nuovo** > **Calcolo** > **App per le funzioni**

    ![creazione di un’app per le funzioni][1-1]

2. In **App per le funzioni**, inserire i seguenti valori e fare clic su **OK** per creare l’app per le funzioni:

    |**Impostazione** | **Valore** | **Dettagli** |
    |---|---|---|
    |**Nome app**|PacketCaptureExample|Nome dell’app per le funzioni|
    |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare l’app per le funzioni.||
    |**Gruppo di risorse**|PacketCaptureRG|Nome del gruppo di risorse che contiene l’app per le funzioni.|
    |**Piano di hosting**|Piano a consumo| Il tipo di piano che l’app per le funzioni utilizzerà. Le opzioni sono A consumo e Piano di servizio app. |
    |**Posizione**|Stati Uniti centrali| La regione dove creare l’app per le funzioni.|
    |**Storage Account**|{generato automaticamente}| Si tratta dell'account di archiviazione richiesto dalle funzioni di Azure per l'archiviazione generica.|

3. Nel pannello delle app per le funzioni **PacketCaptureExample**, fare clic su **+** in **Funzioni** > **Personalizza funzione**. Selezionare **HttpTrigger-Powershell**, quindi inserire le informazioni rimanenti e fare clic su **Crea** per creare la funzione.

    |**Impostazione** | **Valore** | **Dettagli** |
    |---|---|---|
    |**Scenario**|Sperimentale|Tipo di scenario|
    |**Dare un nome alla funzione**|AlertPacketCapturePowerShell|Nome della funzione|
    |**Livello di autorizzazione**|Funzione|Livello di autorizzazione per la funzione.|

![esempio di funzioni][functions1]

> [!NOTE]
> Il modello di PowerShell è sperimentale e non dispone del supporto completo.

Per questo esempio sono richieste alcune personalizzazioni illustrate nei passaggi seguenti:

### <a name="adding-modules"></a>Aggiunta di moduli

Per usare i cmdlet PowerShell di Network Watcher, è necessario caricare il modulo PowerShell più recente nell'app per le funzioni.

1. Nel computer locale con i moduli di Azure PowerShell più recenti installati, eseguire il seguente comando PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Questo esempio visualizza il percorso locale dei moduli di Azure PowerShell. Queste cartelle verranno usate in un passaggio successivo. I moduli usati in questo scenario sono:

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

### <a name="authentication"></a>Autenticazione

Per usare i cmdlet PowerShell, è necessario eseguire l'autenticazione, che deve essere configurata nell'app per le funzioni. Per configurare l'autenticazione, è necessario configurare le variabili di ambiente e caricare un file di chiave crittografata nell'app per le funzioni.

> [!NOTE]
> Questo scenario fornisce solo un esempio di come implementare l'autenticazione con Funzioni di Azure; questa operazione può essere eseguita anche in altri modi.

#### <a name="encrypted-credentials"></a>Credenziali crittografate

Lo script seguente di PowerShell crea un file di chiave denominato **PassEncryptKey.key** e genera una versione crittografata della password fornita.  Questa password è la stessa che viene definita per l'applicazione di Azure AD usata per l'autenticazione.

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

### <a name="retrieve-values-for-environment-variables"></a>Recupero dei valori per le variabili di ambiente

La configurazione finale richiesta consente di configurare le variabili di ambiente necessarie per accedere ai valori per l'autenticazione. Di seguito è riportato un elenco delle variabili di ambiente che vengono create:

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

### <a name="store-the-environment-variables"></a>Archiviazione delle variabili di ambiente

1. Passare all'app per le funzioni, fare clic su **Impostazioni dell'app per le funzioni** > **Configura le impostazioni dell'app**.

    ![configurazione delle impostazioni dell'app][functions11]

1. Aggiungere le variabili di ambiente e i relativi valori alle impostazioni dell'app e fare clic su **Salva**.

    ![app settings][functions12]

### <a name="add-powershell-to-the-function"></a>Aggiunta di PowerShell alla funzione

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

Dopo avere creato la funzione, è necessario configurare l'avviso per chiamare l'URL associato alla funzione. Per ottenere questo valore, copiare l'URL della funzione dall'app per le funzioni.

![ricerca dell'URL della funzione 1][functions13]

Copiare l'URL della funzione per l'app per le funzioni.

![ricerca dell'URL della funzione 2][2]

Se sono necessarie proprietà personalizzate nel payload della richiesta POST del webhook, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurare un avviso in una VM

Si possono configurare avvisi per notificare alle singole persone quando una metrica specifica supera una soglia assegnata. In questo esempio, l'avviso è sui segmenti TCP inviati, ma può essere attivato per molte altre metriche. In questo esempio viene configurato un avviso per chiamare un webhook per chiamare la funzione.

### <a name="create-the-alert-rule"></a>Creare la regola di avviso

Passare a una macchina virtuale esistente, quindi aggiungere una regola di avviso. Per informazioni più dettagliate sulla configurazione di avvisi, vedere [Creazione di avvisi in Monitoraggio di Azure per i servizi Azure - Portale di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Immettere i valori seguenti nel pannello e fare clic su **OK**

  |**Impostazione** | **Valore** | **Dettagli** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome della regola di avviso.|
  |**Descrizione**|Soglia superata segmenti TCP inviati|Descrizione della regola di avviso.||
  |**Metrica**|Segmenti TCP inviati| La metrica da utilizzare per attivare l'avviso. |
  |**Condition**|Maggiore di| La condizione da utilizzare per valutare la metrica.|
  |**Soglia**|100| Il valore della metrica che attiva l'avviso. Questo valore deve essere impostato con un valore valido per l'ambiente in uso.|
  |**Periodo**|Negli ultimi 5 minuti| Determina il periodo in cui cercare la soglia per la metrica.|
  |**Webhook**|[url webhook dall’app per le funzioni]| Si tratta dell'url webhook dall’app per le funzioni creato nei passaggi precedenti.|

> [!NOTE]
> La metrica di segmenti TCP non è abilitata per impostazione predefinita. Per altre informazioni su come abilitare metriche aggiuntive, vedere [Abilitare il monitoraggio e la diagnostica](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Esaminare i risultati

Dopo i criteri di attivazione dell’avviso, viene creata l’acquisizione del pacchetto. Passare a Network Watcher e fare clic su **Acquisizione pacchetti**. In questa pagina è possibile fare clic sul collegamento del file di acquisizione pacchetti per scaricare l'acquisizione dei pacchetti

![visualizzazione di un'acquisizione di pacchetti][functions14]

Se il file di acquisizione è archiviato in locale, viene recuperato accedendo alla macchina virtuale.

Per istruzioni relative al download di file dagli account di archiviazione di Azure, consultare [Introduzione all'archiviazione BLOB di Azure con .NET](../storage/storage-dotnet-how-to-use-blobs.md). Un altro strumento è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

Dopo il download dell'acquisizione, è possibile visualizzarla con qualsiasi strumento per la lettura di un file **.cap**. Di seguito i collegamenti a due di questi strumenti:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare le acquisizioni di pacchetti, vedere [Analisi delle acquisizioni di pacchetti con Wireshark](network-watcher-alert-triggered-packet-capture.md).

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
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
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png

