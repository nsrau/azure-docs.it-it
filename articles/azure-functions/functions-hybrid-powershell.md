---
title: Gestire le risorse locali remote usando le funzioni di PowerShell
description: Informazioni su come configurare Connessioni ibride nel inoltro di Azure per connettere un'app per le funzioni di PowerShell a risorse locali, che possono essere usate per gestire in remoto la risorsa locale.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122272"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gestione di ambienti ibridi con PowerShell in funzioni di Azure e Connessioni ibride del servizio app

La funzionalità Connessioni ibride del servizio app Azure consente l'accesso alle risorse di altre reti. Per ulteriori informazioni su questa funzionalità, vedere la documentazione di [connessioni ibride](../app-service/app-service-hybrid-connections.md) . Questo articolo descrive come usare questa funzionalità per eseguire le funzioni di PowerShell destinate a un server locale. Questo server può quindi essere usato per gestire tutte le risorse nell'ambiente locale da una funzione Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurare un server locale per la comunicazione remota di PowerShell

Con lo script seguente viene abilitata la comunicazione remota di PowerShell e viene creata una nuova regola del firewall e un listener HTTPS WinRM. A scopo di test, viene usato un certificato autofirmato. In un ambiente di produzione è consigliabile usare un certificato firmato.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Creare un'app per le funzioni di PowerShell nel portale

Il servizio app Connessioni ibride funzionalità è disponibile solo nei piani tariffari Basic, standard e isolati. Quando si crea l'app per le funzioni con PowerShell, creare o selezionare uno di questi piani.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella pagina **Nuovo**, selezionare **Calcolo** > **App per le funzioni**.

1. Nella pagina **Informazioni di base** usare le impostazioni dell'app per le funzioni specificate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.  |
    |**Pubblica**| Codice | Opzione per la pubblicazione di file di codice o di un contenitore Docker. |
    | **Stack di runtime** | Lingua preferita | Scegliere PowerShell core. |
    |**Versione**| Numero di versione | Scegliere la versione del runtime installato.  |
    |**Area**| Area preferita | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Creare un'app per le funzioni-nozioni di base." border="true":::

