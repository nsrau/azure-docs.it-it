---
title: Centro sicurezza di Azure e Azure Container Registry
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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: bb004d536d30d62116866581daeef9b42c23d470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936370"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integrazione di Azure Container Registry con Centro sicurezza (anteprima)

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Se si usa il livello standard del Centro sicurezza di Azure, è possibile aggiungere il bundle dei registri contenitori. Questa funzionalità facoltativa offre una maggiore visibilità sulle vulnerabilità delle immagini nei registri basati su ARM. Abilitare o disabilitare il bundle a livello di sottoscrizione per coprire tutti i registri in una sottoscrizione. Questa funzionalità viene addebitata per ogni immagine, non per analisi, come illustrato nella [pagina dei prezzi](security-center-pricing.md). 

L'abilitazione del bundle dei registri contenitori garantisce che il Centro sicurezza sia pronto per l'analisi delle immagini che vengono inserite nel registro di sistema. Le analisi sono a livello di immagine: il Centro sicurezza non analizza il registro di sistema, analizza le immagini archiviate nel registro di sistema. 

Ogni volta che viene eseguito il push di un'immagine nel registro, il Centro sicurezza analizza automaticamente tale immagine. Per attivare l'analisi di un'immagine, eseguirne il push nel repository.

Una volta completata l'analisi, in genere dopo circa 10 minuti, i risultati sono disponibili nelle raccomandazioni del Centro sicurezza, ad esempio:

[![raccomandazione del Centro sicurezza di Azure di esempio sulle vulnerabilità individuate in un'immagine ospitata in Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Il Centro sicurezza identifica i registri ACR basati su ARM nella sottoscrizione e fornisce facilmente:

* **Analisi delle vulnerabilità native di Azure** per tutte le immagini Linux sottomesse a push. Il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys. Questa soluzione nativa è integrata per impostazione predefinita.

* **Raccomandazioni sulla sicurezza** per le immagini Linux con vulnerabilità note. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Inoltre, vengono fornite indicazioni su come correggere le vulnerabilità specifiche presenti in ogni immagine di cui è stato eseguito il push nel registro di sistema.

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) : descrive le raccomandazioni del Centro sicurezza
