---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170098"
---
1. Nel lato sinistro della pagina del portale fare clic su **+** e digitare 'Gateway di rete virtuale' nella casella di ricerca. In **Risultati** individuare e selezionare **Gateway di rete virtuale**.
2. Nella parte inferiore della pagina "Gateway di rete virtuale" fare clic su **Crea**. Verrà visualizzata la pagina **Crea gateway di rete virtuale**.
3. Nella pagina **Crea gateway di rete virtuale** specificare i valori per il gateway di rete virtuale.

    ![Campi della pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Nuovo gateway")

   - **Nome**: Assegnare un nome al gateway. Questa operazione non è come quella utilizzata per assegnare un nome alla subnet del gateway. Si tratta del nome dell'oggetto gateway che verrà creato.
   - **Tipo di gateway**: Selezionare **VPN**. I gateway VPN usano il gateway di rete virtuale di tipo **VPN**. 
   - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN basato su route.
   - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

     Selezionare **Abilita modalità attiva-attiva** solo se si sta creando una configurazione gateway attiva-attiva, altrimenti lasciare l'impostazione non selezionata.
   - **Località**: potrebbe essere necessario scorrere la schermata per trovare la località. Modificare il campo **Località** in modo che faccia riferimento alla località in cui si trova la rete virtuale. Ad esempio, Stati Uniti occidentali. Se la località non fa riferimento all'area in cui si trova la rete virtuale, quest'ultima non verrà visualizzata nell'elenco a discesa quando si seleziona una rete virtuale nel passaggio successivo.
   - **Rete virtuale**: Scegliere la rete virtuale a cui si vuole aggiungere il gateway. Fare clic su **Rete virtuale** per aprire la pagina "Scegliere una rete virtuale". Selezionare la rete virtuale. Se la rete virtuale non viene visualizzata, verificare che il campo Località faccia riferimento all'area in cui si trova la rete virtuale.
   - **Intervallo di indirizzi subnet del gateway**: questa impostazione verrà visualizzata solo se in precedenza non è stata creata una subnet del gateway per la rete virtuale. Se in precedenza si è creata una subnet del gateway valida, questa impostazione non verrà visualizzata.
   - **Indirizzo IP pubblico**: questa impostazione specifica l'oggetto indirizzo IP pubblico che viene associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

     - Lasciare **Crea nuovo** selezionato.
     - Nella casella di testo digitare un **nome** per l'indirizzo IP pubblico.

4. Lasciare **Configura ASN BGP** deselezionata, a meno che la configurazione non richieda specificamente questa impostazione. Se è un'impostazione necessaria, il numero ASN predefinito è 65515, anche se può essere modificato.
5. Verificare le impostazioni. Se si vuole che il gateway venga visualizzato nel dashboard, è possibile selezionare **Aggiungi al dashboard** nella parte inferiore della pagina. 
6. Fare clic su **Crea** per iniziare a creare il gateway VPN. Le impostazioni verranno convalidate e nel dashboard verrà visualizzato il riquadro relativo alla distribuzione del gateway di rete virtuale. La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.
   dell'operazione.

Dopo la creazione del gateway, è possibile visualizzare l'indirizzo IP assegnato esaminando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile fare clic sul dispositivo connesso, ovvero il gateway di rete virtuale, per visualizzare altre informazioni.