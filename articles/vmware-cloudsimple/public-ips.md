---
title: Soluzione VMware di Azure di CloudSimple-allocare indirizzi IP pubblici
description: Viene descritto come allocare indirizzi IP pubblici per le macchine virtuali nell'ambiente cloud privato
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024297"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allocare indirizzi IP pubblici per l'ambiente del cloud privato

Aprire la scheda indirizzi IP pubblici nella pagina rete per assegnare indirizzi IP pubblici per le macchine virtuali nell'ambiente cloud privato.

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md) e selezionare **rete** dal menu laterale.
2. Selezionare **indirizzi IP pubblici**.
3. Fare clic su **nuovo IP pubblico**.

    ![Pagina degli indirizzi IP pubblici](media/public-ips-page.png)

4. Immettere un nome per identificare la voce dell'indirizzo IP.
5. Mantieni il percorso predefinito.
6. Usare il dispositivo di scorrimento per modificare il timeout di inattività, se necessario.
7. Immettere l'indirizzo IP locale per cui si vuole assegnare un indirizzo IP pubblico.
8. Immettere un nome DNS associato.
9. Fare clic su **Submit** (Invia).

![Allocare indirizzi IP pubblici](media/network-public-ip-allocate.png)

Viene avviata l'attività di allocazione dell'indirizzo IP pubblico. È possibile controllare lo stato dell'attività nella pagina **attività >** attività. Al termine dell'allocazione, la nuova voce viene visualizzata nella pagina indirizzi IP pubblici.
