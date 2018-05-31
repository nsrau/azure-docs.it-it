---
title: Eseguire la migrazione del data center locale ad Azure | Microsoft Docs
description: White paper di carattere generale sulla migrazione dei data center locali ad Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167756"
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrazione dei carichi di lavoro locali ad Azure


Microsoft Azure offre l'accesso a un set completo di servizi cloud che vengono usati da sviluppatori e professionisti IT per creare, distribuire e gestire applicazioni in un'ampia gamma di strumenti e framework, tramite una rete globale di data center. Il cloud di Azure consente alle aziende che affrontano le sfide associate al passaggio al digitale di determinare come ottimizzare le risorse e le operazioni, interagire con clienti e dipendenti e trasformare i prodotti offerti.

Nonostante tutti i vantaggi offerti dal cloud in termini di velocità e flessibilità, riduzione al minimo dei costi, prestazioni e affidabilità, tuttavia, Azure riconosce che molte organizzazioni dovranno eseguire data center locali ancora per un certo periodo. In risposta agli ostacoli all'adozione del cloud, Azure offre una strategia di cloud ibrido che collega i data center locali e il cloud pubblico di Azure, ad esempio usando risorse del cloud di Azure come il backup per proteggere le risorse locali oppure usando le funzionalità di analisi di Azure per ottenere informazioni significative sui carichi di lavoro locali. 

Nell'ambito della strategia di cloud ibrido, Azure offre sempre più soluzioni per eseguire la migrazione al cloud di app e carichi di lavoro locali. Con semplici procedure, è possibile valutare in modo completo le risorse locali per determinare come verranno eseguite nel cloud di Azure. Con una valutazione approfondita a disposizione, si può quindi eseguire in tutta sicurezza la migrazione delle risorse ad Azure. Quando saranno operative in Azure, le risorse potranno essere ottimizzate per mantenere e migliorare l'accesso, la flessibilità, la sicurezza e l'affidabilità.

Questa serie di articoli sulla migrazione illustra come è possibile pianificare e creare una strategia di migrazione per la propria azienda. Gli articoli descrivono diversi scenari di complessità crescente, a cui ne verranno aggiunti altri nel tempo. La tabella seguente contiene un riepilogo di questi scenari. Per ognuno vengono offerte una panoramica e un'architettura di migrazione e vengono illustrate le procedure incluse nel processo di migrazione. 

**Scenario** | **Soluzione** | **Servizi** | **Articolo** 
--- | --- | --- | ---
[Scenario 1: Individuazione e valutazione](migrate-scenarios-assessment.md) | Individuare e valutare l'infrastruttura, le app e i dati locali per la migrazione in Azure. | Data Migration Assistant, servizio Azure Migrate  | Ora disponibile
**[Scenario 2: Rehosting dell'app](migrate-scenarios-lift-and-shift.md)** | Trasferimento in modalità lift-and-shift delle app in Azure. | Azure Site Recovery, Servizio Migrazione del database di Azure, istanza gestita di SQL di Azure | Ora disponibile
**Scenario 3: Refactoring dell'app** | Eseguire il refactoring delle app durante la migrazione in Azure. | In pianificazione | Pianificata
**Scenario 4: Riprogettazione dell'app** | Eseguire la riprogettazione delle app durante la migrazione in Azure. | In pianificazione | Pianificata
**Scenario 5: Ricompilazione dell'app** |Ricompilare le app durante la migrazione in Azure. | In pianificazione | Pianificata




