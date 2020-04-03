---
title: Azure Security Center and Azure Container Registry
description: Informazioni sull'integrazione del Centro sicurezza di Azure con il Registro di sistema dei contenitori di AzureLearn about Azure Security Center's integration with Azure Container Registry
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
ms.openlocfilehash: 1c1b48d3715d838827f88f99fc0849d25677fdcc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585735"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integrazione del Registro di sistema contenitori di Azure con il Centro sicurezza

Azure Container Registry (ACR) è un servizio del Registro di sistema Docker privato e gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un Registro di sistema centrale. Si basa sul Registro di sistema Docker open-source 2.0.

Se usi il livello standard del Centro sicurezza di Azure, puoi aggiungere il bundle Registri contenitori. Questa funzionalità opzionale offre una visibilità più profonda delle vulnerabilità delle immagini nei registri basati su ARM. Abilitare o disabilitare il bundle a livello di sottoscrizione per coprire tutti i registri in una sottoscrizione. Questa funzione viene caricata per ogni immagine, come mostrato nella pagina dei [prezzi](security-center-pricing.md). L'abilitazione del bundle Container Registries garantisce che il Centro sicurezza sia pronto per l'analisi delle immagini che vengono inviate nel Registro di sistema. 

Ogni volta che un'immagine viene inserita nel Registro di sistema, il Centro sicurezza esegue automaticamente la scansione dell'immagine. Per attivare la scansione di un'immagine, inviala al repository.

Quando l'analisi viene completata (in genere dopo circa 10 minuti), i risultati sono disponibili nei consigli del Centro sicurezza come questo:

[![Esempio di raccomandazione del Centro sicurezza di Azure sulle vulnerabilità individuate in un'immagine ospitata nel Registro di sistema contenitore di AzureSample Azure Security Center recommendation about vulnerabilities discovered in an Azure Container Registry (ACR) hosted image](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Il Centro sicurezza identifica i registri ACR basati su ARM nell'abbonamento e fornisce senza problemi:

* Analisi delle **vulnerabilità native di Azure** per tutte le immagini Linux inserite. Il Centro sicurezza analizza l'immagine utilizzando uno scanner del fornitore leader del settore per la scansione delle vulnerabilità, Qualys. Questa soluzione nativa è perfettamente integrata per impostazione predefinita.

* **Consigli sulla sicurezza** per immagini Linux con vulnerabilità note. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Inoltre, fornisce indicazioni su come correggere le vulnerabilità specifiche rilevate in ogni immagine inserita nel Registro di sistema.

![Panoramica generale del Centro sicurezza di Azure e del registro dei contenitori di Azure (ACR)Azure Security Center and Azure Container Registry (ACR) high-level overview](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza dei contenitori del Centro sicurezza, vedere:To learn more about security features del Security Center, see:

* [Sicurezza di Azure e sicurezza dei contenitoriAzure Security Center and container security](container-security.md)

* [Integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) - Descrive i consigli del Centro sicurezzaVirtual Machine protection - Describes Security Center's recommendations