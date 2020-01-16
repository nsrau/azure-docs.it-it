---
title: Rilevamento delle minacce per il cloud native Computing nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo presenta gli avvisi di calcolo nativi cloud disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024867"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Rilevamento delle minacce per il cloud native Computing nel centro sicurezza di Azure

Come soluzione nativa, il Centro sicurezza di Azure offre una visibilità univoca dei log interni per l'identificazione della metodologia di attacco in più destinazioni. Questo articolo presenta gli avvisi disponibili per i servizi di Azure seguenti:

* [Servizio app di Azure](#app-services)
* [Contenitori di Azure](#azure-containers) 

> [!NOTE]
> Questi servizi non sono attualmente disponibili in Azure per enti pubblici e aree cloud sovrane.

## Servizio app Azure<a name="app-services"></a>

Il Centro sicurezza usa la scalabilità del cloud per identificare gli attacchi destinati alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati Probe applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere indirizzati ad ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, dove vengono ispezionate e registrate. Questi dati vengono quindi usati per identificare gli exploit e gli utenti malintenzionati e per apprendere nuovi modelli che verranno usati in un secondo momento.

Grazie alla visibilità di Azure come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco in più destinazioni. La metodologia, ad esempio, include analisi generalizzate e attacchi distribuiti. Questo tipo di attacco in genere deriva da un piccolo subset di indirizzi IP e Mostra i modelli di ricerca per indicizzazione in endpoint simili su più host. Gli attacchi stanno cercando una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.

Se si sta eseguendo un piano di servizio App basato su Windows, il Centro sicurezza ha anche accesso alle macchine virtuali e alle VM sottostanti. Insieme ai dati di log indicati in precedenza, l'infrastruttura può raccontare la storia, da un nuovo attacco che circola in modo naturale a compromessi nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che un'app Web è stata sfruttata, potrebbe essere in grado di rilevare attacchi continui.

Per un elenco degli avvisi del servizio app Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).

## Contenitori di Azure<a name="azure-containers"></a>

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Le minacce vengono rilevate a livelli diversi: 

* **Livello host** : l'agente del Centro sicurezza (disponibile nel livello standard, vedere [prezzi](security-center-pricing.md) per i dettagli) monitora Linux per le attività sospette. L'agente attiva avvisi per le attività sospette provenienti dal nodo o da un contenitore in esecuzione su di esso. Esempi di attività di questo tipo includono rilevamento della shell Web e connessione con indirizzi IP sospetti noti.

    Per informazioni più dettagliate sulla sicurezza dell'ambiente in contenitori, l'agente monitora le analisi specifiche del contenitore. Attiverà gli avvisi per eventi quali la creazione di contenitori con privilegi, l'accesso sospetto ai server API e i server Secure Shell (SSH) in esecuzione all'interno di un contenitore docker.

    >[!NOTE]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo un subset dei vantaggi e degli avvisi di rilevamento delle minacce. Verranno comunque ricevuti gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

    Per un elenco degli avvisi a livello di host, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-containerhost).


* Per il **livello del cluster AKS**, il monitoraggio del rilevamento delle minacce è basato sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio senza **agente** , aggiungere l'opzione Kubernetes alla sottoscrizione dalla pagina **Impostazioni & prezzi** (vedere i [prezzi](security-center-pricing.md)). Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti da AKS usando i log recuperati da AKS. Esempi di eventi a questo livello includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di rilevamento per le azioni e le distribuzioni del servizio Kubernetes di Azure che si verificano dopo che l'opzione Kubernetes è stata abilitata nelle impostazioni della sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster AKS, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

Inoltre, il team globale dei ricercatori di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici del contenitore Man mano che vengono individuati.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui piani di servizio app, vedere [piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).