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
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488899"
---
1. Individuare la rete WAN virtuale creata. Nella pagina della rete WAN virtuale selezionare **Hub** nella sezione **Connettività**.
2. Nella pagina Hub selezionare **+Nuovo hub** per aprire la pagina **Crea hub virtuale**.

    ![Nozioni di base](./media/virtual-wan-tutorial-hub-include/basics.png "Nozioni di base")
3. Nella scheda **Generale** della pagina **Crea hub virtuale** completare i campi seguenti:

    **Dettagli del progetto**

   * Regione (precedentemente denominata Posizione)
   * NOME
   * Spazio di indirizzi privato dell'hub. Lo spazio di indirizzi minimo per la creazione di un hub è /24; qualsiasi intervallo compreso tra /25 e /32 genererà pertanto un errore durante la creazione.
4. Selezionare **Avanti: Da sito a sito**.

    ![Da sito a sito](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Da sito a sito")

5. Nella scheda **Da sito a sito** completare i campi seguenti:

   * Scegliere **Sì** per creare una VPN da sito a sito.
   * Il campo Numero AS non è attualmente modificabile nell'hub virtuale.
   * Nell'elenco a discesa selezionare il valore **Unità di scala gateway**. L'unità di scala consente di scegliere la velocità effettiva aggregata del gateway VPN creato nell'hub virtuale a cui si collegano i siti. Se si sceglie l'unità di scala 1 = 500 Mbps, verranno create due istanze per la ridondanza, ciascuna con una velocità effettiva massima di 500 Mbps. Se, ad esempio, si hanno cinque rami, ognuno dei quali contribuisce per 10 Mbps, sarà necessaria una velocità aggregata di 50 Mbps all'estremità finale. La capacità aggregata del gateway VPN di Azure deve essere pianificata dopo aver valutato la capacità necessaria per supportare il numero di rami che confluiscono nell'hub.
6. Selezionare **Rivedi e crea** per convalidare la selezione.
7. Fare clic su **Crea** per creare l'hub. Dopo 30 minuti, fare clic su **Aggiorna** per visualizzare l'hub nella pagina **Hub**. Selezionare **Vai alla risorsa** per passare alla risorsa.