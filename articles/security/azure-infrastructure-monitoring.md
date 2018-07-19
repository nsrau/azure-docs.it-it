---
title: Monitoraggio dell'infrastruttura di Azure
description: Questo articolo illustra il monitoraggio della rete di produzione di Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101972"
---
# <a name="monitoring-of-azure-infrastructure"></a>Monitoraggio dell'infrastruttura di Azure   

## <a name="configuration-and-change-management"></a>Gestione della configurazione e delle modifiche
Microsoft Azure esamina e aggiorna annualmente le impostazioni di configurazione e le configurazioni di base di dispositivi hardware, software e di rete. Prima di essere inserite nell'ambiente di produzione, le modifiche vengono sviluppate, testate e approvate in un ambiente di sviluppo e/o test.

Le configurazioni di base richieste per i servizi basati su Azure vengono esaminate dal team di Azure responsabile della sicurezza e della conformità e dai team specifici dei servizi nell'ambito delle attività di test eseguite prima della distribuzione del servizio di produzione.

## <a name="vulnerability-management"></a>Gestione vulnerabilità
La gestione degli aggiornamenti di sicurezza consente di proteggere i sistemi da vulnerabilità note. Azure usa sistemi di distribuzione integrati per gestire la distribuzione e l'installazione degli aggiornamenti di sicurezza per il software Microsoft. Azure può anche attingere alle risorse di Microsoft Security Response Center (MSRC). MSRC identifica, monitora, risponde e risolve eventi imprevisti di sicurezza e vulnerabilità del cloud 24 ore su 24, ogni giorno dell'anno.

## <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità
L'analisi delle vulnerabilità viene eseguita nei sistemi operativi server, nei database e nei dispositivi di rete. Le analisi delle vulnerabilità vengono eseguite come minimo con frequenza trimestrale. Microsoft Azure si rivolge a ispettori indipendenti per eseguire test di penetrazione del limite di Microsoft Azure. Vengono anche eseguite regolarmente esercitazioni di Red Teaming, i cui risultati vengono usati per apportare miglioramenti della sicurezza.

## <a name="protective-monitoring"></a>Monitoraggio protettivo
La sicurezza in Microsoft Azure prevede requisiti specifici per il monitoraggio attivo. I team dei servizi configurano gli strumenti di monitoraggio attivo sulla base di questi requisiti. Gli strumenti di monitoraggio attivi includono l'Agente di monitoraggio (MA) e System Center Operations Manager. Questi strumenti sono configurati per inviare avvisi temporali al personale della sicurezza di Microsoft Azure in situazioni che richiedono un intervento immediato.

## <a name="incident-management"></a>Gestione di eventi imprevisti
Microsoft implementa un processo di gestione degli eventi imprevisti di sicurezza per facilitare una risposta coordinata in caso di eventi imprevisti.

Se Microsoft viene a conoscenza di accessi non autorizzati a dati di clienti archiviati all'interno di proprie apparecchiature o strutture o di accessi non autorizzati a tali apparecchiature o strutture con conseguente perdita, divulgazione o alterazione dei dati di clienti, Microsoft si attiva con le azioni seguenti:

- Informa tempestivamente il cliente dell'evento imprevisto di sicurezza
- Esamina tempestivamente l'evento imprevisto di sicurezza e fornisce al cliente informazioni dettagliate sull'evento
- Applica misure ragionevoli e immediate per limitare gli effetti e ridurre al minimo i danni derivanti dall'evento imprevisto di sicurezza.

È stata definita una struttura di gestione degli eventi imprevisti con un'attenta definizione dei ruoli e assegnazione delle responsabilità. Il team che si occupa della gestione di eventi imprevisti di sicurezza di Windows Azure è responsabile della gestione degli eventi imprevisti di sicurezza, nonché delle procedure di escalation e dell'eventuale coinvolgimento di team specializzati. Gli Azure Operations Manager sono responsabili di sovrintendere all'analisi e alla risoluzione degli eventi imprevisti di sicurezza e di privacy.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni che Microsoft esegue per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)
