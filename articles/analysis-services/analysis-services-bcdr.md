---
title: Disponibilità elevata di Azure Analysis Services | Documentazione Microsoft
description: Questo articolo descrive il modo in cui Azure Analysis Services garantisce un'elevata disponibilità durante le rotture del servizio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506754"
---
# <a name="analysis-services-high-availability"></a>Disponibilità elevata di Azure Analysis Services

Questo articolo descrive come garantire la disponibilità elevata dei server di Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantire la disponibilità elevata durante un'interruzione del servizio

Sebbene accada raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che può durare pochi minuti oppure alcune ore. La disponibilità elevata è ottenuta spesso con la ridondanza dei server. Azure Analysis Services consente di ottenere tale ridondanza tramite la creazione di server secondari e aggiuntivi in una o più aree. Per garantire che i dati e i metadati presenti sui server ridondanti creati siano sincronizzati con il server posizionato nell'area non in linea, è possibile:

* Distribuire i modelli nei server ridondanti in altre aree. Questo metodo richiede l'elaborazione dei dati in parallelo sia sul server primario sia sui server, assicurando così la sincronizzazione di tutti i server.

* [Eseguire il backup](analysis-services-backup.md) dei database dal server primario e il ripristino nei server ridondanti. È possibile ad esempio automatizzare i backup notturni nell'archiviazione di Azure e il ripristino nei server ridondanti in altre aree. 

In entrambi i casi, se il server principale subisce un'interruzione, è necessario modificare le stringhe di connessione nei client di segnalazione report affinché eseguano la connessione al server del data center di un'altra area. Questa modifica deve essere presa in considerazione come soluzione estrema e solo quando si verifica un'interruzione del data center dell'area. È più probabile che un'interruzione del data center che ospita il server principale venga risolta prima che sia possibile aggiornare le connessioni in tutti i client. 

Per evitare di dover modificare le stringhe di connessione nei client di creazione report, è possibile creare un [alias](analysis-services-server-alias.md) server per il server primario. Se il server primario si arresta, è possibile modificare l'alias in modo che punti a un server ridondante in un'altra area. È possibile automatizzare l'assegnazione dell'alias al nome del server scrivendo il codice per eseguire un controllo di integrità endpoint nel server primario. Se il controllo di integrità non riesce, lo stesso endpoint può indirizzare a un server ridondante in un'altra area. 

## <a name="related-information"></a>Informazioni correlate

[Backup e ripristino](analysis-services-backup.md)   
[Gestisci Azure Analysis Services](analysis-services-manage.md)   
[Nomi del server alias](analysis-services-server-alias.md) 

