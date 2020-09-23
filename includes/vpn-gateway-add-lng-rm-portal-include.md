---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025373"
---
1. Nel portale fare clic su **+Crea una risorsa**.
2. Nella casella di ricerca digitare **Gateway di rete locale**, quindi premere **INVIO** per eseguire la ricerca. Verrà restituito un elenco di risultati. Fare clic su **Gateway di rete locale**, quindi sul pulsante **Crea** per aprire la pagina **Crea un gateway di rete locale**.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="Creare il gateway di rete locale":::

3. Nella pagina **Crea un gateway di rete locale** specificare i valori per il gateway di rete locale.

   - **Nome:** specificare un nome per l'oggetto gateway di rete locale.
   - **Endpoint:** Selezionare il tipo di endpoint per il dispositivo VPN locale- **indirizzo IP** o **FQDN (nome di dominio completo)**.
      - **Indirizzo IP**: se si dispone di un indirizzo IP pubblico statico allocato dal provider di servizi Internet per il dispositivo VPN, selezionare l'opzione indirizzo IP e immettere l'indirizzo IP come illustrato nell'esempio. Si tratta dell'indirizzo IP pubblico del dispositivo VPN a cui si vuole connettere il gateway VPN di Azure. Se al momento non è disponibile l'indirizzo IP, è possibile usare i valori visualizzati nell'esempio, ma sarà necessario tornare indietro e sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del dispositivo VPN. In caso contrario, Azure non sarà in grado di connettersi.
      - **FQDN**: se si dispone di un indirizzo IP pubblico dinamico che può cambiare dopo un determinato periodo di tempo, in genere determinato dal provider di servizi Internet, è possibile usare un nome DNS costante con un servizio DNS dinamico per puntare all'indirizzo IP pubblico corrente del dispositivo VPN. Il gateway VPN di Azure risolverà il nome di dominio completo per determinare l'indirizzo IP pubblico a cui connettersi. Uno screenshot seguente mostra un esempio di uso di FQDN anziché di un indirizzo IP.
   - **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. *Usare valori personalizzati se si vuole stabilire la connessione con il sito locale, non i valori mostrati nell'esempio*.
   - **Configura le impostazioni BGP:** usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
   - **Sottoscrizione:** verificare che sia visualizzata la sottoscrizione corretta.
   - **Gruppo di risorse:** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
   - **Percorso:** Selezionare la posizione in cui verrà creato l'oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.

    Si tratta della stessa pagina, ma con il nome di dominio completo evidenziato:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="Creare il gateway di rete locale-FQDN":::
   
   > [!NOTE]
   >
   > * La VPN di Azure supporta un solo indirizzo IPv4 per ogni FQDN. Se il nome di dominio viene risolto in più indirizzi IP, il gateway VPN di Azure utilizzerà il primo indirizzo IP restituito dai server DNS. Per eliminare l'incertezza, è consigliabile che il nome FQDN venga sempre risolto in un singolo indirizzo IPv4. IPv6 non è supportato.
   > * Il gateway VPN di Azure gestisce una cache DNS aggiornata ogni 5 minuti. Il gateway tenta di risolvere solo i nomi di dominio completi per i tunnel disconnessi. La reimpostazione del gateway attiverà anche la risoluzione FQDN.
   >

4. Al termine della specifica dei valori, selezionare il pulsante **Crea** per creare il gateway.
