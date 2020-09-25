---
title: 'Azure Defender per registri contenitori: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per i registri di contenitori.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6254b78ad19e7034f78f7891d57a3474fee0c602
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301924"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introduzione ad Azure Defender per registri contenitori

Azure Container Registry (ACR) è un servizio di registro Docker privato gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. Si basa sul registro Docker open source 2,0.

Per proteggere tutti i registri basati su Azure Resource Manager nella sottoscrizione, abilitare **Azure Defender per i registri contenitori** a livello di sottoscrizione. Il Centro sicurezza analizzerà quindi le immagini che vengono inserite nel registro di sistema, importate nel registro di sistema o eventuali immagini estratte negli ultimi 30 giorni. Questa funzionalità viene addebitata per ogni immagine.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quali sono i vantaggi di Azure Defender per i registri di contenitori?

Il Centro sicurezza identifica Azure Resource Manager registri ACR basati sulla sottoscrizione e fornisce facilmente la valutazione e la gestione delle vulnerabilità native di Azure per le immagini del registro.

**Azure Defender per i registri contenitori** include uno scanner di vulnerabilità per analizzare le immagini nei registri di Azure container Registry basati su Azure Resource Manager e fornire una maggiore visibilità sulle vulnerabilità delle immagini. Lo scanner integrato è alimentato da Qualys, il fornitore leader del settore per l'analisi delle vulnerabilità.

Quando vengono rilevati problemi, da Qualys o dal centro sicurezza, si riceverà una notifica nel dashboard del Centro sicurezza. Per ogni vulnerabilità, il Centro sicurezza offre raccomandazioni di utilità pratica, oltre a una classificazione di gravità e indicazioni su come correggere il problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza per i contenitori, vedere l' [elenco di riferimento di raccomandazioni](recommendations-reference.md#recs-containers).

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Il Centro sicurezza fornisce i dettagli di ogni vulnerabilità segnalata e una classificazione di gravità. Vengono inoltre fornite indicazioni su come correggere le vulnerabilità specifiche rilevate in ogni immagine.

Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.


## <a name="when-are-images-scanned"></a>Quando vengono analizzate le immagini?

Sono disponibili tre trigger per un'analisi di immagine:

- **Al push** : ogni volta che viene eseguito il push di un'immagine nel registro, il Centro sicurezza analizza automaticamente tale immagine. Per attivare l'analisi di un'immagine, eseguirne il push nel repository.

- **Pull di recente** : poiché le nuove vulnerabilità vengono individuate ogni giorno, **Azure Defender per i registri contenitori** analizza anche le immagini che sono state estratte negli ultimi 30 giorni. Non sono previsti costi aggiuntivi per la ripetizione dell'analisi. come indicato in precedenza, la fatturazione viene addebitata una volta per ogni immagine.

- **In import** -Azure container Registry include strumenti di importazione per inserire le immagini nel registro dall'hub Docker, da Microsoft container Registry o da un altro registro contenitori di Azure. **Azure Defender per i registri contenitori** analizza tutte le immagini supportate importate. Per altre informazioni, vedere [importare le immagini del contenitore in un registro contenitori](../container-registry/container-registry-import-images.md).
 
L'analisi viene completata in genere entro 2 minuti, ma potrebbero essere necessari fino a 15 minuti. I risultati vengono resi disponibili come raccomandazioni del Centro sicurezza, ad esempio:

[![Esempio di raccomandazione del Centro sicurezza di Azure sulle vulnerabilità individuate in un'immagine ospitata di Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Funzionamento del Centro sicurezza con Azure Container Registry

Di seguito è riportato un diagramma di alto livello dei componenti e dei vantaggi della protezione dei registri con il Centro sicurezza.

![Panoramica di alto livello del Centro sicurezza di Azure e di Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Domande frequenti sull'analisi di immagini Container Registry di Azure

### <a name="how-does-security-center-scan-an-image"></a>In che modo il Centro sicurezza analizza un'immagine?
Viene eseguito il pull dell'immagine dal registro di sistema. Viene quindi eseguito in una sandbox isolata con lo scanner Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati dallo scanner. Quando un'immagine è integra, il Centro sicurezza le contrassegna come tali. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo quando si verificano problemi, il Centro sicurezza riduce il rischio di avvisi indesiderati informativi.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati dell'analisi tramite l'API REST?
Sì. I risultati si trovano nell' [API REST delle sottovalutazioni](/rest/api/securitycenter/subassessments/list/). È anche possibile usare Azure Resource Graph (ARG), l'API simile a kusto per tutte le risorse: una query può recuperare un'analisi specifica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quali tipi di registro vengono analizzati? Quali tipi vengono fatturati?
Per un elenco dei tipi di registri contenitori supportati da Azure Defender per i registri contenitori, vedere [disponibilità](defender-for-container-registries-usage.md#availability).

Se si connettono registri non supportati alla sottoscrizione di Azure, non verranno analizzati e non verranno addebitati costi.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle funzionalità di sicurezza del contenitore del Centro sicurezza, vedere:

- [Sicurezza del contenitore e del Centro sicurezza di Azure](container-security.md)

- [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)


