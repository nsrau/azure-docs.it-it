---
title: Gestire le risorse locali remote con funzioni di Azure di PowerShell
description: Informazioni su come configurare Connessioni ibride nel inoltro di Azure per connettere un'app per le funzioni di PowerShell a risorse locali, che possono essere usate per gestire in remoto la risorsa locale.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775416"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gestione di ambienti ibridi con PowerShell in funzioni di Azure e Connessioni ibride del servizio app

Il servizio app Azure Connessioni ibride consente l'accesso alle risorse in altre reti. Per ulteriori informazioni su questa funzionalità, vedere la documentazione [connessioni ibride](../app-service/app-service-hybrid-connections.md) . Le informazioni seguenti descrivono come usare questa funzionalità per eseguire le funzioni di PowerShell destinate a un server locale. Questo server può quindi essere usato per gestire tutte le risorse all'interno dell'ambiente locale da una funzione Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurare un server locale per la comunicazione remota di PowerShell

Lo script seguente abilita la comunicazione remota di PowerShell, crea una nuova regola del firewall e un listener HTTPS WinRM. A scopo di test, viene usato un certificato autofirmato. Si consiglia di utilizzare un certificato firmato per la produzione.

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Creare un'app per le funzioni di PowerShell nel portale

Connessioni ibride del servizio app sono disponibili solo nei piani tariffari Basic, standard e isolati. Quando si crea l'app per le funzioni con PowerShell, creare o selezionare uno di questi piani.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **+ Crea una risorsa** nella parte sinistra, quindi scegliere app per le **funzioni**.

1. Per **piano di hosting**, scegliere **piano di servizio app**, quindi selezionare **piano di servizio app/percorso**.

1. Selezionare **Crea nuovo**, digitare un nome per il **piano di servizio app** , scegliere una **località** in un' [area](https://azure.microsoft.com/regions/) vicina o vicino ad altri servizi a cui accedono le funzioni e quindi selezionare piano **tariffario**.

1. Scegliere il piano standard S1, quindi selezionare **applica**.

1. Selezionare **OK** per creare il piano, quindi usare le impostazioni dell'app per le funzioni rimanenti, come specificato nella tabella sotto l'immagine. 

    ![App per le funzioni PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome app** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z`, `0-9` e `-`.  | 
    | **Sottoscrizione** | Sottoscrizione | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **Gruppo di risorse** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. È anche possibile usare il valore suggerito. |
    | **OS** | Sistema operativo preferito | Selezionare Windows. |
    | **Stack di runtime** | Lingua preferita | Scegliere PowerShell core. |
    | **Archiviazione** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È possibile anche usare un account esistente.
    | **Application Insights** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione, è possibile cambiare il **Nome nuova risorsa** oppure scegliere una **Località** in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vogliono archiviare i dati. |

1. Dopo aver convalidato le impostazioni, selezionare **Crea**.

1. Selezionare l'icona di notifica nell'angolo superiore destro del portale e attendere la visualizzazione del messaggio **La distribuzione è riuscita**.

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Creare una connessione ibrida per l'app per le funzioni

Le connessioni ibride sono configurate dalla sezione rete dell'app per le funzioni.

1. Selezionare la scheda piattaforma dell'app per le funzioni e selezionare rete.
![Panoramica delle app per la rete della piattaforma](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selezionare Configura gli endpoint delle connessioni ibride.
![Rete](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selezionare Aggiungi connessione ibrida.
![Connessione ibrida](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Immettere le informazioni relative alla connessione ibrida, come illustrato di seguito. L'host dell'endpoint può eventualmente corrispondere al nome host del server locale per semplificare la memorizzazione del server in un secondo momento durante l'esecuzione di comandi remoti. La porta corrisponde alla porta predefinita del servizio gestione remota Windows definita nel server.
![Aggiungi connessione ibrida](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Nome della connessione ibrida** ContosoHybridOnPremisesServer
    
    Finance1 **host endpoint**
    
    **Porta Endpoint** 5986
    
    **Spazio dei nomi ServiceBus** Crea nuovo
    
    **Posizione** Seleziona un percorso disponibile
    
    **Nome** contosopowershellhybrid

5. Fare clic su OK per creare la connessione ibrida

## <a name="download-and-install-the-hybrid-connection"></a>Scaricare e installare la connessione ibrida

1. Selezionare l'icona Scarica gestione connessione per salvare il file con estensione msi localmente nel computer.
![Scarica programma di installazione](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Copiare il file con estensione msi dal computer locale al server locale.
1. Eseguire il programma di installazione della connessione ibrida per installare il servizio nel server locale.
![Installare la connessione ibrida](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Dal portale aprire la connessione ibrida e copiare la stringa di connessione del gateway negli Appunti.
![Copia stringa di connessione ibrida](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Aprire l'interfaccia utente di Gestione connessione ibrida nel server locale.
![Apri interfaccia utente di connessione ibrida](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selezionare il pulsante "immettere manualmente" e incollare la stringa di connessione negli Appunti.
![Incolla connessione](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Riavviare Gestione connessione ibrida da PowerShell se non viene visualizzata la connessione.
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Creare un'impostazione dell'app per la password di un account amministratore

1. Selezionare la scheda piattaforma dall'applicazione funzione
1. Selezionare la configurazione dalla sezione ![impostazioni generali selezionare configurazione piattaforma](./media/functions-hybrid-powershell/select-configuration.png)  
1. Selezionare nuova impostazione applicazione per creare una nuova impostazione per la password
1. Denominare l'impostazione ContosoUserPassword e immettere la password
1. Selezionare OK e quindi Salva per archiviare la password nell'applicazione ![per le funzioni Aggiungi app impostazione per password](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Creare un trigger http per la funzione da testare

1. Creare una nuova funzione trigger http dall'app ![per le funzioni crea nuovo trigger http](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Sostituire il codice di PowerShell dal modello con il codice seguente:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
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

3. Fare clic su Salva ed Esegui per testare ![l'app per le funzioni di test delle funzioni](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Gestione di altri sistemi locali

È possibile usare il server locale connesso per connettersi ad altri server e sistemi di gestione nell'ambiente locale. In questo modo è possibile gestire le operazioni data center da Azure usando le funzioni di PowerShell. Lo script seguente registra una sessione di configurazione di PowerShell che viene eseguita con le credenziali fornite. Queste credenziali devono essere un amministratore nei server remoti. È quindi possibile usare questa configurazione per accedere ad altri endpoint nel server locale o in data center.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

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

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
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

Sostituire queste variabili nello script precedente con i valori presenti nell'ambiente:
* $HybridEndpoint
* $RemoteServer

I due scenari precedenti consentono di connettere e gestire gli ambienti locali usando PowerShell in funzioni di Azure e Connessioni ibride. Per informazioni aggiuntive sulle funzioni di [connessioni ibride](../app-service/app-service-hybrid-connections.md) o [PowerShell](./functions-reference-powershell.md) , vedere la documentazione di.

È anche possibile usare le [reti virtuali](./functions-create-vnet.md) di Azure per connettersi all'ambiente locale usando funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Altre informazioni sull'uso delle funzioni di PowerShell](functions-reference-powershell.md)
