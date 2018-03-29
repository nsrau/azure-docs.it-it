---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 932aab3f16a571d4c83c77c1cc2274ae60ea3d35
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
1. Nel portale fare clic su **+Aggiungi** in **Tutte le risorse**.
2. Nella casella di ricerca della pagina **Tutto** digitare **Gateway di rete locale** e quindi fare clic per ottenere un elenco di risorse. Fare clic su **Gateway di rete locale** per aprire la pagina e quindi su **Crea** per aprire la pagina **Crea un gateway di rete locale**.

  ![Creare il gateway di rete locale](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Nella pagina **Crea un gateway di rete locale** specificare i valori per il gateway di rete locale.

  - **Nome:** specificare un nome per l'oggetto gateway di rete locale. Se possibile, usare un nome intuitivo come **ClassicVNetLocal** o **TestVNet1Local**. In questo modo si potrà individuare più facilmente il gateway di rete locale nel portale.
  - **Indirizzo IP:** specificare un **indirizzo IP** pubblico valido per il dispositivo VPN o il gateway di rete virtuale a cui ci si vuole connettere.

    * **Se la rete locale rappresenta una posizione locale**, specificare l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere. Non può essere protetto da NAT e deve essere raggiungibile da Azure.
    * **Se la rete locale rappresenta un'altra rete virtuale**, specificare l'indirizzo IP pubblico assegnato al gateway di rete virtuale per tale rete virtuale.
    * **Se non si dispone ancora dell'indirizzo IP**, è possibile creare un indirizzo IP segnaposto valido e modificare l'impostazione in un secondo momento prima di connettersi.
  - **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si connette.
  - **Configura le impostazioni BGP:** usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
  - **Sottoscrizione:** verificare che sia visualizzata la sottoscrizione corretta.
  - **Gruppo di risorse:** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
  - **Località:** selezionare la località in cui verrà creato questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.
4. Fare clic su **Crea** per creare il gateway di rete locale.