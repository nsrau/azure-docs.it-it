---
title: Gestire le risorse locali remote tramite le funzioni di PowerShellManage remote on-premises resources by using PowerShell functions
description: Informazioni su come configurare connessioni ibride nell'inoltro di Azure per connettere un'app per le funzioni di PowerShell alle risorse locali, che possono quindi essere usate per gestire in remoto la risorsa locale.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226928"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gestione di ambienti ibridi con PowerShell in Funzioni di Azure e connessioni ibride del servizio appManaging hybrid environments with PowerShell in Azure Functions and App Service Hybrid Connections

La funzionalità Connessioni ibride del servizio app di Azure consente l'accesso alle risorse in altre reti. Per altre informazioni su questa funzionalità, vedere la documentazione relativa alle [connessioni ibride.](../app-service/app-service-hybrid-connections.md) In questo articolo viene descritto come utilizzare questa funzionalità per eseguire le funzioni di PowerShell destinate a un server locale. Questo server può quindi essere usato per gestire tutte le risorse nell'ambiente locale da una funzione di Azure PowerShell.This server can then be used to manage all resources in the on-premises environment from an Azure PowerShell function.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configurare un server locale per la comunicazione remota di PowerShellConfigure an on-premises server for PowerShell remoting

Lo script seguente abilita la comunicazione remota di PowerShell e crea una nuova regola del firewall e un listener https di Gestione remota Windows. A scopo di test, viene utilizzato un certificato autofirmato. In un ambiente di produzione, è consigliabile usare un certificato firmato.

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

## <a name="create-a-powershell-function-app-in-the-portal"></a>Creare un'app per le funzioni di PowerShell nel portaleCreate a PowerShell function app in the portal

La funzionalità Connessioni ibride del servizio app è disponibile solo nei piani tariffari Basic, Standard e Isolated. Quando si crea l'app per le funzioni con PowerShell, creare o selezionare uno di questi piani.

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Crea una risorsa** nel menu a sinistra e quindi selezionare App **per**le funzioni .

1. Per **Piano di hosting**, selezionare Piano di servizio **app**, quindi **Piano/Posizione app**.

