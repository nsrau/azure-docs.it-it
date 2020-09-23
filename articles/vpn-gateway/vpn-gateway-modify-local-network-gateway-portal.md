---
title: "Gateway VPN: modificare le impostazioni dell'indirizzo IP del gateway: portale di Azure"
description: Questo articolo illustra in modo dettagliato come modificare i prefissi degli indirizzi IP per il gateway di rete locale usando il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983198"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificare le impostazioni del gateway di rete locale usando il portale di Azure

In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Questo articolo illustra come modificare le impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni con un altro metodo selezionando un'opzione diversa nell'elenco seguente:

Prima di eliminare la connessione, è opportuno scaricare la configurazione dei dispositivi usati per la connessione per ottenere la chiave precondivisa definita. In questo modo non sarà necessario ridefinirla sull'altro lato della connessione.

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configurazione del gateway di rete locale

La schermata seguente mostra la pagina di **configurazione** di una risorsa gateway di rete locale con l'endpoint di indirizzo IP pubblico:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Configurare il gateway di rete locale-indirizzo IP":::

Si tratta della stessa pagina di configurazione con un endpoint FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Configurare il gateway di rete locale-FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Modificare l'indirizzo IP del gateway

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica.

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Nella casella **Indirizzo IP** modificare l'indirizzo IP.
3. Fare clic su **Salva** per salvare le impostazioni.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Modificare il nome di dominio completo del gateway

Se il nome di dominio completo (FQDN) del dispositivo VPN a cui si desidera connettersi è stato modificato, è necessario modificare il gateway di rete locale per riflettere la modifica.

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Nella casella **FQDN** modificare il nome di dominio.
3. Fare clic su **Salva** per salvare le impostazioni.

> ! Si noti Non è possibile modificare un gateway di rete locale tra l'endpoint FQDN e l'endpoint dell'indirizzo IP. È necessario eliminare tutte le connessioni associate a questo gateway di rete locale, crearne uno nuovo con il nuovo endpoint (indirizzo IP o FQDN), quindi ricreare le connessioni.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificare i prefissi degli indirizzi IP

### <a name="to-add-additional-address-prefixes"></a>Per aggiungere altri prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Aggiungere lo spazio di indirizzi IP nella casella *Aggiungi intervallo di indirizzi*.
3. Per salvare le impostazioni, fare clic su **Save** .

### <a name="to-remove-address-prefixes"></a>Per rimuovere prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Fare clic su **"..."** nella riga contenente il prefisso da rimuovere.
3. Scegliere **Rimuovi**.
4. Per salvare le impostazioni, fare clic su **Save** .

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Modificare le impostazioni BGP

### <a name="to-add-or-update-bgp-settings"></a>Per aggiungere o aggiornare le impostazioni BGP:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Selezionare **"Configura impostazioni BGP"** per visualizzare o aggiornare le configurazioni BGP per questo gateway di rete locale
3. Aggiungere o aggiornare il numero di sistema autonomo o l'indirizzo IP del peer BGP nei campi corrispondenti
4. Per salvare le impostazioni, fare clic su **Save** .

### <a name="to-remove-bgp-settings"></a>Per rimuovere le impostazioni BGP:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Annulla la selezione di **"Configura impostazioni BGP"** per rimuovere l'indirizzo IP del peer BGP e BGP esistente
3. Per salvare le impostazioni, fare clic su **Save** .

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).
