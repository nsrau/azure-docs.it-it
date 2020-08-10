---
title: Centro sicurezza di Azure e Azure Container Registry
description: Informazioni su come analizzare i registri dei contenitori con il Centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 767ad928779743ec28dbe3da8be6154a1fe0b0ab
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042314"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Analisi delle immagini del Container Registry di Azure per Centro sicurezza

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Se si usa il livello standard del Centro sicurezza di Azure, è possibile aggiungere il bundle dei registri contenitori. Questa funzionalità facoltativa offre una maggiore visibilità sulle vulnerabilità delle immagini nei registri basati su Azure Resource Manager. Abilitare o disabilitare il bundle a livello di sottoscrizione per coprire tutti i registri in una sottoscrizione. Questa funzionalità viene addebitata per ogni immagine, come illustrato nella [pagina dei prezzi](security-center-pricing.md). L'abilitazione del bundle dei registri contenitori garantisce che il Centro sicurezza sia pronto per l'analisi delle immagini che vengono inserite nel registro di sistema. 

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibilità generale|
|Prezzi|Livello Standard|
|Registri e immagini supportati:|![Sì ](./media/icons/yes-icon.png) registri ACR ospitati da Linux accessibili dalla rete Internet pubblica e offrono l'accesso alla Shell.<br>![Nessun ](./media/icons/yes-icon.png) Registro ACR ospitato da Windows.<br>![Nessun ](./media/icons/yes-icon.png) registro ' privato '-il Centro sicurezza richiede che i registri siano accessibili dalla rete Internet pubblica. Il Centro sicurezza non è attualmente in grado di connettersi o analizzare i registri con accesso limitato con un firewall, un endpoint del servizio o endpoint privati come il collegamento privato di Azure.<br>![Non ci sono ](./media/icons/yes-icon.png) Immagini con minimalista, ad esempio immagini [Scratch di Docker](https://hub.docker.com/_/scratch/) o immagini "senza distribuzione" che contengono solo un'applicazione e le relative dipendenze di runtime senza gestione pacchetti, Shell o sistema operativo.|
|Ruoli e autorizzazioni necessari:|Ruolo lettore di **sicurezza** e [lettore di container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||




## <a name="when-are-images-scanned"></a>Quando vengono analizzate le immagini?

Ogni volta che viene eseguito il push di un'immagine nel registro, il Centro sicurezza analizza automaticamente tale immagine. Per attivare l'analisi di un'immagine, eseguirne il push nel repository.

Al termine dell'analisi (in genere dopo circa 2 minuti, ma può essere fino a 15 minuti), i risultati sono disponibili come raccomandazioni del Centro sicurezza, come indicato di seguito:

[![Esempio di raccomandazione del Centro sicurezza di Azure sulle vulnerabilità individuate in un'immagine ospitata di Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

Il Centro sicurezza identifica Azure Resource Manager registri ACR basati sulla sottoscrizione e fornisce facilmente:

* **Analisi delle vulnerabilità native di Azure** per tutte le immagini Linux sottomesse a push. Il Centro sicurezza esegue l'analisi dell'immagine usando uno scanner del fornitore leader del settore per l'analisi delle vulnerabilità, Qualys. Questa soluzione nativa è integrata per impostazione predefinita.

* **Raccomandazioni sulla sicurezza** per le immagini Linux con vulnerabilità note. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Inoltre, vengono fornite indicazioni su come correggere le vulnerabilità specifiche presenti in ogni immagine di cui è stato eseguito il push nel registro di sistema.

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Domande frequenti sull'analisi di immagini Container Registry di Azure

### <a name="how-does-security-center-scan-an-image"></a>In che modo il Centro sicurezza analizza un'immagine?
Viene eseguito il pull dell'immagine dal registro di sistema. Viene quindi eseguito in una sandbox isolata con lo scanner Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

### <a name="how-often-does-security-center-scan-my-images"></a>Con quale frequenza il Centro sicurezza analizza le immagini?
Le analisi delle immagini vengono attivate a ogni push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati dell'analisi tramite l'API REST?
Sì. I risultati si trovano nell' [API REST delle sottovalutazioni](/rest/api/securitycenter/subassessments/list/). È anche possibile usare Azure Resource Graph (ARG), l'API simile a kusto per tutte le risorse: una query può recuperare un'analisi specifica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quali tipi di registro vengono analizzati? Quali tipi vengono fatturati?
La [sezione relativa alla disponibilità](#availability) elenca i tipi di registri contenitori supportati dal bundle registri contenitori. 

Se i registri non supportati sono connessi alla sottoscrizione di Azure, non verranno analizzati e non verranno addebitati costi.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

* [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

* [Integrazione con il servizio Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Protezione della macchina virtuale](security-center-virtual-machine-protection.md) : descrive le raccomandazioni del Centro sicurezza
