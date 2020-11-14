---
title: File di inclusione
description: Includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "92479615"
---
1. Nel [portale di Azure](https://portal.azure.com), in **Cerca risorse, servizi e documentazione (G+/)** digitare **gateway di rete locale**. Individuare e selezionare **gateway di rete locale** nei risultati della ricerca di **Marketplace**. Verrà visualizzata la pagina **Crea gateway di rete locale**.
1. Nella pagina **Crea un gateway di rete locale** specificare i valori per il gateway di rete locale.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Creare un gateway di rete locale con indirizzo IP":::

   * **Nome:** specificare un nome per l'oggetto gateway di rete locale.
   * **Endpoint:** selezionare il tipo di endpoint per il dispositivo VPN locale, ovvero un **indirizzo IP** o un **nome di dominio completo**.
      * **Indirizzo IP** : se è il provider di servizi Internet ha allocato un indirizzo IP pubblico statico per il dispositivo VPN, selezionare l'opzione dell'indirizzo IP e inserire l'indirizzo IP come mostrato nell'esempio. Si tratta dell'indirizzo IP pubblico del dispositivo VPN a cui si dovrà connettere il gateway VPN di Azure. Se al momento non è disponibile l'indirizzo IP, è possibile usare i valori visualizzati nell'esempio, ma sarà necessario tornare indietro e sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del dispositivo VPN. In caso contrario, Azure non sarà in grado di connettersi.
      * **FQDN:** se è disponibile un indirizzo IP pubblico dinamico che potrebbe cambiare dopo un certo periodo di tempo, in genere specificato dal provider di servizi Internet, è possibile usare un nome DNS costante con un servizio DNS dinamico da puntare all'indirizzo IP pubblico corrente del dispositivo VPN. Il gateway VPN di Azure risolverà il nome di dominio completo per determinare l'indirizzo IP pubblico a cui connettersi. 
   * **Spazio di indirizzi** fa riferimento agli intervalli di indirizzi per la rete rappresentata da questa rete locale. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati non si sovrappongano con gli intervalli di altre reti a cui ci si vuole connettere. Azure indirizzerà l'intervallo di indirizzi specificato all'indirizzo IP del dispositivo VPN locale. *Usare valori personalizzati se si vuole stabilire la connessione con il sito locale, non i valori mostrati nell'esempio*.
   * **Configura le impostazioni BGP:** usare solo quando si configura BGP. In caso contrario, non selezionare questa opzione.
   * **Sottoscrizione:** verificare che sia visualizzata la sottoscrizione corretta.
   * **Gruppo di risorse:** selezionare il gruppo di risorse che si vuole usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già creato.
   * **Località:** la località è la stessa specificata in **Area** in altre impostazioni. selezionare la località in cui verrà creato questo oggetto. È possibile, ma non necessario, selezionare la stessa località in cui risiede la rete virtuale.

   > [!NOTE]
   >
   > * La VPN di Azure supporta un solo indirizzo IPv4 per ogni FQDN. Se il nome di dominio si risolve in più indirizzi IP, il gateway VPN di Azure userà il primo restituito dai server DNS. Per eliminare l'incertezza, è consigliabile che il nome FQDN si risolva sempre in un singolo indirizzo IPv4. IPv6 non è supportato.
   > * Il gateway VPN di Azure mantiene una cache DNS che viene aggiornata ogni 5 minuti. Il gateway prova a risolvere i nomi FQDN solo per i tunnel disconnessi. Anche la reimpostazione del gateway attiverà la risoluzione dei nomi FQDN.
   >

1. Dopo aver specificato tutti i valori, selezionare il pulsante **Crea** nella parte inferiore della pagina per creare il gateway di rete locale.
