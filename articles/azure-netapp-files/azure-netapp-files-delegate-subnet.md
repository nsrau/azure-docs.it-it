---
title: Delegare una subnet ad Azure NetApp Files | Microsoft Docs
description: Descrive come delegare una subnet ad Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: b-juche
ms.openlocfilehash: 5f36e40091ada27f411adc2ffa78b6d4a58f8cca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791409"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegare una subnet ad Azure NetApp Files 

È necessario delegare una subnet ad Azure NetApp Files.   Quando si crea un volume, è necessario specificare la subnet delegata.

## <a name="considerations"></a>Considerazioni
* La procedura guidata per la creazione di una nuova subnet una maschera di rete /24, che serve 251 indirizzi IP disponibili. Per il servizio, è sufficiente l'uso di una maschera di rete /28, la quale serve 16 indirizzi IP utili.
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.   
   Azure consente di creare più subnet delegate in un VNet.  Tuttavia, eventuali tentativi di creare un nuovo volume avranno esito negativo se si utilizza più di una subnet delegata.  
   In un VNet è possibile avere una sola subnet delegata. Un account NetApp può distribuire volumi in più reti virtuali, ognuno dei quali ha una propria subnet delegata.  
* Non è possibile designare un gruppo di sicurezza di rete o un endpoint di servizio nella subnet delegata. In questo modo, la delegazione della subnet ha esito negativo.
* L'accesso a un volume da una rete virtuale con peering globale non è attualmente supportato.
* La creazione di [route personalizzate definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) in subnet VM con prefisso di indirizzo (destinazione) a una subnet delegata a Azure NetApp files non è supportata. Questa operazione influirà sulla connettività della macchina virtuale.

## <a name="steps"></a>Passaggi 
1.  Andare al pannello **Reti virtuali** nel portale di Azure e selezionare la rete virtuale che si desidera usare per Azure NetApp Files.    

1. Selezionare **Subnet** dal pannello Rete virtuale e fare clic sul pulsante **+Subnet**. 

1. Creare una nuova subnet destinata all'uso per Azure NetApp Files completando i seguenti campi obbligatori nella pagina Aggiungi subnet:
    * **Nome**: specificare il nome della subnet.
    * **Intervallo di indirizzi**: specificare l'intervallo di indirizzi IP.
    * **Delega subnet**: selezionare **Microsoft. NetApp/Volumes**. 

      ![Delegazione subnet](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
È anche possibile creare e delegare una subnet quando si [crea un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passaggi successivi  
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informazioni sull'integrazione della rete virtuale per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


