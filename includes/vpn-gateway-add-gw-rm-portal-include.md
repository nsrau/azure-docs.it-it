---
title: includere file
description: Includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fe95481e977d2e35c2f652b6c99b9e69ae89e2f8
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "92479576"
---
1. Nel [portale di Azure](https://portal.azure.com), in **Cerca risorse, servizi e documentazione (G+/)** digitare **gateway di rete virtuale** . Individuare e selezionare **Gateway di rete virtuale** nei risultati della ricerca.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Campo di ricerca" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. Nella pagina **Gateway di rete virtuale** selezionare **+ Aggiungi** . Verrà visualizzata la pagina **Crea gateway di rete virtuale** .

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="Campo di ricerca":::
1. Nella scheda **Informazioni di base** inserire i valori per il gateway di rete virtuale.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Campo di ricerca":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Campo di ricerca":::

   * **Sottoscrizione** Selezionare la sottoscrizione da usare nell'elenco a discesa.
   * **Gruppo di risorse** : questa impostazione viene compilata automaticamente quando si seleziona la rete virtuale in questa pagina.

   **Dettagli istanza**

   * **Nome** : assegnare un nome al gateway. Il nome del gateway non è uguale al nome della subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
   * **Area** : selezionare l'area in cui creare la risorsa. L'area del gateway deve essere uguale a quella della rete virtuale.
   * **Tipo di gateway** : selezionare **VPN** . I gateway VPN usano il gateway di rete virtuale di tipo **VPN** .
   * **Tipo VPN** : selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
   * **SKU** : selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Generazione** : per informazioni sulla generazione del gateway VPN, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Rete virtuale** : scegliere la rete virtuale a cui aggiungere il gateway nell'elenco a discesa.
   * **Intervallo di indirizzi subnet del gateway** : questo campo viene visualizzato solo se la rete virtuale non include una subnet del gateway. Se possibile, impostare un intervallo /27 o più grande (/26, /25 e così via). Non è consigliabile creare un intervallo inferiore a /28. Se è già disponibile una subnet del gateway, è possibile visualizzare i relativi dettagli passando alla rete virtuale. Fare clic su **Subnet** per visualizzare l'intervallo. Se si vuole cambiare l'intervallo, è possibile eliminare l'oggetto GatewaySubnet e ricrearlo.

   **Indirizzo IP pubblico**

   questa impostazione specifica l'oggetto indirizzo IP pubblico che viene associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

     * **Indirizzo IP pubblico** : Lasciare **Crea nuovo** selezionato.
     * **Nome indirizzo IP pubblico** : digitare un nome per l'istanza dell'indirizzo IP pubblico nella casella di testo.
     * **Assegnazione** : il gateway VPN supporta solo l'assegnazione dinamica.
     * **Abilita modalità attiva-attiva** : Selezionare **Abilita modalità attiva-attiva** solo se si sta creando una configurazione gateway attiva-attiva, In caso contrario, lasciare l'impostazione **Disabilitata** .
     * Lasciare l'opzione **Configura BGP** impostata su **Disabilitato** , a meno che la configurazione non richieda specificamente questa opzione. Se è un'impostazione necessaria, il numero ASN predefinito è 65515, anche se può essere modificato.
1. Selezionare **Rivedi e crea** per eseguire la convalida.
1. Una volta superata la convalida, selezionare **Crea** per distribuire il gateway VPN. La creazione completa e la distribuzione di un gateway possono richiedere fino a 45 minuti. È possibile visualizzare lo stato della distribuzione nella pagina Panoramica per il gateway.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso.
