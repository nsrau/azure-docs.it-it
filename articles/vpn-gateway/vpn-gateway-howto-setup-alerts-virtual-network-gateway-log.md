---
title: Impostare gli avvisi sugli eventi del log di diagnostica dal Gateway VPN di Azure
description: Passaggi per configurare avvisi per eventi di log di diagnostica del Gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 3880c847c54136dfd3ba1ecfe0178565091e229f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510212"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Impostare gli avvisi sugli eventi del log di diagnostica dal Gateway VPN

Questo articolo consente di impostare gli avvisi basati sugli eventi di log di diagnostica dal Gateway VPN di Azure.

## <a name="setup"></a>Impostare gli avvisi

Nell'esempio seguente creerà un avviso per un evento di disconnessione che coinvolge un tunnel VPN site-to-site:


1. Nel portale di Azure, cercare **Log Analitica** sotto **tutti i servizi** e selezionare **aree di lavoro di Log Analitica**.

   ![Le selezioni per passare alle aree di lavoro di Log Analitica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selezionare **Create** nel **Log Analitica** pagina.

   ![Pagina di Analitica di log con il pulsante di creazione](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "selezionare")

3. Selezionare **Crea nuovo** e inserire i dettagli.

   ![I dettagli per la creazione di un'area di lavoro di Log Analitica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "selezionare")

4. Trovare il gateway VPN nel **Monitor** > **le impostazioni di diagnostica** pannello.

   ![Selezioni per trovare il gateway VPN in impostazioni di diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "selezionare")

5. Per attivare la diagnostica, fare doppio clic sul gateway e quindi selezionare **attivare la diagnostica**.

   ![Selezioni per l'attivazione di diagnostica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "selezionare")

6. Specificare i dettagli e assicurarsi che **Invia a Log Analitica** e **TunnelDiagnosticLog** siano selezionate. Scegliere l'area di lavoro di Analitica Log creato nel passaggio 3.

   ![Selezionare le caselle di controllo](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "selezionare")

7. Passare alla pagina di panoramica per la risorsa gateway di rete virtuale e selezionare **Alerts** dal **Monitoring** scheda. Creare una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una nuova regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selezionare")

   ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selezionare")
8. Selezionare l'area di lavoro di Log Analitica e la risorsa.

   ![Selezioni per l'area di lavoro e risorse](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "selezionare")

9. Selezionare **ricerca log personalizzata** come la logica del segnale sotto **Aggiungi condizione**.

   ![Le selezioni per la ricerca di log personalizzato](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "selezionare")

10. Immettere la query seguente nella casella di testo **Query di ricerca**. Sostituire i valori in <> come appropriato.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Impostare il valore di soglia su 0 e **selezionare**.

    ![Immissione di una query e selezionando una soglia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "selezionare")

11. Nel **Crea regola** pagina, selezionare **Crea nuovo** sotto il **gruppi di azioni** sezione. Compilare i dettagli e selezionare **OK**.

    ![I dettagli per un nuovo gruppo di azione](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "selezionare")

12. Nel **Crea regola** pagina, immettere i dettagli per **personalizzare le azioni** e assicurarsi che il nome corretto sia visualizzato nel **nome gruppo di azione** sezione. Selezionare **Crea regola di avviso** per creare la regola.

    ![Selezioni per la creazione di una regola](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "selezionare")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi sulle metriche di tunnel, vedere [consente di impostare avvisi sulle metriche di Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
