---
title: Centro sicurezza di Azure e Azure Container Registry | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521865"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integrazione di Azure Container Registry con Centro sicurezza (anteprima)

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Quando si usa ACR con il livello standard del Centro sicurezza di Azure (vedere i [prezzi](security-center-pricing.md)), si ottiene una maggiore visibilità delle vulnerabilità del registro di sistema e delle immagini.

[![consigli di Azure Container Registry (ACR) nel centro sicurezza di Azure](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Il Centro sicurezza identifica i registri ACR nella sottoscrizione e fornisce facilmente:

* **Analisi delle vulnerabilità native di Azure** per tutte le immagini Linux sottomesse a push. Il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys. Questa soluzione nativa è integrata per impostazione predefinita.

* **Raccomandazioni sulla sicurezza** per le immagini Linux con vulnerabilità note. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Inoltre, vengono fornite indicazioni su come correggere le vulnerabilità specifiche presenti in ogni immagine di cui è stato eseguito il push nel registro di sistema.

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) : descrive le raccomandazioni del Centro sicurezza