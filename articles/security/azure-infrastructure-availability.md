---
title: Disponibilità dell'infrastruttura di Azure
description: L'articolo descrive i livelli di ridondanza per garantire la massima disponibilità dei dati dei clienti.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902578"
---
# <a name="azure-infrastructure-availability"></a>Disponibilità dell'infrastruttura di Azure
Azure offre una solida disponibilità basata su un'estesa ridondanza ottenuta grazie alla tecnologia di virtualizzazione nonché numerosi livelli di ridondanza per garantire la massima disponibilità dei dati dei clienti.

## <a name="temporary-outages-and-natural-disaster"></a>Interruzioni temporanee e disastri naturali
Il team delle operazioni e dell'infrastruttura Microsoft Cloud progetta, crea, opera e protegge l'infrastruttura cloud. Tale team assicura che l'infrastruttura di Azure offra disponibilità e affidabilità elevate, alta efficienza, scalabilità intelligente e un ambiente cloud protetto, privato e attendibile.

Gruppi di continuità sicuri e vasti banchi di batterie garantiscono che l'alimentazione non venga interrotta anche se si verifica un'interruzione a breve termine.

I generatori di emergenza forniscono alimentazione di backup in caso di interruzioni estese e per la manutenzione pianificata. Il data center è gestito con riserve di combustibile sul posto disponibili nel caso in cui si verifichi una calamità naturale.

Robuste reti in fibra ottica ad alta velocità connettono i data center ad altri hub principali e agli utenti Internet. Nodi di calcolo ospitano i carichi di lavoro più vicini agli utenti finali per ridurre la latenza, fornire ridondanza geografica e aumentare la resilienza complessiva del servizio. Un team di tecnici opera 24 ore su 24 per garantire che i servizi siano sempre disponibili per i clienti.

Microsoft garantisce disponibilità elevata tramite funzioni di monitoraggio avanzato, risposta agli eventi imprevisti, supporto del servizio e funzionalità di failover del backup. I centri operativi Microsoft distribuiti geograficamente funzionano tutto l'anno 24 ore su 24, 7 giorni su 7. La rete di Azure è una delle più grandi al mondo. La rete in fibra ottica e di distribuzione del contenuto connette i data center e i nodi perimetrali per garantire affidabilità e prestazioni elevate.

## <a name="disaster-recovery"></a>Ripristino di emergenza
Azure mantiene la durabilità dei dati dei clienti in due ubicazioni e offre al cliente la possibilità di scegliere l'ubicazione dei sito di backup. In entrambe le ubicazioni Azure gestisce costantemente più repliche (3) integre (3) dei dati dei clienti.

## <a name="database-availability"></a>Disponibilità del database
Azure garantisce che un database sia accessibile in Internet tramite un gateway Internet, assicurando un'elevata disponibilità del database. Il monitoraggio valuta l'integrità e lo stato dei database attivi a intervalli di 5 minuti.

## <a name="storage-availability"></a>Disponibilità dell'archiviazione
Azure offre funzioni di archiviazione grazie a un servizio durevole e scalabile e a endpoint di connettività in modo da essere accessibile direttamente da un'applicazione consumer. Tramite il servizio di archiviazione, le richieste di archiviazione in ingresso vengono elaborate in modo efficiente con l'integrità transazionale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
