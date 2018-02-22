---
title: Monitorare i gateway VPN con la risoluzione dei problemi di Azure Network Watcher | Microsoft Docs
description: "Questo articolo illustra come diagnosticare la connettività locale con Automazione di Azure e Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3c6efbc5da37a2ac500a575419263c0f21cca4b7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorare i gateway VPN con la risoluzione dei problemi di Network Watcher

Ottenere informazioni approfondite sulle prestazioni di rete è fondamentale per offrire servizi affidabili ai clienti. È quindi essenziale rilevare rapidamente le interruzioni di rete e adottare misure correttive per mitigare la condizione di interruzione. Automazione di Azure consente di implementare ed eseguire un'attività a livello di codice attraverso i runbook. Con Automazione di Azure è possibile mettere in atto una soluzione continua e attiva di monitoraggio e avviso relativa alla rete.

## <a name="scenario"></a>Scenario

Lo scenario nell'immagine seguente è un'applicazione a più livelli, con connettività locale stabilita tramite un gateway VPN e un tunnel. Per prestazioni ottimali delle applicazioni è importante assicurarsi che il Gateway VPN sia in funzione.

Viene creato un runbook con uno script per il controllo dello stato della connessione del tunnel VPN, usando l'API di risoluzione dei problemi delle risorse per verificare lo stato della connessione del tunnel. Se lo stato non è integro, viene inviato un trigger di posta elettronica agli amministratori.

![Esempio dello scenario][scenario]

Questo scenario illustrerà come:

- Creare un runbook chiamando il cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` per risolvere lo stato di connessione.
- Collegare una pianificazione al runbook.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questo scenario, sono necessari i prerequisiti seguenti:

- Un account di automazione di Azure in Azure. Assicurarsi che l'account di automazione abbia i moduli più recenti e anche il modulo AzureRM.Network. Il modulo AzureRM.Network è disponibile nella raccolta di moduli, se è necessario aggiungerlo all'account di automazione.
- Un set di credenziali configurato in Automazione di Azure. Per altre informazioni, vedere l'articolo relativo alla [sicurezza in Automazione di Azure](../automation/automation-security-overview.md).
- Un server SMTP valido, che sia di Office 365, della posta elettronica in locale o altro, e credenziali definite in Automazione di Azure.
- Un gateway di rete virtuale configurato in Azure.
- Un account di archiviazione esistente con un contenitore esistente in cui archiviare i log.

> [!NOTE]
> L'infrastruttura mostrata nell'immagine precedente è a scopo illustrativo e non è stata creata con la procedura descritta in questo articolo.

### <a name="create-the-runbook"></a>Creare il runbook

Il primo passaggio per la configurazione dell'esempio consiste nel creare il runbook. Questo esempio usa un account RunAs. Per altre informazioni sugli account RunAs, vedere [Autenticare runbook con account RunAs di Azure](../automation/automation-create-runas-account.md).

### <a name="step-1"></a>Passaggio 1

Passare ad Automazione di Azure nel [portale di Azure](https://portal.azure.com) e fare clic su **Runbook**.

![Panoramica dell'account di Automazione][1]

### <a name="step-2"></a>Passaggio 2

Fare clic su **Aggiungi runbook** per avviare il processo di creazione del runbook.

![Pannello Runbook][2]

### <a name="step-3"></a>Passaggio 3

In **Creazione rapida** fare clic su **Crea un nuovo runbook** per creare il runbook.

![Pannello Aggiungi runbook][3]

### <a name="step-4"></a>Passaggio 4

In questo passaggio si attribuisce un nome al runbook, che nell'esempio è denominato **Get-VPNGatewayStatus**. È importante attribuire al runbook un nome descrittivo che segua gli standard di denominazione di PowerShell. Il tipo di runbook per questo esempio è **PowerShell**, le altre opzioni disponibili sono Grafico, Flusso di lavoro PowerShell e Flusso di lavoro PowerShell grafico.

![Pannello Runbook][4]

### <a name="step-5"></a>Passaggio 5

In questo passaggio viene creato il runbook. L'esempio di codice seguente contiene tutto il codice necessario per l'esempio. Sostituire gli elementi nel codice che contengono \<value\> con i valori della sottoscrizione usata.

Usare il codice seguente e fare clic su **Salva**.

```PowerShell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Passaggio 6

Dopo aver salvato il runbook, è necessario collegarlo a una pianificazione per automatizzare l'avvio del runbook. Per avviare il processo, fare clic su **Pianificazione**.

![Passaggio 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Collegare una pianificazione al runbook.

È necessario creare una nuova pianificazione. Fare clic su **Collegare una pianificazione al runbook**.

![Passaggio 7][7]

### <a name="step-1"></a>Passaggio 1

Nel pannello **Pianificazione** fare clic su **Crea una nuova pianificazione**.

![Passaggio 8][8]

### <a name="step-2"></a>Passaggio 2

Nel pannello **Nuova pianificazione** immettere le informazioni sulla pianificazione. Di seguito sono elencati i valori che è possibile impostare.

- **Nome**: nome descrittivo della pianificazione.
- **Descrizione**: descrizione della pianificazione.
- **Inizia**: questo valore è una combinazione di data, ora e fuso orario che costituiscono l'orario di attivazione della pianificazione.
- **Ricorrenza**: questo valore determina la ripetizione della pianificazione.  I valori validi sono **Una sola volta** o **Ricorrente**.
- **Ricorre ogni**: intervallo di ricorrenza della pianificazione espresso in ore, giorni, settimane o mesi.
- **Imposta scadenza**: questo valore determina se la pianificazione debba scadere o meno. I valori validi sono **Sì** o **No**. Sì è necessario specificare data e ora valide.

> [!NOTE]
> Se un runbook specifico deve essere eseguito con una frequenza superiore a un'ora, è necessario creare più pianificazioni a intervalli diversi, vale a dire a 15, 30, 45 minuti dopo l'ora.

![Passaggio 9:][9]

### <a name="step-3"></a>Passaggio 3

Fare clic su Salva per salvare la pianificazione del runbook.

![Passaggio 10][10]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come integrare la risoluzione dei problemi di Network Watcher con Automazione di Azure, è possibile apprendere come attivare le acquisizioni pacchetti per gli avvisi di macchina virtuale. Vedere in proposito l'articolo relativo alla [creazione di un'acquisizione pacchetti attivata da un avviso mediante Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
