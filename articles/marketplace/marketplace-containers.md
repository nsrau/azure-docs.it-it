---
title: Guida alla pubblicazione dell'offerta di contenitori per Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare contenitori nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277307"
---
# <a name="containers-offer-publishing-guide"></a>Guida alla pubblicazione dell'offerta di contenitori

Le offerte di contenitori consentono di pubblicare l'immagine del contenitore in Azure Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio Azure Container basato su Kubernetes.

>[!NOTE]
>Ad esempio, un servizio Azure Container basato su Kubernetes come servizio Azure Kubernetes o Istanze di Azure Container, la scelta dei clienti di Azure per un runtime del contenitore basato su Kubernetes.  

Microsoft supporta attualmente i modelli di licenza BYOL (Bring Your Own License) e gratuiti.

## <a name="containers-offer"></a>Offerta di contenitori

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita. |  
| Immagine creata da Dockerfile | Le immagini del contenitore devono essere basate sulla specifica per immagini Docker e devono essere create da Dockerfile.<ul> <li>Per altre informazioni sulla creazione delle immagini Docker, consultare la sezione Usage (Utilizzo) all'indirizzo [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting nel record di controllo di accesso | Le immagini del contenitore devono essere ospitate in un repository di Registro Azure Container.<ul> <li>Per altre informazioni sull'uso di Registro Azure Container, visitare la pagina Avvio rapido: Creare un registro contenitori con il portale di Azure all'indirizzo [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tag di immagine | Le immagini del contenitore devono includere almeno 1 tag (numero massimo di tag: 16).<ul> <li>Per altre informazioni sui tag di immagine, visitare la pagina docker tag all'indirizzo [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Registrati](https://azuremarketplace.microsoft.com/sell) nel marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedi al portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare la tua offerta.
- Vedere [Contenitori](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) per altre informazioni.
