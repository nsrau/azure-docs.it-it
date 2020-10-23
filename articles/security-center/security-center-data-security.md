---
title: Sicurezza dei dati nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come i dati vengono gestiti e protetti nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 49533947ff01aea07eaacd9d761b6414fb672a1c
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339476"
---
# <a name="azure-security-center-data-security"></a>Sicurezza dei dati nel Centro sicurezza di Azure

Per consentire ai clienti di impedire, rilevare e rispondere alle minacce, il Centro sicurezza di Azure raccoglie ed elabora dati correlati alla sicurezza, tra cui informazioni di configurazione, metadati, log eventi e altro ancora. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

Questo articolo illustra come vengono gestiti e protetti i dati nel Centro sicurezza.

## <a name="data-sources"></a>Origini dati
Il Centro sicurezza analizza i dati provenienti dalle origini seguenti per offrire visibilità sullo stato della sicurezza, identificare le vulnerabilità e suggerire le mitigazioni, nonché rilevare le minacce attive:

- **Servizi di Azure**: usa le informazioni sulla configurazione dei servizi di Azure distribuiti comunicando con il provider di risorse del servizio.
- **Traffico di rete**: usa i metadati del traffico di rete campionati provenienti dall'infrastruttura di Microsoft, ad esempio l'IP/porta di origine/destinazione, le dimensioni del pacchetto e il protocollo di rete.
- **Soluzioni partner**: usa gli avvisi di sicurezza provenienti dalle soluzioni partner integrate, ad esempio firewall e soluzioni antimalware.
- **Macchine virtuali**: usa i dettagli della configurazione e informazioni sugli eventi di sicurezza, ad esempio log di controllo e registri eventi di Windows, e messaggi syslog provenienti dalle macchine virtuali.


## <a name="data-protection"></a>Protezione dei dati

### <a name="data-segregation"></a>Separazione dei dati
i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio.

### <a name="data-access"></a>Accesso ai dati
Per offrire raccomandazioni sulla sicurezza e individuare le potenziali minacce, il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi di Azure, tra cui eventi di creazione di processi e altri artefatti, che potrebbero accidentalmente contenere dati del cliente o dati personali provenienti dai computer. 

Vengono applicate le condizioni del [supplemento sulla protezione dei dati di Microsoft Online Services](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), che specifica che Microsoft non userà i dati del cliente o ne ricaverà informazioni per scopi pubblicitari o commerciali simili. Microsoft userà i dati dei clienti solo se necessari per fornire i servizi di Azure, incluse le finalità compatibili con la fornitura di tali servizi. L'utente conserva tutti i diritti sui dati dei clienti.

### <a name="data-use"></a>Uso dei dati
Microsoft usa modelli e intelligence per le minacce trovati in più tenant per migliorare le funzionalità di prevenzione e rilevamento, in base alle garanzie relative alla privacy descritte nell'[informativa sulla privacy](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Gestire la raccolta di dati dai computer
Quando si abilita il Centro sicurezza in Azure, viene attivata la raccolta dati per ogni sottoscrizione di Azure. È anche possibile abilitare la raccolta di dati per le sottoscrizioni nel Centro sicurezza. Se la raccolta di dati è abilitata, il Centro sicurezza effettua il provisioning dell'agente di Log Analytics in tutte le macchine virtuali di Azure supportate esistenti e in quelle nuove che vengono create.

L'agente di Log Analytics esegue l'analisi delle varie configurazioni correlate alla sicurezza e ne genera gli eventi nelle tracce di [Event Trace for Windows (ETW)](/windows/win32/etw/event-tracing-portal). Il sistema operativo genererà anche eventi del log eventi durante l'esecuzione del computer. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant. L'agente di Log Analytics legge le voci del log eventi ed ETW le traccia e le copia nelle aree di lavoro per l'analisi. L'agente di Log Analytics abilita anche gli eventi di creazione del processo e il controllo della riga di comando.

Se non si usa Azure Defender, è anche possibile disabilitare la raccolta dati dalle macchine virtuali nei criteri di sicurezza. La raccolta dati è necessaria per le sottoscrizioni protette da Azure Defender. Gli snapshot dei dischi delle VM e la raccolta di elementi resteranno abilitati anche se la raccolta dati è stata disabilitata.

È possibile specificare l'area di lavoro e l'area geografica in cui vengono archiviati i dati raccolti dai computer. L'impostazione predefinita prevede l'archiviazione dei dati raccolti dai computer nell'area di lavoro più vicina, come illustrato nella tabella seguente:

| Area geografica VM                                      | Area geografica area di lavoro  |
|---------------------------------------------|----------------|
| Stati Uniti, Brasile, Sudafrica         | Stati Uniti  |
| Canada                                      | Canada         |
| Europa (escluso il Regno Unito)           | Europa         |
| Regno Unito                              | Regno Unito |
| Asia (esclusi India, Giappone, Corea, Cina) | Asia Pacifico   |
| Corea del Sud                                       | Asia Pacifico   |
| India                                       | India          |
| Giappone                                       | Giappone          |
| Cina                                       | Cina          |
| Australia                                   | Australia      |
|                                             |                |

> [!NOTE]
> **Azure Defender per Archiviazione** archivia gli artefatti a livello di area in base alla località della risorsa di Azure correlata. Per altre informazioni, vedere [Introduzione ad Azure Defender per Archiviazione](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Utilizzo dei dati

I clienti possono accedere ai dati relativi al Centro sicurezza dai flussi di dati seguenti:


| STREAM                                                                                | Tipi di dati                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Log attività di Azure](../azure-monitor/platform/activity-log.md)                       | Tutti gli avvisi di sicurezza, le richieste di accesso [JIT](security-center-just-in-time.md) del Centro sicurezza approvate e tutti gli accessi generati dai [controlli applicazioni adattivi](security-center-adaptive-application.md).|
| [Log di Monitoraggio di Azure](../azure-monitor/platform/data-platform.md)                      | Tutti gli avvisi di sicurezza.                                                                                                                                                                                                |
| [Diagramma delle risorse di Azure](../governance/resource-graph/overview.md)                      | Avvisi di sicurezza, raccomandazioni sulla sicurezza, risultati della valutazione delle vulnerabilità, informazioni sul punteggio di sicurezza, stato dei controlli di conformità e altro ancora.                                                                       |
| [API REST di Centro sicurezza di Azure](/rest/api/securitycenter/) | Avvisi di sicurezza, raccomandazioni sulla sicurezza e altro ancora.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Passaggi successivi

Questo documento ha illustrato come i dati vengono gestiti e protetti nel Centro sicurezza di Azure. 

Per altre informazioni sul Centro sicurezza di Azure, vedere [Che cos'è il Centro sicurezza di Azure?](security-center-introduction.md)