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
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
1. Nel portale fare clic su **+Aggiungi** in **Tutte le risorse**. 
2. Nella casella di ricerca della pagina **Tutto** digitare **Gateway di rete locale** e quindi fare clic per avviare alla ricerca. Viene restituito un elenco. Fare clic su **Gateway di rete locale** per aprire la pagina e quindi su **Crea** per aprire la pagina **Crea un gateway di rete locale**.

  ![Creare il gateway di rete locale](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Nella pagina **Crea un gateway di rete locale** specificare i valori per il gateway di rete locale.

  - **Nome:** specificare un nome per l'oggetto gateway di rete locale.
  - **Indirizzo IP:** l'indirizzo IP pubblico del dispositivo VPN a cui si vuole connettere Azure. Specificare un indirizzo IP pubblico valido: non può essere protetto da NAT e deve essere raggiungibile da Azure. Se al momento non si dispone dell'indirizzo IP, è possibile usare i valori visualizzati nella schermata, ma sarà necessario tornare indietro e sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del dispositivo VPN. In caso contrario, Azure non sarà in grado di connettersi.
  - **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. *Usare i valori personalizzati, non i valori mostrati nello screenshot*.
  - **Configura le impostazioni BGP:** usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
  - **Sottoscrizione:** verificare che sia visualizzata la sottoscrizione corretta.
  - **Gruppo di risorse:** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
  - **Località:** selezionare la località in cui verrà creato questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.

4. Dopo aver specificato tutti i valori, fare clic su **Crea** nella parte inferiore della pagina per creare il gateway di rete locale.