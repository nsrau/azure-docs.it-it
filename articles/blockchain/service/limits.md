---
title: Limiti del servizio Blockchain di AzureAzure Blockchain Service limits
description: Panoramica dei limiti di servizio e funzionalità nel servizio Blockchain di AzureOverview of the service and functional limits in Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455649"
---
# <a name="limits-in-azure-blockchain-service"></a>Limiti nel servizio Blockchain di AzureLimits in Azure Blockchain Service

Il servizio Blockchain di Azure include limiti di funzionalità e servizio, ad esempio il numero di nodi che un membro può avere, le restrizioni del consorzio e le quantità di archiviazione.

## <a name="pricing-tier"></a>Piano tariffario

I limiti massimi per le transazioni e i nodi di convalida dipendono dal provisioning del servizio Blockchain di Azure ai piani tariffari Basic o Standard.Maximum limits on transactions and validator nodes depend on whether you provision Azure Blockchain Service at Basic or Standard pricing tiers.

| Piano tariffario | Numero massimo di nodi di transazioneMax transaction nodes | Nodi validatore maxMax validator nodes |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

La modifica del piano tariffario tra Basic e Standard dopo la creazione dei membri non è supportata.

## <a name="storage-capacity"></a>Capacità di archiviazione

La quantità massima di spazio di archiviazione che può essere utilizzata per nodo per i dati e i registri della contabilità generale è 1,8 terabyte.

La riduzione delle dimensioni di archiviazione della contabilità generale e dei registri non è supportata.

## <a name="consortium-limits"></a>Limiti del consorzio

* **I nomi di consorzio e membro devono essere univoci** rispetto agli altri nomi di consorzio e membro nel servizio Blockchain di Azure.Consortium and member names must be unique from other consortium and member names in the Azure Blockchain Service.

* **I nomi dei membri e dei consorzi non possono essere modificati**

* **Tutti i membri di un consorzio devono essere nello stesso piano tariffario**

* **Tutti i membri che partecipano a un consorzio devono risiedere nella stessa regione**

    Il primo membro creato in un consorzio detta la regione. I membri invitati al consorzio devono risiedere nella stessa regione del primo membro. Limitare tutti i membri nella stessa regione contribuisce a garantire che il consenso della rete non sia influenzato negativamente.

* **Un consorzio deve avere almeno un amministratore**

    Se in un consorzio è presente un solo amministratore, non possono rimuovere se stessi dal consorzio o eliminare il membro finché non viene aggiunto o promosso un altro amministratore nel consorzio.

* **I membri rimossi dal consorzio non possono essere aggiunti di nuovo**

    Piuttosto, devono essere nuovamente invitati a unirsi al consorzio e creare un nuovo membro. La risorsa membro esistente non viene eliminata per mantenere le transazioni cronologiche.

* **Tutti i membri di un consorzio devono utilizzare la stessa versione contabile**

    Per altre informazioni sulle versioni di applicazione di patch, aggiornamenti e contabilità generale disponibili nel servizio Blockchain di Azure, vedere [Applicazione di patch, aggiornamenti e versioni.](ledger-versions.md)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui criteri relativi all'applicazione di patch e agli aggiornamenti dei sistemi: [applicazione di patch, aggiornamenti e versioni](ledger-versions.md).
