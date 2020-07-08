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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970468"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integrazione del Container Registry di Azure con il Centro sicurezza

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Se si usa il livello standard del Centro sicurezza di Azure, è possibile aggiungere il bundle dei registri contenitori. Questa funzionalità facoltativa offre una maggiore visibilità sulle vulnerabilità delle immagini nei registri basati su ARM. Abilitare o disabilitare il bundle a livello di sottoscrizione per coprire tutti i registri in una sottoscrizione. Questa funzionalità viene addebitata per ogni immagine, come illustrato nella [pagina dei prezzi](security-center-pricing.md). L'abilitazione del bundle dei registri contenitori garantisce che il Centro sicurezza sia pronto per l'analisi delle immagini che vengono inserite nel registro di sistema. 


## <a name="availability"></a>Disponibilità

- Stato versione: **disponibilità generale**
- Ruoli necessari: **Reader di sicurezza** e [ruolo di lettore di container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Cloud: 
    - ✔ Cloud commerciali
    - Cloud ✘ US Government
    - Cloud ✘ Cina per enti pubblici, altri cloud gov


## <a name="when-are-images-scanned"></a>Quando vengono analizzate le immagini?

Ogni volta che viene eseguito il push di un'immagine nel registro, il Centro sicurezza analizza automaticamente tale immagine. Per attivare l'analisi di un'immagine, eseguirne il push nel repository.

Al termine dell'analisi (in genere dopo circa 10 minuti, ma può essere fino a 40 minuti), i risultati sono disponibili come raccomandazioni del Centro sicurezza, come indicato di seguito:

[![Esempio di raccomandazione del Centro sicurezza di Azure sulle vulnerabilità individuate in un'immagine ospitata di Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Il Centro sicurezza identifica i registri ACR basati su ARM nella sottoscrizione e fornisce facilmente:

* **Analisi delle vulnerabilità native di Azure** per tutte le immagini Linux sottomesse a push. Il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys. Questa soluzione nativa è integrata per impostazione predefinita.

* **Raccomandazioni sulla sicurezza** per le immagini Linux con vulnerabilità note. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Inoltre, vengono fornite indicazioni su come correggere le vulnerabilità specifiche presenti in ogni immagine di cui è stato eseguito il push nel registro di sistema.

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>Domande frequenti su ACR con Centro sicurezza

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quali tipi di immagini possono essere analizzati dal centro sicurezza di Azure?
Il Centro sicurezza analizza le immagini basate sul sistema operativo Linux che forniscono l'accesso alla Shell. 

Lo scanner Qualys non supporta immagini estremamente minimaliste come immagini [Scratch di Docker](https://hub.docker.com/_/scratch/) o immagini "senza distribuzione" che contengono solo l'applicazione e le relative dipendenze di runtime senza gestione pacchetti, Shell o sistema operativo.

### <a name="how-does-azure-security-center-scan-an-image"></a>In che modo il Centro sicurezza di Azure esegue l'analisi di un'immagine?
Viene eseguito il pull dell'immagine dal registro di sistema. Viene quindi eseguito in una sandbox isolata con lo scanner Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Con quale frequenza il Centro sicurezza di Azure analizza le immagini?
Le analisi delle immagini vengono attivate a ogni push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati dell'analisi tramite l'API REST?
Sì. I risultati si trovano nell' [API REST delle sottovalutazioni](/rest/api/securitycenter/subassessments/list/). È anche possibile usare Azure Resource Graph (ARG), l'API simile a kusto per tutte le risorse: una query può recuperare un'analisi specifica.
 



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) : descrive le raccomandazioni del Centro sicurezza
