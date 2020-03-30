---
title: Soluzione Azure VMware di CloudSimple - Allocare indirizzi IP pubbliciAzure VMware Solution by CloudSimple - Allocate public IP addresses
description: Descrive come allocare indirizzi IP pubblici per le macchine virtuali nell'ambiente Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024297"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allocare indirizzi IP pubblici per l'ambiente Private Cloud

Aprire la scheda IP pubblici nella pagina Rete per allocare gli indirizzi IP pubblici per le macchine virtuali nell'ambiente Cloud privato.

1. [Accedere al portale CloudSimple](access-cloudsimple-portal.md) e selezionare **Rete** nel menu laterale.
2. Selezionare **IP pubblici**.
3. Fare clic su **Nuovo IP pubblico**.

    ![Pagina IP pubblici](media/public-ips-page.png)

4. Immettere un nome per identificare la voce dell'indirizzo IP.
5. Mantenere la posizione predefinita.
6. Utilizzare il dispositivo di scorrimento per modificare il timeout di inattività, se necessario.
7. Immettere l'indirizzo IP locale a cui si desidera assegnare un indirizzo IP pubblico.
8. Immettere un nome DNS associato.
9. Fare clic su **Submit**.

![Allocare indirizzi IP pubblici](media/network-public-ip-allocate.png)

Inizia l'attività di allocazione dell'indirizzo IP pubblico. È possibile controllare lo stato dell'attività nella pagina **Attività > Attività.** Al termine dell'allocazione, la nuova voce viene visualizzata nella pagina IP pubblici.
