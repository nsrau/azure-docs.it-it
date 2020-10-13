---
title: Azure Defender per registri contenitori - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per registri contenitori.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 12264a79ee5428e98d6cf7d37bef6706295e68dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448382"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introduzione ad Azure Defender per registri contenitori

Registro Azure Container è un servizio di registri Docker privato e gestito che archivia e gestisce le immagini del contenitore per le distribuzioni di Azure in un registro centrale. È basato sull'applicazione open source Docker Registry 2.0.

Per proteggere tutti i registri basati su Azure Resource Manager nella sottoscrizione, abilitare **Azure Defender per registri contenitori** a livello della sottoscrizione. Il Centro sicurezza analizzerà quindi le immagini che sono state inserite nel registro, importate nel registro o estratte negli ultimi 30 giorni. Questa funzionalità prevede un addebito per ogni immagine.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quali sono i vantaggi di Azure Defender per registri contenitori?

Il Centro sicurezza identifica i registri del servizio Registro Azure Container basati su Azure Resource Manager nella sottoscrizione e facilita la valutazione e la gestione delle vulnerabilità native di Azure per le immagini del registro.

**Azure Defender per registri contenitori** include una funzionalità di analisi delle vulnerabilità che analizza le immagini nei registri di Registro Azure Container basati su Azure Resource Manager e fornisce una maggiore visibilità sulle vulnerabilità delle immagini. La funzionalità di analisi integrata è fornita da Qualys, leader nel settore dell'analisi delle vulnerabilità.

Quando viene rilevato un problema, da Qualys o dal Centro sicurezza, viene visualizzata una notifica nel dashboard del Centro sicurezza. Per ogni vulnerabilità il Centro sicurezza offre raccomandazioni pratiche, una classificazione della gravità e istruzioni per la risoluzione del problema. Per informazioni dettagliate sulle raccomandazioni del Centro sicurezza per i contenitori, vedere l'[elenco di riferimento delle raccomandazioni](recommendations-reference.md#recs-containers).

Il Centro sicurezza filtra e classifica i risultati restituiti dalla funzionalità di analisi. Quando un'immagine è integra, il Centro sicurezza la contrassegna come tale. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Il Centro sicurezza fornisce informazioni dettagliate su ogni vulnerabilità segnalata, insieme a una classificazione della gravità. Fornisce inoltre indicazioni su come correggere le vulnerabilità specifiche rilevate in ogni immagine.

Inviando notifiche solo in caso di problemi individuati, il Centro sicurezza riduce il rischio di avvisi informativi indesiderati.


## <a name="when-are-images-scanned"></a>Quando vengono analizzate le immagini?

L'analisi di un'immagine viene attivata da tre trigger:

- **Inserimento**: ogni volta che un'immagine viene inserita nel registro, il Centro sicurezza la analizza automaticamente. Per attivare l'analisi di un'immagine, inserirla nel repository.

- **Estrazione recente**: poiché ogni giorno vengono rilevate nuove vulnerabilità, **Azure Defender per registri contenitori** analizza anche qualsiasi immagine estratta negli ultimi 30 giorni. Non sono previsti costi aggiuntivi per la ripetizione dell'analisi. Come accennato in precedenza, i costi vengono addebitati una sola volta per ogni immagine.

- **Importazione**: Registro Azure Container offre strumenti di importazione per inserire immagini nel registro da Docker Hub, Registro contenitori di Microsoft o un altro registro contenitori di Azure. **Azure Defender per registri contenitori** analizza le immagini supportate importate. Per altre informazioni, vedere [Importare immagini del contenitore in un registro contenitori](../container-registry/container-registry-import-images.md).
 
L'analisi viene completata in genere entro 2 minuti, ma può impiegare fino a 15 minuti. I risultati vengono resi disponibili in forma di raccomandazioni del Centro sicurezza, come la seguente:

[![Esempio di raccomandazione del Centro sicurezza di Azure sulle vulnerabilità rilevate in un'immagine ospitata da Registro Azure Container](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Funzionamento del Centro sicurezza con Registro Azure Container

Di seguito è riportato un diagramma generale dei componenti e dei vantaggi della protezione dei registri con il Centro sicurezza.

![Panoramica generale del Centro sicurezza di Azure e del servizio Registro Azure Container](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Domande frequenti sull'analisi delle immagini di Registro Azure Container

### <a name="how-does-security-center-scan-an-image"></a>Come vengono analizzate le immagini dal Centro sicurezza?
L'immagine viene estratta dal registro. Viene quindi eseguita in una sandbox isolata con la funzionalità di analisi di Qualys che estrae un elenco di vulnerabilità note.

Il Centro sicurezza filtra e classifica i risultati restituiti dalla funzionalità di analisi. Quando un'immagine è integra, il Centro sicurezza la contrassegna come tale. Il Centro sicurezza genera raccomandazioni sulla sicurezza solo per le immagini che presentano problemi da risolvere. Inviando notifiche solo in caso di problemi individuati, il Centro sicurezza riduce il rischio di avvisi informativi indesiderati.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>È possibile ottenere i risultati dell'analisi tramite l'API REST?
Sì. I risultati si trovano nell'[API REST Sub-Assessments](/rest/api/securitycenter/subassessments/list/). Si può anche usare Azure Resource Graph, l'API Kusto-per tutte le risorse: una query può recuperare un'analisi specifica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quali tipi di registri vengono analizzati? Per quali tipi vengono addebitati dei costi?
Per un elenco dei tipi di registri contenitori supportati da Azure Defender per registri contenitori, vedere [Disponibilità](defender-for-container-registries-usage.md#availability).

Se si collegano registri non supportati alla sottoscrizione di Azure, questi non vengono analizzati e non viene addebitato alcun costo.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza dei contenitori del Centro sicurezza, vedere:

- [Centro sicurezza di Azure e sicurezza dei contenitori](container-security.md)

- [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)


