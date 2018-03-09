---
title: "Gestire la capacità di memoria fisica per lo Stack di Azure | Documenti Microsoft"
description: Monitorare e gestire lo spazio di archiviazione disponibile per lo Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2017
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: 137e5d726aaedb405852c786b909b51110df1a50
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Gestire la capacità di memoria fisica per lo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

Per aumentare la capacità di memoria totale disponibile per lo Stack di Azure, è possibile aggiungere ulteriore memoria. Nello Stack di Azure del server fisico è detta anche un *nodo unità di scala*. Tutti i nodi di unità di scala che sono membri di una singola unità di scala deve essere la stessa quantità di memoria.

> [!note]  
> Prima di continuare, consultare la documentazione del produttore di hardware per verificare se una memoria Aggiorna che produttore supporta un aggiornamento di memoria fisica. Il contratto di supporto del fornitore di hardware OEM può richiedere che il fornitore esegua il posizionamento di rack server fisici e l'aggiornamento del firmware del dispositivo.

Il diagramma di flusso seguente viene illustrato il processo generale per l'aggiunta di memoria a ogni nodo di unità di scala.

![Aggiunta di memoria in ogni nodo di unità di scala](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Aggiunta di memoria a un nodo esistente
I passaggi seguenti offrono una panoramica del processo di memoria di componente. 

> [!Warning]  
Non eseguire la procedura senza fare riferimento alla documentazione fornita dall'OEM.

> [!Warning]  
Non è supportato un aggiornamento di memoria in sequenza, è necessario arrestare l'unità di scala intero.

1. Arrestare Stack Azure seguendo la procedura descritta nel [Start e stop Azure Stack](azure-stack-start-and-stop.md) articolo.
2. Aggiornamento della memoria in ogni computer fisico utilizzando la documentazione del produttore dell'hardware.
3. Avviare dello Stack di Azure utilizzando la procedura descritta nel [Start e stop Azure Stack](azure-stack-start-and-stop.md) articolo.

## <a name="next-steps"></a>Passaggi successivi

 - Per informazioni su come gestire gli account di archiviazione nello Stack di Azure per trovare, ripristinare e recuperare la capacità di archiviazione in base alle esigenze di business, vedere [gestire gli account di archiviazione in Azure Stack](azure-stack-manage-storage-accounts.md).
 - Per informazioni sull'operatore cloud Stack Azure monitora e gestisce la capacità di archiviazione della loro distribuzione dello Stack di Azure, vedere [gestire la capacità di archiviazione per lo Stack di Azure](azure-stack-manage-storage-shares.md). 