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
ms.openlocfilehash: 0ca7bfb276f49da720264305a92d31e81857cfd5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229313"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integrazione di Azure Container Registry con Centro sicurezza (anteprima)

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Per una maggiore visibilità delle vulnerabilità del registro di sistema e delle immagini, gli utenti del livello standard del Centro sicurezza di Azure possono abilitare il bundle facoltativo dei registri contenitori. Per altre informazioni, vedere la pagina relativa ai [prezzi](security-center-pricing.md). Con il bundle abilitato, il Centro sicurezza analizza automaticamente le immagini nel registro di sistema ogni volta che viene eseguito il push di un'immagine nel registro.

> [!NOTE]
> La prima analisi del Centro sicurezza di un registro si verificherà solo dopo l'abilitazione del bundle dei registri contenitori e il push di un'immagine nel registro di sistema.

Al termine dell'analisi, in genere dopo circa 10 minuti, i risultati sono disponibili nelle raccomandazioni del Centro sicurezza, come indicato di seguito:

[![raccomandazione del Centro sicurezza di Azure di esempio sulle vulnerabilità individuate in un'immagine ospitata in Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

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