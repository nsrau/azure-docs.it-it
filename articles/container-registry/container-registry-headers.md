---
title: Archivi di registri contenitori di Azure | Documentazione Microsoft
description: Come usare gli archivi di registri contenitori di Azure per le immagini Docker
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dd4feff057269ed7106990bb63eed7fcffa2dbec
ms.contentlocale: it-it
ms.lasthandoff: 06/10/2017


---
# <a name="azure-container-registry-repositories"></a>Archivi di registri contenitori di Azure

I registri contenitori di Azure sono compatibili con una vasta gamma di servizi e agenti di orchestrazione. Per tenere traccia in modo più semplice dei servizi di origine e degli agenti da cui viene usato il servizio Registro contenitori di Azure, abbiamo iniziato a usare il campo di intestazione Docker nel file Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Visualizzazione dei repository nel portale

Le intestazioni del servizio Registro contenitori di Azure hanno il formato seguente:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud: Azure, Azure Stack o altri cloud di Azure per enti pubblici o paesi specifici. Anche se Azure Stack e i cloud per enti pubblici non sono attualmente supportati, questo parametro consente il supporto futuro.
* Service: nome del servizio.
* Optionalservicename: parametro facoltativo per i servizi con servizi secondari o per specificare un'unità SKU (ad esempio, le app Web corrispondono ad Azure/app-service/web-apps).

I partner sono invitati a usare valori di intestazione specifici per i loro servizi e agenti di orchestrazione, in modo da agevolare la raccolta dei dati di telemetria. Gli utenti possono anche modificare il valore passato all'intestazione, se necessario.

Di seguito sono indicati i valori che viene richiesto di usare ai partner del servizio Registro contenitori di Azure per il campo "X-Meta-Source-Client":

| Nome servizio              | Intestazione                                |
| ------------------------- | ------------------------------------- |
| Servizio contenitore di Azure   | azure/compute/azure-container-service |
| Servizio app - App Web    | azure/app-service/web-apps            |
| Servizio app - App per la logica  | azure/app-service/logic-apps          |
| Batch                     | azure/compute/batch                   |
| Cloud Console             | azure/cloud-console                   |
| Funzioni                 | azure/compute/functions               |
| Internet delle cose - Hub  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Machine Learning          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sui registri e i servizi e gli agenti di orchestrazione supportati](container-registry-intro.md)