1. Selezionare **Crea nuovo**, digitare il nome di un **piano di servizio app,** scegliere una **località** in un'area vicina all'utente o vicino ad altri servizi a cui accedono le funzioni e quindi selezionare **Livello tariffario**. [region](https://azure.microsoft.com/regions/)

1. Scegliere il piano S1 Standard e quindi selezionare **Applica**.

1. Selezionare **OK** per creare il piano, quindi configurare le restanti impostazioni **dell'app per** le funzioni come specificato nella tabella immediatamente dopo la schermata seguente:

    ![App per le funzioni di PowerShell CorePowerShell Core function app](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome dell'app** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z`, `0-9` e `-`.  | 
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **Gruppo di risorse** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. È inoltre possibile utilizzare il valore suggerito. |
    | **Os** | Sistema operativo preferito | Selezionare Windows. |
    | **Stack di runtime** | Lingua preferita | Scegliere PowerShell Core.Choose PowerShell Core. |
    | **Archiviazione** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È possibile anche usare un account esistente.
    | **Application Insights** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione, è possibile modificare il nome della **nuova risorsa** o scegliere una **posizione** diversa in un'area geografica di [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si desidera archiviare i dati. |

1. Dopo aver convalidato le impostazioni, selezionare **Crea**.

1. Selezionare l'icona **Notifica** nell'angolo superiore destro del portale e attendere il messaggio "Distribuzione riuscita".

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Creare una connessione ibrida per l'app per le funzioniCreate a hybrid connection for the function app

Le connessioni ibride vengono configurate dalla sezione networking dell'app per le funzioni:

1. Selezionare la scheda **Funzionalità della piattaforma** nell'app per le funzioni, quindi selezionare **Rete**. 
   ![Panoramica dell'app per la rete della piattaforma](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selezionare **Configura endpoint connessioni ibride**.
   ![Rete](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selezionare **Aggiungi connessione ibrida**.
   ![Connessione ibrida](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Immettere le informazioni sulla connessione ibrida come illustrato subito dopo la schermata seguente. È possibile fare in modo che l'impostazione **Host endpoint** corrisponda al nome host del server locale per semplificare la memorizzazione del server in un secondo momento quando si eseguono comandi remoti. La porta corrisponde alla porta predefinita del servizio di gestione remota Windows definita in precedenza nel server.
  ![Aggiunta della connessione ibrida](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Nome connessione ibrida**: ContosoHybridOnPremisesServer
    
    **Host endpoint**: finance1
    
    **Porta endpoint**: 5986Endpoint Port : 5986
    
    **Spazio dei nomi Servicebus**: Crea nuovoServicebus namespace : Create New
    
    **Località**: Scegliere una posizione disponibile
    
    **Nome**: contosopowershellhybrid

5. Selezionare **OK** per creare la connessione ibrida.

## <a name="download-and-install-the-hybrid-connection"></a>Scaricare e installare la connessione ibrida

1. Selezionare **Scarica gestione connessione** per salvare il file con estensione msi in locale nel computer.
![Scaricare il programma di installazione](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Copiare il file con estensione msi dal computer locale al server locale.
1. Eseguire il programma di installazione di Connection Manager ibrido per installare il servizio nel server locale.
![Installare la connessione ibridaInstall Hybrid Connection](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Dal portale aprire la connessione ibrida e quindi copiare la stringa di connessione gateway negli Appunti.From the portal, open the hybrid connection and then copy the gateway connection string to the clipboard.
![Copia stringa di connessione ibrida](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Aprire l'interfaccia utente di Gestione connessione ibrida nel server locale.
![Aprire l'interfaccia utente di Connessione ibridaOpen Hybrid Connection UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selezionare il pulsante **Immetti manualmente** e incollare la stringa di connessione dagli Appunti.
![Incolla connessione](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Riavviare Gestione connessione ibrida da PowerShell se non viene visualizzato come connesso.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Creare un'impostazione dell'app per la password di un account amministratore

1. Selezionare la scheda **Funzionalità della piattaforma** nell'app per le funzioni.
1. In **Impostazioni generali**selezionare **Configurazione**.
![Selezionare la configurazione della piattaforma](./media/functions-hybrid-powershell/select-configuration.png)  
1. Espandere **Nuova impostazione applicazione** per creare una nuova impostazione per la password.
1. Assegnare un nome all'impostazione _ContosoUserPassword_e immettere la password.
1. Selezionare **OK** e quindi salvare per memorizzare la password nell'applicazione funzione.
![Aggiungi impostazione app per la password](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Creare un trigger di funzione http da testareCreate a function http trigger to test

1. Creare una nuova funzione trigger HTTP dall'app per le funzioni.
![Crea nuovo trigger HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Sostituire il codice PowerShell dal modello con il codice seguente:Replace the PowerShell code from the template with the following code:

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

3. Selezionare **Salva** ed **esegui** per testare la funzione.
![Testare l'app per le funzioni](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Gestione di altri sistemi in locale

È possibile utilizzare il server locale connesso per connettersi ad altri server e sistemi di gestione nell'ambiente locale. In questo modo è possibile gestire le operazioni del data center da Azure usando le funzioni di PowerShell.This lets you manage your datacenter operations from Azure by using your PowerShell functions. Lo script seguente registra una sessione di configurazione di PowerShell che viene eseguita con le credenziali fornite. Queste credenziali devono essere destinate a un amministratore dei server remoti. È quindi possibile utilizzare questa configurazione per accedere ad altri endpoint nel server locale o nel data center.

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

Sostituire le seguenti variabili in questo script con i valori applicabili dell'ambiente:
* $HybridEndpoint
* $RemoteServer

Nei due scenari precedenti è possibile connettere e gestire gli ambienti locali usando PowerShell in Funzioni di Azure e Connessioni ibride. Ti invitiamo a saperne di più sulle [connessioni ibride](../app-service/app-service-hybrid-connections.md) e [PowerShell nelle funzioni.](./functions-reference-powershell.md)

È anche possibile usare le reti virtuali di Azure per connettersi all'ambiente locale tramite Funzioni di Azure.You can also use Azure [virtual networks](./functions-create-vnet.md) to connect to your on-premises environment through Azure Functions.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Altre informazioni sull'uso delle funzioni di PowerShellLearn more about working with PowerShell functions](functions-reference-powershell.md)
