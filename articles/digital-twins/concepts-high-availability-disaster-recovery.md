---
title: Disponibilità elevata e ripristino di emergenza
titleSuffix: Azure Digital Twins
description: Descrive le funzionalità di Azure e dei dispositivi gemelli digitali di Azure che consentono di creare soluzioni di Azure per la disponibilità elevata con funzionalità di ripristino di emergenza.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094439"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza di Azure Digital Twins

Un'area di considerazione chiave per le soluzioni resilienti delle cose è la continuità aziendale e il ripristino di emergenza. La progettazione per la **disponibilità elevata** e **il ripristino di emergenza** consentono di definire e raggiungere gli obiettivi di tempo di esecuzione appropriati per la soluzione.

Questo articolo illustra le funzionalità di disponibilità elevata e ripristino di emergenza offerte specificamente dal servizio Azure Digital Twins.

I dispositivi gemelli digitali di Azure supportano queste opzioni di funzionalità:
* *Disponibilità elevata intra-Region* : ridondanza incorporata per garantire il tempo di esecuzione del servizio
* *Ripristino di emergenza tra aree* : failover in un'area geografica di Azure abbinata in caso di errore imprevisto Data Center

È anche possibile vedere la sezione [*procedure consigliate*](#best-practices) per indicazioni generali su Azure per la progettazione di disponibilità elevata/ripristino di emergenza.

## <a name="intra-region-ha"></a>Disponibilità elevata intra-area
 
I dispositivi gemelli digitali di Azure forniscono una disponibilità elevata all'interno dell'area implementando le ridondanze nel servizio. **Non sono necessarie altre operazioni da parte degli sviluppatori di una soluzione di i dispositivi gemelli digitali di Azure per sfruttare i vantaggi di queste funzionalità di disponibilità elevata.** Anche se i dispositivi gemelli digitali di Azure offrono una garanzia di tempo di esecuzione ragionevolmente elevata, è possibile che si verifichino errori temporanei, come per qualsiasi piattaforma di elaborazione distribuita. I criteri di ripetizione dei tentativi appropriati devono essere incorporati nei componenti che interagiscono con un'applicazione cloud per gestire gli errori temporanei.

## <a name="cross-region-dr"></a>Ripristino di emergenza tra aree

È possibile che si verifichino rari casi in cui un data center presenta interruzioni estese a causa di interruzioni dell'alimentazione o di altri eventi nell'area. Tali eventi sono rari e, durante tali errori, la funzionalità di disponibilità elevata di tipo intra Region descritta sopra potrebbe non essere utile. Azure Digital gemelli risolve questo problema con il failover avviato da Microsoft.

Il **failover avviato da Microsoft** viene eseguito da Microsoft in rari casi per eseguire il failover di tutte le istanze di Azure Digital Twins da un'area interessata all'area geografica corrispondente. Questo processo è un'opzione predefinita (senza alcun modo per consentire agli utenti di rifiutare esplicitamente) e non richiede alcun intervento da parte dell'utente. Microsoft si riserva il diritto di effettuare una determinazione di quando questa opzione viene esercitata. Questo meccanismo non implica il consenso dell'utente prima del failover dell'istanza dell'utente.

>[!NOTE]
> Alcuni servizi di Azure forniscono anche un'opzione aggiuntiva denominata **failover avviato dal cliente**, che consente ai clienti di avviare un failover solo per la propria istanza, ad esempio per eseguire un'esercitazione sul ripristino di emergenza. Questo meccanismo non è attualmente **supportato** dai dispositivi gemelli digitali di Azure. 

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate per la disponibilità elevata/ripristino di emergenza, vedere le seguenti indicazioni su Azure per questo argomento: 
* L'articolo [*informazioni tecniche sulla continuità aziendale di Azure*](/azure/architecture/framework/resiliency/overview) descrive un framework generale che consente di valutare la continuità aziendale e il ripristino di emergenza. 
* Il documento sul [*ripristino di emergenza e la disponibilità elevata per le applicazioni Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornisce indicazioni sull'architettura sulle strategie per le applicazioni Azure per ottenere la disponibilità elevata (ha) e il ripristino di emergenza.

## <a name="next-steps"></a>Passaggi successivi 

Scopri di più su come iniziare a usare le soluzioni di dispositivi gemelli digitali di Azure:
 
* [*Cos'è il servizio Gemelli digitali di Azure?*](overview.md)
* [*Guida introduttiva: esplorare uno scenario di esempio*](quickstart-adt-explorer.md)