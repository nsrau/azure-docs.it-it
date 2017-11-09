---
title: Creare un servizio di bilanciamento del carico con connessione Internet - Portale di Azure classico | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico Internet nel modello di distribuzione classica mediante il portale di Azure classico
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: e07b6808f2401ac7b2b21e5f8816bac5a15b50b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) nel portale di Azure classico

> [!div class="op_single_selector"]
> * [Portale di Azure classico](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Servizi cloud di Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, la distribuzione classica e Azure Resource Manager. È importante comprendere i [modelli e strumenti di distribuzione](../azure-classic-rm.md) prima di lavorare con le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurazione del servizio di bilanciamento del carico Internet per le macchine virtuali

Per bilanciare il carico del traffico di rete Internet tra le macchine virtuali di un servizio cloud, è necessario creare un set con carico bilanciato. Questa procedura presuppone che le macchine virtuali siano già state create e che si trovino tutte nello stesso servizio cloud.

**Per configurare un set con carico bilanciato per le macchine virtuali**

1. Nel portale di Azure classico fare clic su **Macchine virtuali**e quindi sul nome di una macchina virtuale nel set con carico bilanciato.
2. Far clic su **Endpoint** e selezionare **Aggiungi**.
3. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic sulla freccia destra.
4. Nella pagina **Specificare i dettagli dell'endpoint** :

   * In **Nome**digitare un nome per l'endpoint o selezionarne uno dall'elenco di endpoint predefiniti per i protocolli comuni.
   * In **Protocol**selezionare il protocollo richiesto dal tipo di endpoint, ossia TCP o UDP.
   * In **Porta pubblica e Porta privata**digitare il numero di porta da utilizzare per la macchina virtuale. È possibile usare le regole relative a porte private e firewall sulla macchina virtuale per reindirizzare il traffico in modo appropriato per l'applicazione. La porta privata può essere uguale alla porta pubblica. Per un endpoint per il traffico Web (HTTP), è ad esempio possibile assegnare la porta 80 sia come porta pubblica che privata.

5. Selezionare **Create a load-balanced set**e quindi fare clic sulla freccia destra.
6. Nella pagina **Configura il set con carico bilanciato** immettere un nome per il set con carico bilanciato e quindi assegnare i valori per il comportamento del probe del servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico usa i probe per determinare se le macchine virtuali del set con carico bilanciato sono disponibili a ricevere traffico in ingresso.
7. Fare clic sul segno di spunta per creare l'endpoint con carico bilanciato. Sarà visualizzato **Sì** nella colonna **Nome del set con carico bilanciato** della pagina **Endpoint** relativa alla macchina virtuale.
8. Nel portale fare clic su **Macchine virtuali**, selezionare il nome di una macchina virtuale aggiuntiva nel set con carico bilanciato, fare clic su **Endpoint** e infine su **Aggiungi**.
9. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint a un set con carico bilanciato esistente**, selezionare il nome del set con carico bilanciato e fare clic sulla freccia destra.
10. Nella pagina **Specificare i dettagli dell'endpoint** digitare un nome per l'endpoint e quindi fare clic sul segno di spunta.

Per le macchine virtuali aggiuntive nel set con carico bilanciato, ripetere i passaggi da 8 a 10.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
