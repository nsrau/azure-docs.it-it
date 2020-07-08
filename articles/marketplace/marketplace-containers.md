---
title: Guida alla pubblicazione per le offerte di contenitori in Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare le offerte di contenitori in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: fae11343332fe741301016d4871d098b0214c7f5
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964448"
---
# <a name="publishing-guide-for-container-offers"></a>Guida alla pubblicazione per le offerte di contenitori

I contenitori offrono supporto per la pubblicazione dell'immagine del contenitore in Azure Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Le offerte di contenitori sono offerte di transazioni distribuite e fatturate tramite Azure Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare il tipo di offerta del *contenitore* quando la soluzione è un'immagine del contenitore Docker configurata come istanza del servizio contenitore di Azure basata su Kubernetes. 

> [!NOTE]
> Esempi di istanze del servizio contenitore di Azure basate su Kubernetes sono il servizio Azure Kubernetes o istanze di contenitore di Azure, la scelta dei clienti di Azure per un runtime di contenitori basato su Kubernetes.  

Microsoft supporta attualmente i modelli di licenza BYOL (Bring Your Own License) e gratuiti.

## <a name="container-offer-requirements"></a>Requisiti delle offerte Contenitore

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita.<br><br> |  
| Immagine compilata da un Dockerfile | Le immagini del contenitore devono essere basate sulla specifica dell'immagine Docker e compilate da un Dockerfile.<br> <br>Per ulteriori informazioni sulla compilazione di immagini Docker, vedere la sezione relativa all'utilizzo di [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting in un repository di Azure Container Registry | Le immagini del contenitore devono essere ospitate in un repository di Azure Container Registry.<br> <br>Per altre informazioni sull'uso di Azure Container Registry, vedere [Guida introduttiva: creare un registro contenitori privato usando il portale di Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Tag di immagine | Le immagini del contenitore devono contenere almeno un tag (numero massimo di Tag: 16).<br><br>Per ulteriori informazioni sull'assegnazione di tag a un'immagine, vedere la `docker tag` pagina nel sito della [documentazione di Docker](https://docs.docker.com/engine/reference/commandline/tag) .<br><br> |  

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, scoprire come [Aumentare il business sul cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Per eseguire la registrazione e iniziare a usare il Centro per i partner:

- [Accedere al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare l'offerta.
- Per altre informazioni, vedere [creare un'offerta di contenitore di Azure](./partner-center-portal/create-azure-container-offer.md) .
