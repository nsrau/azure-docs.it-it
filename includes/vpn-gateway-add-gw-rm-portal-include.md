---
title: includere file
description: Includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78331271"
---
1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**. 

   ![Creare una risorsa nel portale di Azure](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Nel campo **Ceca nel Marketplace** digitare 'Gateway di rete virtuale'. Individuare **Gateway di rete virtuale** nei risultati della ricerca e selezionare la voce. Nella pagina **Gateway di rete virtuale** selezionare **Crea**. Verrà visualizzata la pagina **Crea gateway di rete virtuale**.
3. Nella scheda **Informazioni di base** inserire i valori per il gateway di rete virtuale.

   ![Campi della pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Campi della pagina Crea gateway di rete virtuale")

   ![Campi della pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Campi della pagina Crea gateway di rete virtuale")

   **Dettagli del progetto**

   - **Sottoscrizione** Selezionare la sottoscrizione da usare nell'elenco a discesa.
   - **Gruppo di risorse**: questa impostazione viene compilata automaticamente quando si seleziona la rete virtuale in questa pagina.

   **Dettagli istanza**

   - **Nome**: assegnare un nome al gateway. Il nome del gateway non è uguale al nome della subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
   - **Area**: selezionare l'area in cui creare la risorsa. L'area del gateway deve essere uguale a quella della rete virtuale.
   - **Tipo di gateway**: selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**.
   - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
   - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   - **Generazione**: per informazioni sulla generazione del gateway VPN, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Rete virtuale**: scegliere la rete virtuale a cui aggiungere il gateway nell'elenco a discesa.
   - **Intervallo di indirizzi subnet del gateway**: questo campo viene visualizzato solo se la rete virtuale non include una subnet del gateway. Se possibile, impostare un intervallo /27 o più grande (/26, /25 e così via). Non è consigliabile creare un intervallo inferiore a /28. Se è già disponibile una subnet del gateway, è possibile visualizzare i relativi dettagli passando alla rete virtuale. Fare clic su **Subnet** per visualizzare l'intervallo. Se si vuole cambiare l'intervallo, è possibile eliminare l'oggetto GatewaySubnet e ricrearlo.

   **Indirizzo IP pubblico**: questa impostazione specifica l'oggetto indirizzo IP pubblico che viene associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

     - **Indirizzo IP pubblico**: Lasciare **Crea nuovo** selezionato.
     - **Nome indirizzo IP pubblico**: digitare un nome per l'istanza dell'indirizzo IP pubblico nella casella di testo.
     - **Assegnazione**: il gateway VPN supporta solo l'assegnazione dinamica.

   **Modalità attiva-attiva**: Selezionare **Abilita modalità attiva-attiva** solo se si sta creando una configurazione gateway attiva-attiva, altrimenti lasciare l'impostazione non selezionata.

   Lasciare **Configura ASN BGP** deselezionata, a meno che la configurazione non richieda specificamente questa impostazione. Se è un'impostazione necessaria, il numero ASN predefinito è 65515, anche se può essere modificato.
4. Selezionare **Rivedi e crea** per eseguire la convalida. Una volta superata la convalida, selezionare **Crea** per distribuire il gateway VPN. La creazione completa e la distribuzione di un gateway possono richiedere fino a 45 minuti. È possibile visualizzare lo stato della distribuzione nella pagina Panoramica per il gateway.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso.
