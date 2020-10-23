---
title: "Gateway VPN: modificare le impostazioni dell'indirizzo IP del gateway: portale di Azure"
description: Questo articolo illustra in modo dettagliato come modificare i prefissi degli indirizzi IP per il gateway di rete locale usando il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143141"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificare le impostazioni del gateway di rete locale usando il portale di Azure

In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Questo articolo illustra come modificare le impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni con un altro metodo selezionando un'opzione diversa nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configurazione del gateway di rete locale

La schermata seguente mostra la pagina di **configurazione** di una risorsa gateway di rete locale con l'endpoint di indirizzo IP pubblico:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Impostazioni dell'indirizzo IP" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Questa è la pagina di configurazione con un endpoint FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Impostazioni dell'indirizzo IP":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Per modificare l'indirizzo IP o il nome di dominio completo del gateway

> [!NOTE]
> Non è possibile modificare un gateway di rete locale tra l'endpoint FQDN e l'endpoint dell'indirizzo IP. È necessario eliminare tutte le connessioni associate a questo gateway di rete locale, crearne uno nuovo con il nuovo endpoint (indirizzo IP o FQDN), quindi ricreare le connessioni.
>

Se il dispositivo VPN a cui si vuole connettersi ha modificato l'indirizzo IP pubblico, modificare il gateway di rete locale usando la procedura seguente:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Nella casella **Indirizzo IP** modificare l'indirizzo IP.
3. Selezionare **Salva** per salvare le impostazioni.

Se il dispositivo VPN a cui si desidera connettersi ha modificato il relativo FQDN (nome di dominio completo), modificare il gateway di rete locale attenendosi alla procedura seguente:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Nella casella **FQDN** modificare il nome di dominio.
3. Selezionare **Salva** per salvare le impostazioni.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Per modificare i prefissi degli indirizzi IP

Per aggiungere altri prefissi degli indirizzi:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Aggiungere lo spazio di indirizzi IP nella casella *Aggiungi intervallo di indirizzi*.
3. Per salvare le impostazioni, fare clic su **Save** (Salva).

Per rimuovere prefissi degli indirizzi:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Selezionare **'.. .'** nella riga contenente il prefisso da rimuovere.
3. Selezionare **Rimuovi**.
4. Per salvare le impostazioni, fare clic su **Save** (Salva).

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Per modificare le impostazioni BGP

Per aggiungere o aggiornare le impostazioni BGP:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Selezionare **"Configura impostazioni BGP"** per visualizzare o aggiornare le configurazioni BGP per questo gateway di rete locale
3. Aggiungere o aggiornare il numero di sistema autonomo o l'indirizzo IP del peer BGP nei campi corrispondenti
4. Per salvare le impostazioni, fare clic su **Save** (Salva).

Per rimuovere le impostazioni BGP:

1. Nella risorsa gateway di rete locale, nella sezione **Impostazioni** , selezionare **configurazione**.
2. Deselezionare **"Configura impostazioni BGP"** per rimuovere l'indirizzo IP del peer BGP e BGP esistente
3. Per salvare le impostazioni, fare clic su **Save** (Salva).

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).
