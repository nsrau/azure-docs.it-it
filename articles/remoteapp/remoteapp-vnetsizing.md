---
title: Informazioni sul ridimensionamento di una rete virtuale in Azure RemoteApp | Documentazione Microsoft
description: Informazioni sui requisiti relativi agli indirizzi IP per Azure RemoteApp in esecuzione con una rete virtuale
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b6e1c4ba-0236-42b2-bced-69353bf211be
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9375981db64ec4a1ae523e958423b5f5787cec33
ms.lasthandoff: 03/31/2017


---
# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informazioni sul ridimensionamento di una rete virtuale in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Quando si usa Azure RemoteApp con una rete virtuale (VNET), RemoteApp usa gli indirizzi IP all'interno della subnet. In base alla scala del servizio RemoteApp, è necessario assicurarsi che la subnet abbia indirizzi IP sufficienti per le macchine virtuali RemoteApp. Queste indicazioni di ridimensionamento non sono complete se si osserva in che modo RemoteApp esegue dinamicamente lo spin-up e lo spin-down delle macchine virtuali all'interno di una raccolta, tuttavia consentono di stimare l'intervallo di subnet. Ciò è particolarmente importante perché, quando si include un servizio RemoteApp in una rete virtuale, non è possibile aumentare le dimensioni della subnet senza rimuovere RemoteApp.

Per ogni raccolta RemoteApp da eseguire alla capacità massima devono essere disponibili 100 indirizzi IP. Ad esempio, se si ha una raccolta RemoteApp nel piano Standard e si vuole avere un numero massimo di 500 utenti, sono necessari 100 indirizzi IP per la raccolta. Analogamente, in un piano Basic con 800 utenti sono necessari 100 indirizzi IP per una raccolta RemoteApp. Se si prevede un numero minore di utenti (inferiore al numero massimo), è possibile ridurre gli indirizzi IP per ogni raccolta. Il requisito minimo per le dimensioni della subnet è di 30 indirizzi IP (/27).

Consultare le informazioni seguenti per assicurarsi che la rete virtuale sia configurata e funzioni correttamente:

* [Come eseguire la migrazione da una rete virtuale personale a una rete virtuale di Azure](remoteapp-migratevnet.md)
* [Convalidare la rete virtuale di Azure da usare con Azure RemoteApp](remoteapp-vnet.md)


