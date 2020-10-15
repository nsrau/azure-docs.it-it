---
title: Eliminare le repliche per Azure NetApp Files la replica tra aree | Microsoft Docs
description: Viene descritto come eliminare una connessione di replica non più necessaria tra i volumi di origine e di destinazione.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708819"
---
# <a name="delete-replications"></a>Eliminare le repliche

È possibile terminare la connessione di replica tra i volumi di origine e di destinazione eliminando la replica del volume. È possibile eseguire l'operazione di eliminazione dal volume di origine o di destinazione. L'operazione Delete rimuove solo l'autorizzazione per la replica. non rimuove il volume di origine o di destinazione. 

## <a name="steps"></a>Passaggi

1. Per eliminare la replica del volume, selezionare **replica** dal volume di origine o di destinazione.  

2. Fare clic su **Elimina**.    

3. Confermare l'eliminazione digitando **Sì** e facendo clic su **Elimina**.   

    ![Elimina replica](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Risolvere i problemi relativi alla replica tra aree](troubleshoot-cross-region-replication.md)

