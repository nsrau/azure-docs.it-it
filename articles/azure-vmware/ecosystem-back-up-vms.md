---
title: Soluzioni di backup per le macchine virtuali della soluzione VMware di Azure
description: Informazioni sulle soluzioni di backup e ripristino iniziali per le macchine virtuali della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d72e7d2491441c05bf59a32dfb19b0af0cf94c95
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343759"
---
# <a name="backup-solutions-for-azure-vmware-solution-virtual-machines-vms"></a>Soluzioni di backup per macchine virtuali della soluzione VMware di Azure (VM)

Un principio chiave della soluzione VMware di Azure è quello di consentire di continuare a usare gli investimenti e le soluzioni VMware preferite in esecuzione in Azure. Il supporto della tecnologia ISV (Independent Software Vendor), convalidato con la soluzione VMware di Azure, è una parte importante di questa strategia. 

CommVault, Veritas e Veeam offrono soluzioni di backup e ripristino leader negli ambienti basati su VMware. I clienti hanno ampiamente adottato queste soluzioni per le distribuzioni locali. Ora questi partner hanno esteso le proprie soluzioni alla soluzione VMware di Azure, usando Azure per fornire un repository di backup e una destinazione di archiviazione per la conservazione e l'archiviazione a lungo termine.

Il traffico di rete di backup tra le macchine virtuali della soluzione VMware di Azure e il repository di backup in Azure si sposta su un collegamento a bassa latenza e larghezza di banda elevata. Il traffico di replica tra le aree passa attraverso la rete interna del backplane di Azure, che riduce i costi di larghezza di banda per gli utenti.

Altre informazioni su queste soluzioni di backup sono disponibili qui:
- [CommVault](https://documentation.commvault.com/11.21/essential/128997_support_for_azure_vmware_solution.html)
- [Veritas](https://vrt.as/nb4avs)
- [Veeam](https://www.veeam.com/kb4012)
