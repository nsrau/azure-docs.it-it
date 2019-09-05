---
title: Configurare gli avvisi sugli eventi del log di diagnostica dal gateway VPN di Azure
description: Procedura per configurare gli avvisi sugli eventi del log di diagnostica del gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: d914c020553bace7ea5ab8898ac4093fea30e6c9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307003"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurare gli avvisi per gli eventi del log di diagnostica dal gateway VPN

Questo articolo consente di configurare gli avvisi in base agli eventi del log di diagnostica dal gateway VPN di Azure usando Azure Log Analytics. 

In Azure sono disponibili i log seguenti:

|***Nome*** | ***Descrizione*** |
|---        | ---               |
|GatewayDiagnosticLog | Contiene i log di diagnostica per eventi di configurazione del gateway, modifiche primarie ed eventi di manutenzione |
|TunnelDiagnosticLog | Contiene gli eventi di modifica dello stato del tunnel. Gli eventi di connessione/disconnessione del tunnel hanno un motivo riepilogativo per la modifica dello stato, se applicabile |
|RouteDiagnosticLog | Registra le modifiche alle route statiche e agli eventi BGP che si verificano nel gateway |
|IKEDiagnosticLog | Registra gli eventi e i messaggi di controllo IKE nel gateway |
|P2SDiagnosticLog | Registra gli eventi e i messaggi di controllo da punto a sito nel gateway |

## <a name="setup"></a>Configurare gli avvisi

Nei passaggi di esempio seguenti viene creato un avviso per un evento di disconnessione che prevede un tunnel VPN da sito a sito:


1. Nella portale di Azure cercare **log Analytics** in **tutti i servizi** e selezionare aree di **lavoro log Analytics**.

   ![Selezioni per passare ad aree di lavoro log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Crea")

2. Selezionare **Crea** nella pagina **log Analytics** .

   ![Pagina log Analytics con pulsante Crea](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Seleziona")

3. Selezionare **Crea nuovo** e specificare i dettagli.

   ![Dettagli per la creazione di un'area di lavoro log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Seleziona")

4. Trovare il gateway VPN nel pannello **monitora** > **impostazioni di diagnostica** .

   ![Selezioni per la ricerca del gateway VPN nelle impostazioni di diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Seleziona")

5. Per attivare la diagnostica, fare doppio clic sul gateway e quindi selezionare **attiva diagnostica**.

   ![Selezioni per l'attivazione della diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Seleziona")

6. Inserire i dettagli e assicurarsi che sia selezionata l'opzione **Invia a log Analytics** e **TunnelDiagnosticLog** . Scegliere l'area di lavoro Log Analytics creata nel passaggio 3.

   ![Caselle di controllo selezionate](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Seleziona")

7. Passare alla panoramica della risorsa gateway di rete virtuale e selezionare **avvisi** dalla scheda **monitoraggio** . Creare quindi una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una nuova regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Seleziona")

   ![da punto a sito](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Seleziona")
8. Selezionare l'area di lavoro Log Analytics e la risorsa.

   ![Selezioni per area di lavoro e risorsa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Seleziona")

9. Selezionare **Ricerca log personalizzata** come logica del segnale in **Aggiungi condizione**.

   ![Selezioni per una ricerca log personalizzata](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Seleziona")

10. Immettere la query seguente nella casella di testo **Query di ricerca**. Sostituire i valori in < > e TimeGenerated in base alle esigenze.

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

    Impostare il valore di soglia su 0 e **selezionare**.

    ![Immissione di una query e selezione di una soglia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Seleziona")

11. Nella pagina **Crea regola** selezionare **Crea nuovo** nella sezione gruppi di **azioni** . Inserire i dettagli e fare clic su **OK**.

    ![Dettagli per un nuovo gruppo di azioni](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Seleziona")

12. Nella pagina **Crea regola** specificare i dettagli per le azioni di **personalizzazione** e verificare che nella sezione **nome gruppo di azioni** sia visualizzato il nome corretto. Selezionare **Crea regola di avviso** per creare la regola.

    ![Selezioni per la creazione di una regola](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Seleziona")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi sulle metriche del tunnel, vedere [configurare gli avvisi per le metriche del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
