---
title: Connettersi a Azure Resource Manager nel dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come connettersi alla Azure Resource Manager in esecuzione sulla GPU di Azure Stack Edge Pro usando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5cf406dc0577f477858dd8a6570f7975747112e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891252"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Connettersi a Azure Resource Manager nel dispositivo Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Resource Manager offre un livello di gestione che consente di creare, aggiornare ed eliminare risorse nella sottoscrizione di Azure. Il dispositivo Azure Stack Edge Pro supporta le stesse API Azure Resource Manager per creare, aggiornare ed eliminare le macchine virtuali in una sottoscrizione locale. Questo supporto consente di gestire il dispositivo in modo coerente con il cloud. 

Questa esercitazione descrive come connettersi alle API locali sul dispositivo Azure Stack Edge Pro tramite Azure Resource Manager con Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Informazioni su Azure Resource Manager

Azure Resource Manager offre un livello di gestione coerente per chiamare l'API del dispositivo Azure Stack Edge Pro ed eseguire operazioni quali la creazione, l'aggiornamento e l'eliminazione di macchine virtuali. Il diagramma seguente illustra in dettaglio l'architettura del Azure Resource Manager.

![Diagramma per Azure Resource Manager](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Endpoint nel dispositivo Azure Stack Edge Pro

La tabella seguente riepiloga i diversi endpoint esposti nel dispositivo, i protocolli supportati e le porte per accedere a tali endpoint. Nell'articolo sono disponibili riferimenti a questi endpoint.

| # | Endpoint | Protocolli supportati | Porta utilizzata | Utilizzato per |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Per connettersi a Azure Resource Manager per l'automazione |
| 2. | Servizio token di sicurezza | https | 443 | Per eseguire l'autenticazione tramite token di accesso e di aggiornamento |
| 3. | BLOB | https | 443 | Per connettersi all'archiviazione BLOB tramite REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Connessione al flusso di lavoro Azure Resource Manager

Il processo di connessione alle API locali del dispositivo con Azure Resource Manager richiede i passaggi seguenti:

| N.ro passaggio | Questa operazione verrà eseguita... | .. in questa posizione. |
| --- | --- | --- |
| 1. | [Configurare il dispositivo Azure Stack Edge Pro](#step-1-configure-azure-stack-edge-pro-device) | Interfaccia utente Web locale |
| 2. | [Creare e installare i certificati](#step-2-create-and-install-certificates) | Interfaccia utente Web locale/client Windows |
| 3. | [Esaminare e configurare i prerequisiti](#step-3-install-powershell-on-the-client) | Client Windows |
| 4. | [Configurare Azure PowerShell nel client](#step-4-set-up-azure-powershell-on-the-client) | Client Windows |
| 5. | [Modificare il file host per la risoluzione dei nomi degli endpoint](#step-5-modify-host-file-for-endpoint-name-resolution) | Server DNS o client Windows |
| 6. | [Verificare che il nome dell'endpoint sia stato risolto](#step-6-verify-endpoint-name-resolution-on-the-client) | Client Windows |
| 7. | [Usare i cmdlet di Azure PowerShell per verificare la connessione a Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Client Windows |

Le sezioni seguenti illustrano ogni passaggio precedente in connessione a Azure Resource Manager.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che il client usato per la connessione al dispositivo tramite Azure Resource Manager stia usando TLS 1,2. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di TLS 1,2 nel client Windows che accede al dispositivo Azure stack Edge Pro](azure-stack-edge-j-series-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Passaggio 1: configurare il dispositivo Pro Azure Stack Edge 

Eseguire i passaggi seguenti nell'interfaccia utente Web locale del dispositivo Azure Stack Edge Pro.

1. Completare le impostazioni di rete per il dispositivo Azure Stack Edge Pro. 

    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Prendere nota dell'indirizzo IP del dispositivo. Questo indirizzo IP verrà usato in un secondo momento.

2. Configurare il nome del dispositivo e il dominio DNS dalla pagina del **dispositivo** . Prendere nota del nome del dispositivo e del dominio DNS che si utilizzerà in un secondo momento.

    ![Pagina "Dispositivo" dell'interfaccia utente Web locale](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Il nome del dispositivo, dominio DNS verrà usato per formare gli endpoint esposti.
    > Usare gli endpoint Azure Resource Manager e BLOB dalla pagina **dispositivo** nell'interfaccia utente Web locale.


## <a name="step-2-create-and-install-certificates"></a>Passaggio 2: creare e installare i certificati

I certificati garantiscono che la comunicazione sia attendibile. Sul dispositivo Azure Stack Edge Pro i certificati autofirmati, BLOB e Azure Resource Manager vengono generati automaticamente. Facoltativamente, è possibile importare i propri BLOB firmati e Azure Resource Manager anche i certificati.

Quando si porta un certificato firmato, è necessaria anche la catena di firma corrispondente del certificato. Per la catena di firma, Azure Resource Manager e i certificati BLOB sul dispositivo, sarà necessario disporre dei certificati corrispondenti nel computer client anche per l'autenticazione e la comunicazione con il dispositivo.

Per connettersi a Azure Resource Manager, è necessario creare o ottenere i certificati della catena di firma e dell'endpoint, importare questi certificati nel client Windows e caricare infine questi certificati nel dispositivo.

### <a name="create-certificates-optional"></a>Creare certificati (facoltativo)

Solo per l'uso di test e sviluppo, è possibile usare Windows PowerShell per creare certificati nel sistema locale. Durante la creazione dei certificati per il client, attenersi alle seguenti linee guida:

1. Per prima cosa è necessario creare un certificato radice per la catena di firma. Per ulteriori informazioni, vedere la pagina relativa alla procedura per [creare certificati della catena di firma](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate).

2. È quindi possibile creare i certificati dell'endpoint per il BLOB e Azure Resource Manager. È possibile ottenere questi endpoint dalla pagina del **dispositivo** nell'interfaccia utente Web locale. Vedere i passaggi per [creare i certificati dell'endpoint](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates).

3. Per tutti questi certificati, verificare che il nome del soggetto e il nome alternativo del soggetto siano conformi alle linee guida seguenti:

    |Tipo |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Archiviazione BLOB|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificato singolo a più SAN per entrambi gli endpoint|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Per ulteriori informazioni sui certificati, vedere How to [Manage Certificates](azure-stack-edge-j-series-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Caricare i certificati sul dispositivo

I certificati creati nel passaggio precedente si troveranno nell'archivio personale del client. Questi certificati devono essere esportati nel client in file di formato appropriati che possono quindi essere caricati nel dispositivo.

1. Il certificato radice deve essere esportato come file di formato DER con estensione *. cer* . Per i passaggi dettagliati, vedere [esportare i certificati come file di formato CER](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format).

2. I certificati dell'endpoint devono essere esportati come file con *estensione pfx* con chiavi private. Per i passaggi dettagliati, vedere [esportare i certificati come file con estensione pfx con chiavi private](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. I certificati radice e dell'endpoint vengono quindi caricati sul dispositivo usando l'opzione **+ Aggiungi certificato** nella pagina **certificati** dell'interfaccia utente Web locale. Per caricare i certificati, attenersi alla procedura descritta in [caricare i certificati](azure-stack-edge-j-series-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importa i certificati sul client che esegue Azure PowerShell

Il client Windows in cui si richiameranno le API Azure Resource Manager deve stabilire una relazione di trust con il dispositivo. A tal fine, i certificati creati nel passaggio precedente devono essere importati nel client Windows nell'archivio certificati appropriato.

1. Il certificato radice esportato come formato DER con estensione *CER* dovrebbe ora essere importato nelle autorità di certificazione radice attendibili nel sistema client. Per i passaggi dettagliati, vedere [importare certificati nell'archivio Autorità di certificazione radice attendibili.](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)

2. I certificati di endpoint esportati come file con *estensione pfx* devono essere esportati come file con *estensione cer*. Questa *. cer* viene quindi importata nell'archivio certificati **personali** del sistema. Per i passaggi dettagliati, vedere [importare i certificati nell'archivio personale](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Passaggio 3: installare PowerShell nel client 

Il client Windows deve soddisfare i prerequisiti seguenti:

1. Eseguire PowerShell versione 5,0. È necessario disporre di PowerShell versione 5,0 o successiva. Per controllare la versione di PowerShell nel sistema, eseguire il cmdlet seguente:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Confrontare la versione **principale** e verificare che sia 5,0 o successiva.

    Se è presente una versione obsoleta, vedere [Aggiornamento di Windows PowerShell esistente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

    Se non si \' dispone di PowerShell 5,0, seguire [l'installazione di Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6).

    Di seguito è riportato un output di esempio.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. È possibile accedere al PowerShell Gallery.

    Eseguire PowerShell come amministratore. Verificare che il PSGallery sia registrato come repository.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Di seguito è riportato un output di esempio.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Se il repository non è attendibile o sono necessarie altre informazioni, vedere [convalidare l'accessibilità PowerShell Gallery](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Passaggio 4: configurare Azure PowerShell nel client 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Si installeranno i moduli Azure PowerShell nel client che funzioneranno con il dispositivo.

    a. Eseguire PowerShell come amministratore. È necessario accedere a PowerShell Gallery. 


    b. Per installare i moduli Azure PowerShell richiesti dall'PowerShell Gallery, eseguire il comando seguente:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Assicurarsi che la versione del modulo 2.5.0 di Azure-RM sia in esecuzione al termine dell'installazione. 
    Se si dispone di una versione esistente del modulo Azure-RM che non corrisponde alla versione richiesta, disinstallare usando il comando seguente:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    A questo punto è necessario installare di nuovo la versione richiesta.
   

Di seguito è riportato un esempio di output che indica che i moduli AzureRM versione 2.5.0 sono stati installati correttamente.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Passaggio 5: modificare il file host per la risoluzione dei nomi degli endpoint 

A questo punto si aggiungerà il VIP coerente di Azure definito nell'interfaccia utente Web locale del dispositivo a:

- Il file host nel client OPPURE
- La configurazione del server DNS

> [!IMPORTANT]
> Si consiglia di modificare la configurazione del server DNS per la risoluzione dei nomi di endpoint.

Nel client Windows usato per la connessione al dispositivo, seguire questa procedura:

1. Avviare il **blocco note** come amministratore e quindi aprire il file **hosts** disponibile in C:\Windows\System32\Drivers\etc.

    ![File hosts di Esplora risorse](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. Aggiungere le voci seguenti al file **hosts** sostituendo i valori con quelli appropriati per il dispositivo: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > La voce nel file degli host deve corrispondere esattamente a quella fornita per connettersi a Azure Resource Manager in un passaggio successivo. Verificare che la voce del dominio DNS sia presente in minuscolo.

    L'IP del dispositivo è stato salvato dall'interfaccia utente Web locale in un passaggio precedente.

    Account di accesso \<appliance name\> .\<DNS domain\> entry è l'endpoint per il servizio token di sicurezza (STS). STS è responsabile della creazione, della convalida, del rinnovo e dell'annullamento dei token di sicurezza. Il servizio token di sicurezza viene usato per creare il token di accesso e il token di aggiornamento usati per la comunicazione continua tra il dispositivo e il client.

3. Per indicazioni, vedere l'immagine seguente. Salvare il file **hosts**.

    ![File hosts nel Blocco note](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Passaggio 6: verificare la risoluzione dei nomi dell'endpoint nel client

Controllare se il nome dell'endpoint è stato risolto nel client usato per connettersi al VIP coerente di Azure.

1. È possibile utilizzare l'utilità da riga di comando ping.exe per verificare che il nome dell'endpoint sia stato risolto. Dato un indirizzo IP, il comando ping restituirà il nome host TCP/IP del computer che si sta \' ritracciando.

    Aggiungere l' `-a` opzione alla riga di comando come illustrato nell'esempio riportato di seguito. Se il nome host può essere restituito, restituirà anche queste informazioni potenzialmente utili nella risposta.

    ![Ping nel prompt dei comandi](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Passaggio 7: impostare Azure Resource Manager ambiente

Impostare l'ambiente di Azure Resource Manager e verificare che il dispositivo alla comunicazione client tramite Azure Resource Manager funzioni correttamente. Per questa verifica, seguire questa procedura:


1. Usare il `Add-AzureRmEnvironment` cmdlet per assicurarsi che la comunicazione tramite Azure Resource Manager funzioni correttamente e che le chiamate API attraversino la porta dedicata per Azure Resource Manager-443.

    Il `Add-AzureRmEnvironment` cmdlet aggiunge endpoint e metadati per consentire a Azure Resource Manager cmdlet di connettersi a una nuova istanza di Azure Resource Manager. 


    > [!IMPORTANT]
    > L'URL dell'endpoint Azure Resource Manager fornito nel cmdlet seguente fa distinzione tra maiuscole e minuscole. Verificare che l'URL dell'endpoint sia in lettere minuscole e corrisponda a quanto specificato nel file degli host. Se il case non corrisponde, verrà visualizzato un errore.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Di seguito è riportato un output di esempio:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Impostare l'ambiente come Azure Stack Edge Pro e la porta da usare per Azure Resource Manager chiamate come 443. L'ambiente viene definito in due modi:

    - Impostare l'ambiente. Digitare il comando seguente:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Per ulteriori informazioni, vedere [set-AzureRMEnvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    - Definire l'ambiente inline per ogni cmdlet eseguito. Ciò garantisce che tutte le chiamate API stiano attraversando l'ambiente corretto. Per impostazione predefinita, le chiamate passano attraverso il pubblico di Azure, ma si vuole che questi passino attraverso l'ambiente impostato per il dispositivo Azure Stack Edge Pro.

    - Per altre informazioni su [come cambiare gli ambienti AzureRM](#switch-environments), vedere.

2. Chiamare le API del dispositivo locale per autenticare le connessioni a Azure Resource Manager. 

    1. Queste credenziali sono per un account del computer locale e vengono usate esclusivamente per l'accesso all'API.

    2. È possibile connettersi tramite il `login-AzureRMAccount` comando o via `Connect-AzureRMAccount` . 

        1. Per accedere, digitare il comando seguente. L'ID tenant in questa istanza è hardcoded-c0257de7-538f-415c-993a-1b87a031879d. Usare il nome utente e la password seguenti.

            - **Nome utente**  -  *EdgeArmUser*

            - **Password**  -  di [Impostare la password per Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md) e usare questa password per accedere. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Un metodo alternativo per accedere consiste nell'usare il `login-AzureRmAccount` cmdlet. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            Di seguito è riportato un esempio di output del comando. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> La connessione al Azure Resource Manager scade ogni 1,5 ore o se il dispositivo Azure Stack Edge Pro viene riavviato. In tal caso, tutti i cmdlet eseguiti restituiranno messaggi di errore che non sono più connessi ad Azure. Sarà necessario eseguire di nuovo l'accesso.

## <a name="switch-environments"></a>Cambia ambienti

Eseguire `Disconnect-AzureRmAccount` il comando per passare a un diverso `AzureRmEnvironment` . 

Se si utilizza `Set-AzureRmEnvironment` e `Login-AzureRmAccount` senza utilizzare `Disconnect-AzureRmAccount` , l'ambiente non viene effettivamente scambiato.  

Negli esempi seguenti viene illustrato come passare tra due ambienti, `AzDBE1` e `AzDBE2` .

In primo luogo, elencare tutti gli ambienti esistenti nel client.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Successivamente, ottenere l'ambiente a cui si è attualmente connessi tramite il Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

È ora necessario disconnettersi dall'ambiente corrente prima di passare all'altro ambiente.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Accedere all'altro ambiente. Di seguito è riportato l'output di esempio.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Eseguire questo cmdlet per confermare l'ambiente a cui si è connessi.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
È ora possibile passare all'ambiente desiderato.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire le macchine virtuali nel dispositivo Azure stack Edge Pro](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
