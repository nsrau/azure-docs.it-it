---
title: Come configurare gli avvisi sugli eventi di log di diagnostica di Gateway VPN di Azure
description: Passaggi per configurare avvisi per eventi di log di diagnostica del Gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769737"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Impostare gli avvisi su eventi di log di diagnostica del Gateway VPN

Questo articolo consente di configurare gli avvisi basati su eventi di log di diagnostica del Gateway VPN.


## <a name="setup"></a>Configurare gli avvisi di monitoraggio di Azure in base agli eventi di log di diagnostica tramite il portale

La procedura di esempio seguente creerà un avviso per un evento di disconnessione del tunnel VPN site-to-site



1. Cercare "Log Analitica" in tutti i servizi e scegliere "Aree di lavoro di Log Analitica" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Nella pagina Log Analitica, fare clic su "Crea".
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "selezionare")

3. Selezionare "Crea nuovo" area di lavoro e specificare i dettagli.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "selezionare")

4. Trovare il gateway VPN in "Monitoraggio" > Pannello "Impostazioni di diagnostica" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "selezionare")

5. Per attivare la diagnostica, fare doppio clic sul gateway e quindi fare clic su "Attiva diagnostica" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "selezionare")

6. Specificare i dettagli e assicurarsi che siano selezionati "Invia a Log Analitica" e "TunnelDiagnosticLog". Selezionare l'area di lavoro di Log Analitica che è stato creato nel passaggio 3.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "selezionare")

7. Passare alla panoramica di risorsa gateway della rete virtuale e selezionare "Avvisi" nella scheda monitoraggio, quindi creare una nuova regola di avviso o modificare una regola di avviso esistente.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "selezionare")

8. Selezionare l'area di lavoro di Log Analitica e la risorsa.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "selezionare")

9. Selezionare "ricerca log personalizzata" come la logica dei segnali in "Aggiungi condizioni" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "selezionare")

10. Immettere la query seguente nella casella di testo "Query di ricerca" sostituendo i valori in <> come appropriato.

    AzureDiagnostics | in cui categoria = = "TunnelDiagnosticLog" e ID risorsa = = toupper ("<RESOURCEID OF GATEWAY>") e TimeGenerated > ago(5m) e remoteIP_s = = "<REMOTE IP OF TUNNEL>" e status_s = = "Disconnected"

    Impostare il valore di soglia su 0 e fare clic su "Fine"

    ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "selezionare")

11. Nella pagina "Crea regola", fare clic su "Crea nuovo" nella sezione gruppi di azioni. Specificare i dettagli e fare clic su OK

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "selezionare")

12. Nella pagina "Crea regola", immettere i dettagli per "Personalizzare Action" e assicurarsi che il nome del gruppo di azione corretto sia visualizzato nella sezione "Nome gruppo di azione". Fare clic su "Crea regola di avviso" per creare la regola.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "selezionare")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi sulle metriche di tunnel, vedere [come configurare gli avvisi sulle metriche di Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
