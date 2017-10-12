---
title: Usare l'acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con avvisi e Funzioni di Azure | Microsoft Docs
description: Questo articolo descrive come creare un'acquisizione di pacchetti attivata da un avviso con Azure Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1b3da4d6e4593f3c71995ef9331fcea2d5b6ec19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Usare l'acquisizione di pacchetti per il monitoraggio proattivo della rete con avvisi e Funzioni di Azure

Il servizio di acquisizione di pacchetti di Network Watcher crea sessioni di acquisizione per registrare il traffico da e verso le macchine virtuali. Il file di acquisizione può avere un filtro che viene definito per tenere traccia solo del traffico che si vuole monitorare. Questi dati vengono quindi archiviati in un BLOB di archiviazione o in locale nel computer guest.

Questa funzionalità può essere avviata in remoto da altri scenari di automazione, ad esempio Funzioni di Azure. L'acquisizione di pacchetti consente di eseguire acquisizioni proattive in base alle anomalie di rete definite. Altri utilizzi comprendono la raccolta di statistiche di rete, l'ottenimento di informazioni sulle intrusioni nella rete, il debug delle comunicazioni client-server e molto altro ancora.

Le risorse distribuite in Azure sono in esecuzione 24 ore su 24, 7 giorni su 7. Nessuno può monitorare attivamente lo stato di tutte le risorse 24 ore su 24, 7 giorni su 7. Si pensi a cosa può accadere se si verifica un problema alle 2 del mattino.

Usando Network Watcher, gli avvisi e le funzioni dall'ecosistema di Azure, è possibile rispondere in modo proattivo alle problematiche della rete con i dati e gli strumenti più idonei.

