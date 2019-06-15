---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150285"
---
1. Accedere al portale di Azure e selezionare **Crea una risorsa**. Verrà visualizzata la pagina **Nuovo**.

2. Nel campo **Cerca nel Marketplace** immettere *gateway rete virtuale* e quindi selezionare **Gateway di rete virtuale** nell'elenco dei risultati della ricerca. 

3. Nella parte inferiore della pagina **Gateway di rete virtuale** selezionare **Crea** per aprire la pagina **Crea gateway di rete virtuale**.

   ![Campi nella pagina Crea gateway di rete virtuale](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Campi nella pagina Crea gateway di rete virtuale")

4. Nella pagina **Crea gateway di rete virtuale** specificare i valori per il gateway di rete virtuale:

   - **Nome**: immettere un nome per l'oggetto gateway che si sta creando. Questo nome è diverso da quello della subnet del gateway. 

   - **Tipo di gateway**: selezionare **VPN** per i gateway VPN. 

   - **Tipo VPN**: selezionare il tipo di VPN specificato per la configurazione. La maggior parte delle configurazioni richiede un tipo di VPN **basato su route**.

   - **SKU**: selezionare lo SKU del gateway dall'elenco a discesa. Gli SKU disponibili nell'elenco a discesa dipendono dal tipo di VPN selezionato. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Selezionare **Abilita modalità attiva-attiva** solo se si sta creando una configurazione di gateway attiva-attiva, altrimenti lasciare l'impostazione non selezionata.
  
   - **Località**: può essere necessario scorrere la schermata per trovare l'elenco a discesa **Località**. In **Località** impostare la località in cui si trova la rete virtuale. Ad esempio, **Stati Uniti occidentali**. Se come località non si imposta l'area in cui si trova la rete virtuale, quest'ultima non viene visualizzata nell'elenco a discesa quando si seleziona una rete virtuale.

   - **Rete virtuale**: Scegliere la rete virtuale a cui si vuole aggiungere il gateway. Selezionare **Rete virtuale** per aprire la pagina **Scegli rete virtuale** ed eseguire la selezione. Se la rete virtuale non viene visualizzata, verificare che il campo **Località** faccia riferimento all'area in cui si trova la rete virtuale.

   - **Intervallo di indirizzi subnet del gateway**: questa impostazione viene visualizzata solo se in precedenza non si è creata una subnet del gateway per la rete virtuale. Se in precedenza si è creata una subnet del gateway valida, questa impostazione non viene visualizzata.

   - **Indirizzo IP pubblico**: questa impostazione specifica l'oggetto indirizzo IP pubblico associato al gateway VPN. L'indirizzo IP pubblico viene assegnato dinamicamente a questo oggetto durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione *dinamica* degli indirizzi IP pubblici. Ciò non significa tuttavia che l'indirizzo IP venga modificato dopo l'assegnazione al gateway VPN. L'unica volta in cui l'indirizzo IP pubblico viene modificato è quando il gateway viene eliminato e creato di nuovo. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.
    
      - Lasciare **Crea nuovo** selezionato.

      - Nella casella di testo immettere un nome per l'indirizzo IP pubblico.

   - **Configura ASN BGP**: lasciare deselezionata, a meno che la configurazione non richieda specificamente questa impostazione. Se è questa impostazione necessaria, il numero ASN predefinito è *65515* e può essere modificato.
     
5. Verificare le impostazioni e selezionare **Crea** per iniziare a creare il gateway VPN. Le impostazioni vengono convalidate e nel dashboard verrà visualizzato il riquadro **Distribuzione di Gateway di rete virtuale**. La creazione di un gateway può richiedere fino a 45 minuti. Potrebbe essere necessario aggiornare la pagina del portale per visualizzare lo stato di completamento.

6. Dopo aver creato il gateway, verificare l'indirizzo IP assegnato visualizzando la rete virtuale nel portale. Il gateway viene visualizzato come un dispositivo connesso. È possibile selezionare il dispositivo connesso, ovvero il gateway di rete virtuale, per visualizzare altre informazioni.