1. Selezionare **Avanti: Hosting**. Nella pagina **Hosting** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Account di archiviazione](../storage/common/storage-account-create.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema operativo**| Sistema operativo preferito | Viene preselezionato automaticamente un sistema operativo in base alla selezione dello stack di runtime, ma, se necessario, è possibile modificare l'impostazione. |
    | **[Tipo di piano](../azure-functions/functions-scale.md)** | **Piano di servizio app** | Scegliere il **piano di servizio app**. In caso di esecuzione in un piano di servizio app, è necessario gestire il [ridimensionamento dell'app per le funzioni](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Creare un'app per le funzioni-hosting." border="true":::

1. Selezionare **Avanti: Monitoraggio**. Nella pagina **Monitoraggio** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione o selezionando **Crea nuovo**, è possibile modificare il nome del Application Insights o scegliere un'area diversa in una [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vogliono archiviare i dati. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Creare un monitoraggio delle app per le funzioni." border="true":::

1. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.

1. Nella pagina **Rivedi e crea** esaminare le impostazioni e quindi selezionare **Crea** per effettuare il provisioning e distribuire l'app per le funzioni.

1. Selezionare l'icona **notifiche** nell'angolo superiore destro del portale e controllare il messaggio **distribuzione riuscita** .

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Creare una connessione ibrida per l'app per le funzioni

Le connessioni ibride sono configurate dalla sezione rete dell'app per le funzioni:

1. In **Impostazioni** nell'app per le funzioni appena creata selezionare **rete**. 
1. Selezionare **Configura gli endpoint delle connessioni ibride**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Configurare gli endpoint della connessione ibrida." border="true":::

1. Selezionare **Aggiungi connessione ibrida**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Aggiungere una connessione ibrida." border="true":::

1. Immettere le informazioni sulla connessione ibrida, come illustrato immediatamente dopo la schermata seguente. È possibile fare in modo che l'impostazione **host endpoint** corrisponda al nome host del server locale per semplificare la memorizzazione del server in un secondo momento quando si eseguono comandi remoti. La porta corrisponde alla porta predefinita del servizio gestione remota Windows che è stata definita nel server.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Aggiungere la connessione ibrida." border="true":::

    | Impostazione      | Valore consigliato  |
    | ------------ | ---------------- |
    | **Nome della connessione ibrida** | ContosoHybridOnPremisesServer |
    | **Host endpoint** | finance1 |
    | **Porta endpoint** | 5986 |
    | **Spazio dei nomi ServiceBus** | Creazione di un nuovo sito |
    | **Posizione** | Seleziona un percorso disponibile |
    | **Nome** | contosopowershellhybrid | 

1. Selezionare **OK** per creare la connessione ibrida.

## <a name="download-and-install-the-hybrid-connection"></a>Scaricare e installare la connessione ibrida

1. Selezionare **Scarica gestione connessione** per salvare il file con *estensione msi* localmente nel computer.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Scaricare il programma di installazione." border="true":::

1. Copiare il file con *estensione msi* dal computer locale al server locale.
1. Eseguire il programma di installazione Gestione connessione ibrida per installare il servizio nel server locale.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Installare la connessione ibrida." border="true":::

1. Dal portale aprire la connessione ibrida, quindi copiare la stringa di connessione del gateway negli Appunti.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Copiare la stringa di connessione ibrida." border="true":::

1. Aprire l'interfaccia utente di Gestione connessione ibrida nel server locale.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Aprire l'interfaccia utente della connessione ibrida." border="true":::

1. Selezionare **invio manualmente** e incollare la stringa di connessione negli Appunti.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Incollare la connessione ibrida." border="true":::

1. Riavviare il Gestione connessione ibrida da PowerShell se non viene visualizzato come connesso.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Creare un'impostazione dell'app per la password di un account amministratore

1. In **Impostazioni** per l'app per le funzioni selezionare **configurazione**. 
1. Selezionare **+ nuova impostazione applicazione**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Configurare una password per l'account amministratore." border="true":::

1. Denominare l'impostazione **ContosoUserPassword**e immettere la password. Selezionare **OK**.
1. Selezionare **Salva** per archiviare la password nell'applicazione per le funzioni.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Salvare la password per l'account amministratore." border="true":::

## <a name="create-a-function-http-trigger"></a>Creare un trigger HTTP di funzione

1. Nell'app per le funzioni selezionare **funzioni**e quindi selezionare **+ Aggiungi**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Crea nuovo trigger HTTP." border="true":::

1. Selezionare il modello di **trigger http** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Selezionare il modello di trigger HTTP." border="true":::

1. Assegnare un nome alla nuova funzione e selezionare **Crea funzione**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Nome e creazione della nuova funzione trigger HTTP." border="true":::

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione selezionare **codice + test**. Sostituire il codice di PowerShell dal modello con il codice seguente:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Selezionare **Salva**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Modificare il codice di PowerShell e salvare la funzione trigger HTTP." border="true":::

 1. Selezionare **test**, quindi selezionare **Esegui** per testare la funzione. Esaminare i log per verificare che il test abbia avuto esito positivo.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Testare la funzione trigger HTTP." border="true":::

## <a name="managing-other-systems-on-premises"></a>Gestione di altri sistemi locali

È possibile usare il server locale connesso per connettersi ad altri server e sistemi di gestione nell'ambiente locale. In questo modo è possibile gestire le operazioni dei data center da Azure usando le funzioni di PowerShell. Lo script seguente registra una sessione di configurazione di PowerShell che viene eseguita con le credenziali fornite. Queste credenziali devono essere per un amministratore nei server remoti. È quindi possibile usare questa configurazione per accedere ad altri endpoint nel server locale o nel Data Center.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Sostituire le variabili seguenti in questo script con i valori applicabili dell'ambiente:
* $HybridEndpoint
* $RemoteServer

Nei due scenari precedenti, è possibile connettere e gestire gli ambienti locali usando PowerShell in funzioni di Azure e Connessioni ibride. Si consiglia di ottenere altre informazioni su [connessioni ibride](../app-service/app-service-hybrid-connections.md) e [PowerShell nelle funzioni](./functions-reference-powershell.md).

È anche possibile usare le [reti virtuali](./functions-create-vnet.md) di Azure per connettersi all'ambiente locale tramite funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Altre informazioni sull'uso delle funzioni di PowerShell](functions-reference-powershell.md)
