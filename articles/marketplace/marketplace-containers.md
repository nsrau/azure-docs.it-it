---
title: Guida alla pubblicazione per le offerte di contenitori in Azure Marketplace
description: Questo articolo descrive i requisiti per pubblicare le offerte di contenitori in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95741662"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Guida alla pubblicazione per le offerte di contenitori di Azure

Il contenitore di Azure consente di pubblicare l'immagine del contenitore in Azure Marketplace. Utilizzare questa guida per comprendere i requisiti per questo tipo di offerta.

Le offerte di contenitori di Azure sono offerte di transazioni distribuite e fatturate tramite Azure Marketplace. L'opzione di elenco visualizzata dall'utente è "Get it Now".

Usare il tipo di offerta contenitore di Azure quando la soluzione è un'immagine del contenitore Docker configurata come istanza di contenitore di Azure basata su Kubernetes.

> [!NOTE]
> Un'istanza di contenitore di Azure è un'istanza Docker in fase di esecuzione che fornisce il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover gestire macchine virtuali e senza dover adottare un servizio di livello superiore. Le istanze di contenitore possono essere distribuite direttamente in Azure o orchestrate dai servizi di Azure Kubernetes o dal motore del servizio Kubernetes di Azure.  

Microsoft supporta attualmente i modelli di licenza BYOL (Bring Your Own License) e gratuiti.

## <a name="container-offer-requirements"></a>Requisiti delle offerte Contenitore

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita.<br><br> |  
| Immagine compilata da un Dockerfile | Le immagini del contenitore devono essere basate sulla specifica dell'immagine Docker e compilate da un Dockerfile.<br> <br>Per ulteriori informazioni sulla compilazione di immagini Docker, vedere la sezione relativa all'utilizzo di [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting in un repository di Azure Container Registry | Le immagini del contenitore devono essere ospitate in un repository di Azure Container Registry.<br> <br>Per altre informazioni sull'uso di Azure Container Registry, vedere [Guida introduttiva: creare un registro contenitori privato usando il portale di Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Assegnazione di tag alle immagini | Le immagini del contenitore devono contenere almeno un tag (numero massimo di Tag: 16).<br><br>Per ulteriori informazioni sull'assegnazione di tag a un'immagine, vedere la `docker tag` pagina nel sito della [documentazione di Docker](https://docs.docker.com/engine/reference/commandline/tag) .<br><br> |  

## <a name="next-steps"></a>Passaggi successivi

- Per preparare le risorse tecniche per un'offerta di contenitore, vedere [creare asset tecnici di contenitori di Azure](create-azure-container-technical-assets.md).

- Per creare un'offerta di contenitore di Azure, vedere [creare un'offerta di contenitore di Azure in Azure Marketplace](create-azure-container-offer.md) per altre informazioni.
