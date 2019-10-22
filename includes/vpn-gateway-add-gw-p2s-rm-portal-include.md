---
title: file di inclusione
description: file di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268901"
---
1. Nel lato sinistro del portale fare clic su **+ Crea una risorsa** e digitare "gateway di rete virtuale" nella casella di ricerca. Individuare **Gateway di rete virtuale** nei risultati della ricerca e fare clic sulla voce. Nella pagina **gateway di rete virtuale** fare clic su **Crea**. Verrà visualizzata la pagina **Crea gateway di rete virtuale**.

   ![Campi della pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Campi della pagina Crea gateway di rete virtuale")

   ![Campi della pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Campi della pagina Crea gateway di rete virtuale")
2. Nella pagina **Crea gateway di rete virtuale** inserire i valori per il gateway di rete virtuale.

   **Dettagli del progetto**

   - **Sottoscrizione**: selezionare la sottoscrizione che si vuole usare dall'elenco a discesa.
   - **Gruppo di risorse**: questa impostazione viene riempita automaticamente quando si seleziona la rete virtuale in questa pagina.

   **Dettagli istanza**

   - **Nome**: assegnare un nome al gateway. La denominazione del gateway non equivale alla denominazione di una subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
   - **Area**: selezionare l'area in cui si vuole creare la risorsa. L'area del gateway deve essere identica alla rete virtuale.
   - **Tipo di gateway**: selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**. 
   - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
   - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Rete virtuale**: scegliere la rete virtuale a cui si vuole aggiungere il gateway.

      **Intervallo di indirizzi subnet del gateway**: questo campo viene visualizzato solo se la rete virtuale selezionata non dispone di una subnet del gateway. Inserire l'intervallo se non si ha già una subnet del gateway. Se possibile, eseguire l'intervallo/27 o più grande (/26,/25 e così via)

   **Indirizzo IP pubblico**: questa impostazione specifica l'oggetto indirizzo IP pubblico che viene associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

     - **Indirizzo IP pubblico**: lasciare selezionata l'opzione **Crea nuovo** .
     - **Nome dell'indirizzo IP pubblico**: digitare un nome per l'istanza dell'indirizzo IP pubblico nella casella di testo.
     - **Assegnazione**: il gateway VPN supporta solo Dynamic.

   **Modalità attivo-attivo**: selezionare **Abilita modalità attivo-attivo** solo se si sta creando una configurazione del gateway Active-Active. altrimenti lasciare l'impostazione non selezionata.

   Lasciare **Configura ASN BGP** deselezionata, a meno che la configurazione non richieda specificamente questa impostazione. Se è un'impostazione necessaria, il numero ASN predefinito è 65515, anche se può essere modificato.

3. Fare clic su **Verifica + crea** per eseguire la convalida. Al termine della convalida, fare clic su **Crea** per distribuire il gateway VPN. Un gateway può richiedere fino a 45 minuti per la creazione e la distribuzione completa. È possibile visualizzare lo stato della distribuzione nella pagina Panoramica per il gateway.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso.