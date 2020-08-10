---
title: 'Gateway VPN di Azure: configurare gli avvisi sugli eventi del log delle risorse di diagnostica'
description: Informazioni su come configurare gli avvisi in base agli eventi del log delle risorse dal gateway VPN di Azure, usando il Log Analytics di monitoraggio di Azure.
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: f8c688adda3924d0cea3c9b57d2d24ed3b3b1e68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033174"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Configurare gli avvisi per gli eventi del log delle risorse dal gateway VPN

Questo articolo consente di configurare gli avvisi in base agli eventi del log delle risorse dal gateway VPN di Azure tramite il Log Analytics di monitoraggio di Azure. 

In Azure sono disponibili i log delle risorse seguenti:

|***Nome*** | ***Descrizione*** |
|---        | ---               |
|GatewayDiagnosticLog | Contiene i log delle risorse per eventi di configurazione del gateway, modifiche primarie ed eventi di manutenzione |
|TunnelDiagnosticLog | Contiene gli eventi di modifica dello stato del tunnel. Gli eventi di connessione/disconnessione del tunnel hanno un motivo riepilogativo per la modifica dello stato, se applicabile |
|RouteDiagnosticLog | Registra le modifiche alle route statiche e agli eventi BGP che si verificano nel gateway |
|IKEDiagnosticLog | Registra gli eventi e i messaggi di controllo IKE nel gateway |
|P2SDiagnosticLog | Registra gli eventi e i messaggi di controllo da punto a sito nel gateway. Le informazioni sull'origine della connessione sono disponibili solo per le connessioni IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Configurare gli avvisi nel portale di Azure

La procedura di esempio seguente crea un avviso per un evento di disconnessione che include un tunnel VPN da sito a sito:


1. Nella portale di Azure cercare **log Analytics** in **tutti i servizi** e selezionare aree di **lavoro log Analytics**.

   ![Selezioni per passare ad aree di lavoro Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selezionare **Crea** nella pagina **log Analytics** .

   ![Pagina Log Analytics con pulsante Crea](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. Selezionare **Crea nuovo** e specificare i dettagli.

   ![Dettagli per la creazione di un'area di lavoro Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. Trovare il gateway VPN nel pannello **monitora**  >  **impostazioni di diagnostica** .

   ![Selezioni per la ricerca del gateway VPN nelle impostazioni di diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Select")

5. Per attivare la diagnostica, fare doppio clic sul gateway e quindi selezionare **attiva diagnostica**.

   ![Selezioni per l'attivazione della diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Select")

6. Inserire i dettagli e assicurarsi che sia selezionata l'opzione **Invia a log Analytics** e **TunnelDiagnosticLog** . Scegliere l'area di lavoro Log Analytics creata nel passaggio 3.

   ![Caselle di controllo selezionate](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

   > [!NOTE]
   > La visualizzazione iniziale dei dati potrebbe richiedere alcune ore.

7. Passare alla panoramica della risorsa gateway di rete virtuale e selezionare **avvisi** dalla scheda **monitoraggio** . Creare quindi una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una nuova regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

   ![da punto a sito](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Selezionare l'area di lavoro Log Analytics e la risorsa.

   ![Selezioni per area di lavoro e risorsa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. Selezionare **Ricerca log personalizzata** come logica del segnale in **Aggiungi condizione**.

   ![Selezioni per una ricerca log personalizzata](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Select")

10. Immettere la query seguente nella casella di testo della **query di ricerca** . Sostituire i valori in <> e TimeGenerated in base alle esigenze.

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

    Impostare il valore soglia su 0 e selezionare **fine**.

    ![Immissione di una query e selezione di una soglia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. Nella pagina **Crea regola** selezionare **Crea nuovo** nella sezione gruppi di **azioni** . Inserire i dettagli e fare clic su **OK**.

    ![Dettagli per un nuovo gruppo di azioni](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. Nella pagina **Crea regola** specificare i dettagli per le azioni di **personalizzazione** e verificare che nella sezione **nome gruppo di azioni** sia visualizzato il nome corretto. Selezionare **Crea regola di avviso** per creare la regola.

    ![Selezioni per la creazione di una regola](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configurare gli avvisi tramite PowerShell

La procedura di esempio seguente crea un avviso per un evento di disconnessione che include un tunnel VPN da sito a sito.

1. Creare un'area di lavoro Log Analytics:

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

3. Creare un gruppo di azione.

   Questo codice crea un gruppo di azioni che invia una notifica tramite posta elettronica quando viene generato un avviso:

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

4. Creare una regola di avviso basata su una ricerca log personalizzata:

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

Per configurare gli avvisi sulle metriche del tunnel, vedere [configurare gli avvisi per le metriche del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
