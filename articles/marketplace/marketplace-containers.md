---
title: Guida alla pubblicazione dell'offerta di contenitori per Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare contenitori nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 5eb30c65032332825d05097f86d0275b015a8929
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057583"
---
# <a name="containers-offer-publishing-guide"></a>Guida alla pubblicazione dell'offerta di contenitori

Le offerte di contenitori consentono di pubblicare l'immagine del contenitore in Azure Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio contenitore di Azure basato su Kubernetes.

>[!NOTE]
>Ad esempio, un servizio contenitore di Azure basato su Kubernetes come Azure Kubernetes Service o Istanze di contenitore di Azure, la scelta dei clienti di Azure per un runtime del contenitore basato su Kubernetes.  

Microsoft supporta attualmente i modelli di licenza BYOL (Bring Your Own License) e gratuiti.

## <a name="containers-offer"></a>Offerta di contenitori

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita. |  
| Immagine creata da Dockerfile | Le immagini del contenitore devono essere basate sulla specifica per immagini Docker e devono essere create da Dockerfile.<ul> <li>Per altre informazioni sulla creazione delle immagini Docker, consultare la sezione Usage (Utilizzo) all'indirizzo [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting nel record di controllo di accesso | Le immagini del contenitore devono essere ospitate in un repository del Registro contenitori di Azure.<ul> <li>Per altre informazioni sull'uso del Registro contenitori di Azure, visitare la pagina Avvio rapido: Creare un registro contenitori con il portale di Azure all'indirizzo [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tag di immagine | Le immagini del contenitore devono includere almeno 1 tag (numero massimo di tag: 16).<ul> <li>Per altre informazioni sui tag di immagine, visitare la pagina docker tag all'indirizzo [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale Cloud Partner](https://cloudpartner.azure.com) per creare e completare l'offerta
