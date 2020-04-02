---
title: Limiti del servizio Blockchain di AzureAzure Blockchain Service limits
description: Panoramica dei limiti di servizio e funzionalità nel servizio Blockchain di AzureOverview of the service and functional limits in Azure Blockchain Service
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529566"
---
# <a name="limits-in-azure-blockchain-service"></a>Limiti nel servizio Blockchain di AzureLimits in Azure Blockchain Service

Il servizio Blockchain di Azure include limiti di funzionalità e servizio, ad esempio il numero di nodi che un membro può avere, le restrizioni del consorzio e le quantità di archiviazione.

## <a name="pricing-tier"></a>Piano tariffario

I limiti massimi per le transazioni e i nodi di convalida dipendono dal provisioning del servizio Blockchain di Azure a i piani tariffari di base o standard.

| Piano tariffario | Numero massimo di nodi di transazioneMax transaction nodes | Nodi validatore maxMax validator nodes |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

La rete del consorzio deve avere almeno due nodi di livello standard del servizio Blockchain di Azure.Your consortium network should have a least two Azure Blockchain Service standard tier nodes. I nodi di livello standard includono due nodi validator. Quattro nodi validator sono necessari per soddisfare il consenso di tolleranza di errore [bizantene](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)di Istanbul .

Usare il livello di base per lo sviluppo, il test e la prova dei concetti. Usare il livello Standard per le distribuzioni di livello di produzione. È inoltre necessario utilizzare il livello *Standard* se si utilizza Blockchain Data Manager o si invia un elevato volume di transazioni private.

La modifica del piano tariffario tra base e standard dopo la creazione dei membri non è supportata.

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

    Piuttosto, devono essere nuovamente invitati a unirsi al consorzio e creare un nuovo membro. Le risorse membro esistenti non vengono eliminate per mantenere le transazioni cronologiche.

* **Tutti i membri di un consorzio devono utilizzare la stessa versione contabile**

    Per altre informazioni sulle versioni di applicazione di patch, aggiornamenti e contabilità generale disponibili nel servizio Blockchain di Azure, vedere [Applicazione di patch, aggiornamenti e versioni.](ledger-versions.md)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui criteri relativi all'applicazione di patch e agli aggiornamenti dei sistemi: [applicazione di patch, aggiornamenti e versioni](ledger-versions.md).