![Scenario][scenario]

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Un'istanza esistente di Network Watcher. Se non è già presente, creare un'[istanza di Network Watcher](network-watcher-create.md).
* Una macchina virtuale esistente nella stessa area di Network Watcher con [estensione Windows](../virtual-machines/windows/extensions-nwa.md) o [estensione della macchina virtuale Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenario

In questo esempio, la VM invia più segmenti TCP del solito e si vuole essere avvisati. Come esempio qui vengono usati i segmenti TCP, ma è possibile usare qualsiasi condizione di avviso.

Una volta ricevuto l'avviso, si vogliono ricevere dati a livello di pacchetto per comprendere perché la comunicazione è aumentata. Sarà così possibile intervenire per riportare la macchina virtuale in condizioni di normale comunicazione.

Questo scenario presuppone che esistano già un'istanza di Network Watcher e un gruppo di risorse con una macchina virtuale valida.

L'elenco seguente è una panoramica del flusso di lavoro effettivo:

1. Nella macchina virtuale viene attivato un avviso.
1. L'avviso chiama la funzione di Azure tramite un webhook.
1. La funzione di Azure elabora l'avviso e avvia una sessione di acquisizione di pacchetti di Network Watcher.
1. L'acquisizione di pacchetti viene eseguita nella macchina virtuale e raccoglie il traffico.
1. Il file di acquisizione pacchetto viene caricato in un account di archiviazione per revisione e diagnosi.

Per automatizzare questo processo, nella VM viene creato e connesso un avviso da attivare quando si verifica l'evento imprevisto. Viene anche creata una funzione per chiamare Network Watcher.

Questo scenario prevede le seguenti operazioni:

* Crea una funzione di Azure che avvia un'acquisizione di pacchetti.
* Crea una regola di avviso in una macchina virtuale e configura la regola di avviso in modo da chiamare la funzione di Azure.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Il primo passaggio è la creazione di una funzione di Azure per elaborare l'avviso e creare un'acquisizione di pacchetti.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Nuovo** > **Calcolo** > **App per le funzioni**.

    ![Creazione di un'app per le funzioni][1-1]

2. Nel pannello **App per le funzioni** immettere i valori seguenti e fare clic su **OK** per creare l'app:

    |**Impostazione** | **Valore** | **Dettagli** |
    |---|---|---|
    |**Nome app**|PacketCaptureExample|Nome dell'app per le funzioni.|
    |**Sottoscrizione**|[Sottoscrizione]: sottoscrizione in cui creare l'app per le funzioni.||
    |**Gruppo di risorse**|PacketCaptureRG|Nome del gruppo di risorse che conterrà l'app per le funzioni.|
    |**Piano di hosting**|Piano a consumo| Tipo di piano usato dall'app per le funzioni. Le opzioni sono Consumo e Piano di servizio app di Azure. |
    |**Posizione**|Stati Uniti centrali| Area in cui creare l'app per le funzioni.|
    |**Storage Account**|{generato automaticamente}| Account di archiviazione richiesto da Funzioni di Azure per l'archiviazione di uso generico.|

3. Nel pannello delle app per le funzioni **PacketCaptureExample** selezionare **Funzioni** > **Funzione personalizzata** >**+**.

4. Selezionare **HttpTrigger-Powershell** e quindi immettere le informazioni rimanenti. Selezionare infine **Crea** per creare la funzione.

    |**Impostazione** | **Valore** | **Dettagli** |
    |---|---|---|
    |**Scenario**|Sperimentale|Tipo di scenario|
    |**Dare un nome alla funzione**|AlertPacketCapturePowerShell|Nome della funzione|
    |**Livello di autorizzazione**|Funzione|Livello di autorizzazione per la funzione|

![Esempio di funzioni][functions1]

> [!NOTE]
> Il modello di PowerShell è sperimentale e non dispone del supporto completo.

Per questo esempio sono richieste alcune personalizzazioni illustrate nei passaggi seguenti.

### <a name="add-modules"></a>Aggiungere moduli

Per usare i cmdlet PowerShell di Network Watcher, caricare il modulo PowerShell più recente nell'app per le funzioni.

1. Nel computer locale con i moduli di Azure PowerShell più recenti installati, eseguire il seguente comando PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Questo esempio visualizza il percorso locale dei moduli di Azure PowerShell. Queste cartelle verranno usate in un passaggio successivo. I moduli usati in questo scenario sono:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Cartelle di PowerShell][functions5]

1. Selezionare **Impostazioni dell'app per le funzioni** > **Passa all'editor del servizio app**.

    ![Impostazioni dell'app per le funzioni][functions2]

1. Fare clic con il pulsante destro del mouse sulla cartella **AlertPacketCapturePowershell** e quindi creare una cartella denominata **azuremodules**. 

4. Creare una sottocartella per ogni modulo necessario.

    ![Cartelle e sottocartelle][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Fare clic con il pulsante destro del mouse sulla sottocartella **AzureRM.Network** e quindi scegliere **Carica file**. 

6. Passare ai moduli di Azure. Nella cartella **AzureRM.Network** locale selezionare tutti i file presenti. Selezionare **OK**. 

7. Ripetere questi passaggi per **AzureRM.Profile** e **AzureRM.Resources**.

    ![Caricare file][functions6]

1. Al termine, ogni cartella dovrebbe includere i file dei moduli di PowerShell del computer locale.

    ![File di PowerShell][functions7]

### <a name="authentication"></a>Autenticazione

Per usare i cmdlet PowerShell, è necessario eseguire l'autenticazione, L'autenticazione viene configurata nell'app per le funzioni. Per configurare l'autenticazione è necessario definire le variabili di ambiente e caricare un file di chiave crittografata nell'app per le funzioni.

> [!NOTE]
> Questo scenario fornisce solo un esempio di come implementare l'autenticazione con Funzioni di Azure. Per eseguire questa operazione è possibile procedere in altri modi.

#### <a name="encrypted-credentials"></a>Credenziali crittografate

Lo script di PowerShell seguente crea un file di chiave denominato **PassEncryptKey.key** e mette a disposizione una versione crittografata della password fornita. Questa password è la stessa che viene definita per l'applicazione di Azure Active Directory usata per l'autenticazione.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Nell'editor del servizio app dell'app per le funzioni creare una cartella denominata **keys** in **AlertPacketCapturePowerShell**. Caricare quindi il file **PassEncryptKey.key** creato nell'esempio di PowerShell precedente.

![Chiave delle funzioni][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Recuperare i valori per le variabili di ambiente

L'ultimo requisito consiste nel configurare le variabili di ambiente necessarie per accedere ai valori per l'autenticazione. Di seguito è riportato un elenco delle variabili di ambiente create:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

L'ID client è l'ID applicazione di un'applicazione in Azure Active Directory.

1. Se non esiste già un'applicazione da usare, eseguire questo esempio per crearne una.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > La password usata per la creazione dell'applicazione deve essere la stessa creata in precedenza durante il salvataggio del file di chiave.

1. Nel portale di Azure selezionare **Sottoscrizioni**. Selezionare la sottoscrizione da usare e quindi fare clic su **Controllo di accesso (IAM)**.

    ![IAM delle funzioni][functions9]

1. Scegliere l'account da usare e quindi selezionare **Proprietà**. Copiare l'ID applicazione.

    ![ID applicazione per le funzioni][functions10]

#### <a name="azuretenant"></a>AzureTenant

Ottenere l'ID tenant eseguendo questo esempio di PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Il valore della variabile di ambiente AzureCredPassword è il valore ottenuto eseguendo questo esempio di PowerShell. Si tratta dello stesso esempio riportato nella sezione **Credenziali crittografate** precedente. Il valore richiesto è l'output della variabile `$Encryptedpassword`.  Si tratta della password dell'entità servizio crittografata tramite lo script di PowerShell.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Archiviare le variabili di ambiente

1. Passare all'app per le funzioni. Selezionare quindi **Impostazioni dell'app per le funzioni** > **Configurare le impostazioni dell'app**.

    ![Configurare le impostazioni applicazione][functions11]

1. Aggiungere le variabili di ambiente e i relativi valori alle impostazioni dell'app e quindi fare clic su **Salva**.

    ![Impostazioni app][functions12]

### <a name="add-powershell-to-the-function"></a>Aggiunta di PowerShell alla funzione

A questo punto è necessario chiamare Network Watcher dalla funzione di Azure. L'implementazione di questa funzione può variare a seconda dei requisiti. Il flusso generale del codice è tuttavia il seguente:

1. Elaborare i parametri di input.
2. Eseguire una query sulle acquisizioni di pacchetti esistenti per verificare i limiti e risolvere i conflitti di nomi.
3. Creare un'acquisizione di pacchetti con i parametri appropriati.
4. Eseguire periodicamente il polling dell'acquisizione di pacchetti fino al completamento.
5. Notificare all'utente che la sessione di acquisizione di pacchetti è completa.

L'esempio seguente è codice PowerShell che può essere usato nella funzione. È necessario sostituire i valori per **subscriptionId**, **resourceGroupName** e **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
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


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Recuperare l'URL della funzione 
1. Dopo avere creato la funzione, configurare l'avviso per chiamare l'URL associato alla funzione. Per ottenere questo valore, copiare l'URL della funzione dall'app per le funzioni.

    ![Ricerca dell'URL della funzione][functions13]

2. Copiare l'URL della funzione per l'app per le funzioni.

    ![Copia dell'URL della funzione][2]

Se sono necessarie proprietà personalizzate nel payload della richiesta POST del webhook, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configurare un avviso in una VM

Si possono configurare avvisi per notificare alle singole persone quando una metrica specifica supera una soglia assegnata. In questo esempio, l'avviso è sui segmenti TCP inviati, ma può essere attivato per molte altre metriche. In questo esempio viene configurato un avviso per chiamare un webhook per chiamare la funzione.

### <a name="create-the-alert-rule"></a>Creare la regola di avviso

Passare a una macchina virtuale esistente, quindi aggiungere una regola di avviso. Per informazioni più dettagliate sulla configurazione di avvisi, vedere [Creazione di avvisi in Monitoraggio di Azure per i servizi Azure - Portale di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Immettere i valori seguenti nel pannello **Regola di avviso** e fare clic su **OK**.

  |**Impostazione** | **Valore** | **Dettagli** |
  |---|---|---|
  |**Nome**|TCP_Segments_Sent_Exceeded|Nome della regola di avviso.|
  |**Descrizione**|Soglia superata segmenti TCP inviati|Descrizione della regola di avviso.||
  |**Metrica**|Segmenti TCP inviati| La metrica da utilizzare per attivare l'avviso. |
  |**Condition**|Maggiore di| La condizione da utilizzare per valutare la metrica.|
  |**Soglia**|100| Valore della metrica che attiva l'avviso. Deve trattarsi di un valore valido per l'ambiente in uso.|
  |**Periodo**|Negli ultimi cinque minuti| Determina il periodo in cui cercare la soglia per la metrica.|
  |**Webhook**|[URL webhook dell'app per le funzioni]| URL webhook dall'app per le funzioni creata nei passaggi precedenti.|

> [!NOTE]
> La metrica di segmenti TCP non è abilitata per impostazione predefinita. Per altre informazioni su come abilitare metriche aggiuntive, vedere [Abilitare il monitoraggio e la diagnostica](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Esaminare i risultati

Dopo i criteri di attivazione dell'avviso, viene creata l'acquisizione di pacchetti. Passare a Network Watcher e quindi selezionare **Acquisizione pacchetti**. In questa pagina è possibile fare clic sul collegamento del file di acquisizione pacchetti per scaricare l'acquisizione di pacchetti.

![Visualizzare l'acquisizione di pacchetti][functions14]

Se il file di acquisizione è archiviato in locale, è possibile recuperarlo accedendo alla macchina virtuale.

Per istruzioni relative al download di file dagli account di archiviazione di Azure, vedere [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Un altro strumento è [Storage Explorer](http://storageexplorer.com/).

Dopo il download dell'acquisizione, è possibile visualizzarla con qualsiasi strumento per la lettura di un file **.cap**. Di seguito i collegamenti a due di questi strumenti:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare le acquisizioni di pacchetti, vedere [Analisi delle acquisizioni di pacchetti con Wireshark](network-watcher-deep-packet-inspection.md).


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
