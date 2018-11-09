---
title: Gestire l'accesso ad Azure Site Recovery con il controllo degli accessi in base al ruolo | Microsoft Docs
description: Questo articolo descrive come applicare il controllo degli accessi in base al ruolo per gestire l'accesso ad Azure Site Recovery.
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 19d41d8c9b1a54cefc09190531b064d640048385
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212047"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Gestire l'accesso a Site Recovery con il controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il controllo degli accessi in base al ruolo, è possibile separare le responsabilità all'interno del team e concedere agli utenti solo le autorizzazioni di accesso specifiche necessarie per svolgere il lavoro.

Azure Site Recovery offre 3 ruoli predefiniti per controllare le operazioni di gestione di Site Recovery. Maggiori informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md)

* [Collaboratore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - Questo ruolo ha tutte le autorizzazioni necessarie per gestire le operazioni di Azure Site Recovery in un insieme di credenziali di Servizi di ripristino. Un utente con questo ruolo, tuttavia, non può creare o eliminare un insieme di credenziali di Servizi di ripristino oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per gli amministratori del ripristino di emergenza che possono abilitare e gestire il ripristino di emergenza per le applicazioni o per intere organizzazioni.
* [Operatore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - Questo ruolo ha le autorizzazioni per eseguire e gestire le operazioni di failover e failback. Un utente con questo ruolo non può abilitare o disabilitare la replica, creare o eliminare insiemi di credenziali, registrare una nuova infrastruttura oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per un operatore di ripristino di emergenza che può eseguire il failover di macchine virtuali o applicazioni quando richiesto dai proprietari delle applicazioni e dagli amministratori IT in una situazione di emergenza effettiva o simulata, ad esempio per un'esercitazione sul ripristino di emergenza. In seguito alla risoluzione della situazione di emergenza, l'operatore di ripristino di emergenza può proteggere nuovamente le macchine virtuali ed eseguirne il failback.
* [Lettore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - Questo ruolo ha le autorizzazioni per visualizzare tutte le operazioni di gestione di Site Recovery. Questo ruolo è ideale per un dirigente del monitoraggio IT che può controllare lo stato corrente di protezione e generare i ticket di supporto all'occorrenza.

Per definire ruoli personalizzati per un controllo ancora maggiore, vedere come [creare ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Autorizzazioni necessarie per consentire la replica per le nuove macchine virtuali
Quando viene eseguita la replica di una nuova macchina virtuale in Azure con Azure Site Recovery, i livelli di accesso dell'utente associati vengono convalidati per assicurarsi che l'utente abbia le autorizzazioni necessarie per usare le risorse di Azure fornite per Site Recovery.

Per la replica per una nuova macchina virtuale, un utente deve avere:
* Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
* Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
* Autorizzazione per la scrittura nell'account di archiviazione selezionato

Per completare la replica di una nuova macchina virtuale, un utente deve avere le autorizzazioni seguenti.

> [!IMPORTANT]
>Assicurarsi che vengano aggiunte le autorizzazioni appropriate per il modello di distribuzione (Resource Manager/classica) usato per la distribuzione delle risorse.

| **Tipo di risorsa** | **Modello di distribuzione** | **Autorizzazione** |
| --- | --- | --- |
| Calcolo | Gestione risorse | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Classico | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rete | Gestione risorse | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Classico | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Archiviazione | Gestione risorse | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Classico | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Gruppo di risorse | Gestione risorse | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

È consigliabile usare i [ruoli predefiniti](../role-based-access-control/built-in-roles.md) "Collaboratore Macchina virtuale" e "Collaboratore Macchina virtuale classica", rispettivamente per il modello di distribuzione Resource Manager e il modello di distribuzione classica.

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](../role-based-access-control/troubleshooting.md): suggerimenti per la risoluzione di problemi comuni.
