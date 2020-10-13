---
title: Delegare una subnet ad Azure NetApp Files | Microsoft Docs
description: Informazioni su come delegare una subnet a Azure NetApp Files. Specificare la subnet delegata quando si crea un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bed1375631c017d23ed53b6102c424533237099e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447563"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegare una subnet ad Azure NetApp Files 

È necessario delegare una subnet ad Azure NetApp Files.   Quando si crea un volume, è necessario specificare la subnet delegata.

## <a name="considerations"></a>Considerazioni

* La procedura guidata per la creazione di una nuova subnet una maschera di rete /24, che serve 251 indirizzi IP disponibili. L'uso di un network mask/28, che fornisce 11 indirizzi IP utilizzabili, è sufficiente per il servizio.
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.   
   Azure consente di creare più subnet delegate in un VNet.  Tuttavia, eventuali tentativi di creare un nuovo volume avranno esito negativo se si utilizza più di una subnet delegata.  
   In un VNet è possibile avere una sola subnet delegata. Un account NetApp può distribuire volumi in più reti virtuali, ognuno dei quali ha una propria subnet delegata.  
* Non è possibile designare un gruppo di sicurezza di rete o un endpoint di servizio nella subnet delegata. In questo modo, la delegazione della subnet ha esito negativo.
* L'accesso a un volume da una rete virtuale con peering globale non è attualmente supportato.
* Le [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) (UDR) e i gruppi di sicurezza di rete (gruppi) non sono supportati nelle subnet delegate per Azure NetApp files. È tuttavia è possibile applicare route definite dall'utente e gruppi di sicurezza di rete ad altre subnet, anche all'interno della stessa rete virtuale della subnet delegata ad Azure NetApp Files.  
   Azure NetApp Files crea una route di sistema alla subnet delegata. Se necessario per la risoluzione dei problemi, la route viene visualizzata in **route valide** nella tabella di route.

## <a name="steps"></a>Passaggi

1.  Passare al pannello **reti virtuali** nella portale di Azure e selezionare la rete virtuale che si vuole usare per Azure NetApp files.    

1. Selezionare **Subnet** dal pannello Rete virtuale e fare clic sul pulsante **+Subnet**. 

1. Creare una nuova subnet destinata all'uso per Azure NetApp Files completando i seguenti campi obbligatori nella pagina Aggiungi subnet:
    * **Nome**: specificare il nome della subnet.
    * **Intervallo di indirizzi**: specificare l'intervallo di indirizzi IP.
    * **Delega subnet**: selezionare **Microsoft. NetApp/Volumes**. 

      ![Delegazione subnet](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
È anche possibile creare e delegare una subnet quando si [crea un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


