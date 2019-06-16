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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711811"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegare una subnet ad Azure NetApp Files 

È necessario delegare una subnet ad Azure NetApp Files.   Quando si crea un volume, è necessario specificare la subnet delegata.

## <a name="considerations"></a>Considerazioni
* La procedura guidata per la creazione di una nuova subnet una maschera di rete /24, che serve 251 indirizzi IP disponibili. Per il servizio, è sufficiente l'uso di una maschera di rete /28, la quale serve 16 indirizzi IP utili.
* In ogni rete virtuale di Azure è possibile delegare una sola subnet ad Azure NetApp Files.
* Non è possibile designare un gruppo di sicurezza di rete o un endpoint di servizio nella subnet delegata. In questo modo, la delegazione della subnet ha esito negativo.
* Accesso a un volume da una rete virtuale con peering globale non è attualmente supportato.
* La creazione [route personalizzate definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) nelle subnet della macchina virtuale con indirizzo prefisso (destinazione) a una subnet delegata NetApp in file di Azure non è supportata. Questa operazione influirà sulla connettività delle macchine Virtuali.

## <a name="steps"></a>Passaggi 
1.  Andare al pannello **Reti virtuali** nel portale di Azure e selezionare la rete virtuale che si desidera usare per Azure NetApp Files.    

1. Selezionare **Subnet** dal pannello Rete virtuale e fare clic sul pulsante **+Subnet**. 

1. Creare una nuova subnet destinata all'uso per Azure NetApp Files completando i seguenti campi obbligatori nella pagina Aggiungi subnet:
    * **Nome**: specificare il nome della subnet.
    * **Intervallo di indirizzi**: specificare l'intervallo di indirizzi IP.
    * **Delegazione subnet**: selezionare **Microsoft.NetApp/volumes**. 

      ![Delegazione subnet](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
È anche possibile creare e delegare una subnet quando si [crea un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passaggi successivi  
* [Creare un volume per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


