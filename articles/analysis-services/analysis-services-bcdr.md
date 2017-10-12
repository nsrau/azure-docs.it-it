---
title: "Disponibilità elevata di Azure Analysis Services | Documentazione Microsoft"
description: "Assicurare la disponibilità elevata di Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 84b4c59bac1feeb8611b3a8d783d093ba073e532
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="analysis-services-high-availability"></a>Disponibilità elevata di Azure Analysis Services
Questo articolo descrive come garantire la disponibilità elevata dei server di Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantire la disponibilità elevata durante un'interruzione del servizio
Sebbene accada raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che può durare pochi minuti oppure alcune ore. La disponibilità elevata è ottenuta spesso con la ridondanza dei server. Azure Analysis Services consente di ottenere tale ridondanza tramite la creazione di server secondari e aggiuntivi in una o più aree. Per garantire che i dati e i metadati presenti sui server ridondanti creati siano sincronizzati con il server posizionato nell'area non in linea, è possibile:

* Distribuire i modelli nei server ridondanti in altre aree. Questo metodo richiede l'elaborazione dei dati in parallelo sia sul server primario sia sui server, assicurando così la sincronizzazione di tutti i server.

* Eseguire il backup dei database dal server primario e il ripristino nei server ridondanti. È possibile ad esempio automatizzare i backup notturni nell'archiviazione di Azure e il ripristino nei server ridondanti in altre aree. 

In entrambi i casi, se il server principale subisce un'interruzione, è necessario modificare le stringhe di connessione nei client di segnalazione report affinché eseguano la connessione al server del data center di un'altra area. Questa modifica deve essere presa in considerazione come soluzione estrema e solo quando si verifica un'interruzione del data center dell'area. È più probabile che un'interruzione del data center che ospita il server principale venga risolta prima che sia possibile aggiornare le connessioni in tutti i client. 



## <a name="related-information"></a>Informazioni correlate
[Backup e ripristino](analysis-services-backup.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md) 

