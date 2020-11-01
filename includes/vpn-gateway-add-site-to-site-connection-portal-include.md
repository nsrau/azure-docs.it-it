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
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "92479593"
---
1. Aprire la pagina relativa al gateway di rete virtuale. È possibile accedere al gateway passando a **Nome della rete virtuale -> Panoramica -> Dispositivi connessi-> Nome del gateway** , anche se sono disponibili molti altri modi.
1. Nella pagina del gateway selezionare **Connessioni** . Nella parte superiore della pagina Connessioni selezionare **+Aggiungi** per aprire la pagina **Aggiungi connessione** .

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Connessione da sito a sito":::
1. Nella pagina **Aggiungi connessione** configurare i valori per la connessione.

   * **Nome:** assegnare un nome alla connessione.
   * **Tipo di connessione** : selezionare **Da sito a sito (IPSec)** .
   * **Gateway di rete virtuale:** questo valore è fisso perché la connessione viene eseguita da questo gateway.
   * **Gateway di rete locale:** selezionare **Scegli un gateway di rete locale** e quindi selezionare quello da usare.
   * **Chiave condivisa:** il valore di questo campo deve corrispondere a quello che si usa per il dispositivo VPN locale. L'esempio usa "abc123", ma è possibile e consigliabile usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.
   * Lasciare l'opzione **Usa indirizzo IP privato di Azure** deselezionata.
   * Lasciare l'opzione **Abilita BGP** deselezionata.
   * Selezionare **IKEv2** .
   * I restanti valori di **Sottoscrizione** , **Gruppo di risorse** e **Località** sono fissi.

1. Selezionare **OK** per creare la connessione. Sullo schermo lampeggerà il messaggio *Creazione della connessione* .
1. È possibile visualizzare la connessione nella pagina **Connessioni** relativa al gateway di rete virtuale. Lo stato passa da *Sconosciuto* a *Connessione* e quindi a *Operazione riuscita* .
