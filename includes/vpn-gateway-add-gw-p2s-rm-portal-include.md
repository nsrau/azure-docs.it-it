---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eba7dbb934bbc19ed7dc7452c15cbf22a9429bc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706758"
---
1. Nel lato sinistro del portale fare clic su **+ Crea una risorsa** e digitare "gateway di rete virtuale" nella casella di ricerca. Individuare **Gateway di rete virtuale** nei risultati della ricerca e fare clic sulla voce. Nella pagina **gateway di rete virtuale** fare clic su **Crea**. Verrà visualizzata la pagina **Crea gateway di rete virtuale**.

   ![Campi nella pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Campi nella pagina Crea gateway di rete virtuale")

   ![Campi nella pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Campi nella pagina Crea gateway di rete virtuale")
2. Nella pagina **Crea gateway di rete virtuale** inserire i valori per il gateway di rete virtuale.

   **Dettagli progetto**

   - **Sottoscrizione** Selezionare la sottoscrizione che si vuole usare dall'elenco a discesa.
   - **Gruppo di risorse**: Questa impostazione viene riempita automaticamente quando si seleziona la rete virtuale in questa pagina.

   **Dettagli istanza**

   - **Nome**: Assegnare un nome al gateway. La denominazione del gateway non equivale alla denominazione di una subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
   - **Area**: Selezionare l'area in cui si vuole creare la risorsa. L'area del gateway deve essere identica alla rete virtuale.
   - **Tipo di gateway**: Selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**. 
   - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
   - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Rete virtuale**: Scegliere la rete virtuale a cui si vuole aggiungere il gateway.

      **Intervallo di indirizzi subnet del gateway**: Questo campo viene visualizzato solo se la rete virtuale selezionata non dispone di una subnet del gateway. Inserire l'intervallo se non si ha già una subnet del gateway. Se possibile, eseguire l'intervallo/27 o più grande (/26,/25 e così via)

    **Indirizzo IP pubblico**: questa impostazione specifica l'oggetto indirizzo IP pubblico che viene associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

     - **Indirizzo IP pubblico**: Lasciare **Crea nuovo** selezionato.
     - **Nome indirizzo IP pubblico**: Nella casella di testo digitare un nome per l'istanza dell'indirizzo IP pubblico.
     - **Assegnazione**: Il gateway VPN supporta solo Dynamic.

   **Modalità attivo-attivo**: Selezionare **Abilita modalità attiva-attiva** solo se si sta creando una configurazione gateway attiva-attiva, altrimenti lasciare l'impostazione non selezionata.

   Lasciare **Configura ASN BGP** deselezionata, a meno che la configurazione non richieda specificamente questa impostazione. Se è un'impostazione necessaria, il numero ASN predefinito è 65515, anche se può essere modificato.

3. Fare clic su **Verifica + crea** per eseguire la convalida. Al termine della convalida, fare clic su **Crea** per distribuire il gateway VPN. Un gateway può richiedere fino a 45 minuti per la creazione e la distribuzione completa. È possibile visualizzare lo stato della distribuzione nella pagina Panoramica per il gateway.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso.