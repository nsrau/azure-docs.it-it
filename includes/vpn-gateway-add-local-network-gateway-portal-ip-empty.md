---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487090"
---
1. Dal [portale di Azure](https://portal.azure.com), in **Cerca risorse, servizi e documenti (G +/)** digitare gateway di **rete locale**. Individuare il **gateway di rete locale** in **Marketplace** nei risultati della ricerca e selezionarlo. Verrà visualizzata la pagina **Crea gateway di rete locale** .
1. Nella pagina **Crea un gateway di rete locale** specificare i valori per il gateway di rete locale.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="Creare un gateway di rete locale con l'indirizzo IP":::

   * **Nome:** specificare un nome per l'oggetto gateway di rete locale.
   * **Endpoint:** selezionare il tipo di endpoint per il dispositivo VPN locale, ovvero un **indirizzo IP** o un **nome di dominio completo**.
      * **Indirizzo IP**: se è il provider di servizi Internet ha allocato un indirizzo IP pubblico statico per il dispositivo VPN, selezionare l'opzione dell'indirizzo IP e inserire l'indirizzo IP come mostrato nell'esempio. Si tratta dell'indirizzo IP pubblico del dispositivo VPN a cui si dovrà connettere il gateway VPN di Azure. Se al momento non è disponibile l'indirizzo IP, è possibile usare i valori visualizzati nell'esempio, ma sarà necessario tornare indietro e sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del dispositivo VPN. In caso contrario, Azure non sarà in grado di connettersi.
   * **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. *Usare valori personalizzati se si vuole stabilire la connessione con il sito locale, non i valori mostrati nell'esempio*.
   * **Configura le impostazioni BGP:** usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
   * **Sottoscrizione:** verificare che sia visualizzata la sottoscrizione corretta.
   * **Gruppo di risorse:** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
   * **Località:** la località è la stessa specificata in **Area** in altre impostazioni. selezionare la località in cui verrà creato questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.

1. Dopo aver specificato tutti i valori, selezionare il pulsante **Crea** nella parte inferiore della pagina per creare il gateway di rete locale.
