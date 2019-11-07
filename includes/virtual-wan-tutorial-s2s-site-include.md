---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488868"
---
1. Nella pagina del portale per la rete WAN virtuale, nella sezione **Connettività** selezionare **Siti VPN** per aprire la pagina Siti VPN.
2. Nella pagina **Siti VPN** fare clic su **+Crea sito**.

   ![Nozioni di base](./media/virtual-wan-tutorial-site-include/basics.png "Nozioni di base")
3. Nella pagina **Crea sito WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

    * **Regione** - Precedentemente denominata posizione. Si tratta del percorso in cui si vuole creare la risorsa del sito.
    * **Nome** - Nome che si vuole usare per fare riferimento al sito locale.
    * **Fornitore del dispositivo** - Nome del fornitore del dispositivo VPN (ad esempio: Citrix, Cisco, Barracuda). Queste informazioni possono consentire al team di Azure di comprendere meglio l'ambiente per aggiungere ulteriori possibilità di ottimizzazione in futuro o per offrire supporto per la risoluzione dei problemi.
    * **Border Gateway Protocol** - L'abilitazione implica che tutte le connessioni dal sito saranno abilitate per BGP. Verranno infine configurate le informazioni BGP per ogni collegamento dal Sito VPN nella sezione Collegamenti. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in un gateway VPN della rete virtuale di Azure. L'indirizzo del peer BGP locale non deve essere uguale all'indirizzo IP pubblico del dispositivo VPN né allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Tuttavia, non può essere un indirizzo APIPA (169.254.x.x). Specificare questo indirizzo nel sito VPN corrispondente che rappresenta la posizione. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). È sempre possibile modificare una connessione VPN per aggiornare i parametri BGP (IP del peering sul collegamento e l'AS #) dopo che l'impostazione BGP del sito VPN è stata abilitata.
    * **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato al sito locale. Questa operazione è necessaria quando BGP non è abilitato per il sito.
    * **Hub** - Hub a cui si vuole connettere il sito. Un sito può essere connesso solo agli hub con gateway VPN. Se non viene visualizzato un hub, creare prima un gateway VPN in tale hub.
4. Selezionare **Collegamenti** per aggiungere informazioni sui collegamenti fisici al ramo. Se è presente un dispositivo CPE del partner WAN virtuale, rivolgersi al partner per verificare se queste informazioni vengono scambiate con Azure nell'ambito del caricamento delle informazioni sul ramo configurato dai rispettivi sistemi.

   ![collegamenti](./media/virtual-wan-tutorial-site-include/links.png "Collegamenti")

    * **Nome collegamento** - Nome che si desidera fornire per il collegamento fisico al sito VPN. Esempio: mylink1.
    * **Nome provider** - Nome del collegamento fisico al sito VPN. Esempio: ATT, Verizon.
    * **Velocità** - Velocità del dispositivo VPN in corrispondenza della località del ramo. Esempio: 50, che indica 50 Mbps è la velocità del dispositivo VPN nella sede del ramo.
    * **Indirizzo IP** - Indirizzo IP pubblico del dispositivo locale usando questo collegamento. Facoltativamente, è possibile specificare l'indirizzo IP privato del dispositivo VPN locale che si trova dietro ExpressRoute.
5. Per eliminare o aggiungere altri collegamenti, è possibile utilizzare la casella di controllo. Sono supportati quattro collegamenti per sito VPN. Se, ad esempio, si dispone di quattro provider di servizi Internet (ISP) nella posizione del ramo, è possibile creare quattro collegamenti: uno per ogni ISP e fornire le informazioni per ogni collegamento.
6. Al termine della compilazione dei campi, selezionare **Rivedi + crea** per verificare e creare il sito.
7. Visualizzare lo stato della pagina Siti VPN. Il sito passerà a **Connessione necessaria** perché il sito non è ancora stato collegato all'hub.