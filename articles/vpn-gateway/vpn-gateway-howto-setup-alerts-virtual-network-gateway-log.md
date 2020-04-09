---
title: 'Gateway VPN di Azure: configurare gli avvisi per gli eventi del log di diagnosticaAzure VPN Gateway: Configure alerts on diagnostic log events'
description: Passaggi per configurare gli avvisi per gli eventi del log di diagnostica del gateway VPNSteps to configure alerts on VPN Gateway diagnostic log events
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878902"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurare gli avvisi per gli eventi del log di diagnostica dal gateway VPNSet up alerts on diagnostic log events from VPN Gateway

Questo articolo illustra come configurare gli avvisi in base agli eventi del log di diagnostica dal gateway VPN di Azure usando Azure Log Analytics.This article helps you set up alerts based on diagnostic log events from Azure VPN Gateway using Azure Log Analytics. 

I log seguenti sono disponibili in Azure:The following logs are available in Azure:

|***Nome*** | ***Descrizione*** |
|---        | ---               |
|GatewayDiagnosticLog | Contiene i log di diagnostica per gli eventi di configurazione del gateway, le modifiche primarie e gli eventi di manutenzione |
|TunnelDiagnosticLog | Contiene gli eventi di modifica dello stato del tunnel. Gli eventi di connessione/disconnessione del tunnel hanno un motivo riepilogativa della modifica dello stato, se applicabile |
|RouteDiagnosticLog | Registra le modifiche alle route statiche e agli eventi BGP che si verificano nel gateway |
|IKEDiagnosticLog | Registra i messaggi e gli eventi di controllo IKE nel gateway |
|P2SDiagnosticLog | Registra i messaggi e gli eventi di controllo da punto a sito nel gateway. Le informazioni sull'origine della connessione vengono fornite solo per le connessioni IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Configurare gli avvisi nel portale di Azure

La procedura di esempio seguente consente di creare un avviso per un evento di disconnessione che prevede un tunnel VPN da sito a sito:The following example steps will create an alert for a disconnection event that involves a site-to-site VPN tunnel:


1. Nel portale di Azure cercare **Log Analytics** in Tutti **i servizi** e selezionare Aree di lavoro di **Log Analytics.**

   ![Selezioni per l'accesso alle aree di lavoro di Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selezionare Crea nella pagina **Log Analytics.Select** **Create** on the Log Analytics page.

   ![Pagina Log Analytics con il pulsante Crea](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. Selezionare **Crea nuovo** e immettere i dettagli.

   ![Dettagli per la creazione di un'area di lavoro di Log AnalyticsDetails for creating a Log Analytics workspace](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. Individuare il gateway VPN nel pannello **monitoraggio** > **delle impostazioni di Diagnostica.**

   ![Selezioni per trovare il gateway VPN in Impostazioni di diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Select")

5. Per attivare la diagnostica, fare doppio clic sul gateway e quindi selezionare **Attiva diagnostica**.

   ![Selezioni per l'attivazione della diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Select")

6. Compilare i dettagli e assicurarsi che **l'opzione Invia a Log Analytics** e **TunnelDiagnosticLog** sia selezionata. Scegliere l'area di lavoro di Log Analytics creata nel passaggio 3.

   ![Caselle di controllo selezionate](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

   > [!NOTE]
   > La visualizzazione iniziale dei dati potrebbe richiedere alcune ore.

7. Passare alla panoramica della risorsa gateway di rete virtuale e selezionare Avvisi nella scheda **Monitoraggio.Go** to the overview for the virtual network gateway resource and select **Alerts** from the Monitoring tab. Creare quindi una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una nuova regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

   ![da punto a sito](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Selezionare l'area di lavoro Log Analytics e la risorsa.

   ![Selezioni per l'area di lavoro e le risorse](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. Selezionare **Ricerca log personalizzata** come logica del segnale in **Aggiungi condizione**.

   ![Selezioni per una ricerca log personalizzata](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Select")

10. Immettere la query seguente nella casella di testo Query di **ricerca.** Sostituire i valori in <> e TimeGenerated in base alle esigenze.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Impostare il valore di soglia su 0 e selezionare **Fatto**.

    ![Immissione di una query e selezione di una soglia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. Nella pagina **Crea regola** selezionare **Crea nuova** nella sezione GRUPPI **di azione.** Compilare i dettagli e selezionare **OK**.

    ![Dettagli per un nuovo gruppo di azioni](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. Nella pagina **Crea regola** immettere i dettagli per **Personalizza azioni** e assicurarsi che nella sezione NOME **GRUPPO azione** venga visualizzato il nome corretto. Selezionare **Crea regola di avviso** per creare la regola.

    ![Selezioni per la creazione di una regola](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configurare gli avvisi tramite PowerShellSet up alerts by using PowerShell

La procedura di esempio seguente consente di creare un avviso per un evento di disconnessione che prevede un tunnel VPN da sito a sito.

1. Creare un'area di lavoro di Log Analytics:Create a Log Analytics workspace:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Attivare la diagnostica per il gateway VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Creare un gruppo di azioni.

   Questo codice crea un gruppo di azioni che invia una notifica tramite posta elettronica quando viene attivato un avviso:This code creates an action group that sends an e-mail notification when an alert is triggered:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Creare una regola di avviso basata su una ricerca log personalizzata:Create an alert rule based on a custom log search:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Passaggi successivi

Per configurare avvisi sulle metriche del tunnel, vedere [Impostare avvisi per le metriche del gateway VPN.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)
