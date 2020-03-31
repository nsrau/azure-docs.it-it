---
title: Risolvere i dettagli del firewall di archiviazione
description: Un'impostazione del firewall di rete di un account di archiviazione può causare errori durante la creazione di una destinazione di archiviazione BLOB di Azure nella cache HPC di Azure.A storage account network firewall setting can cause failure when creating an Azure Blob storage target in Azure HPC Cache. In questo articolo viene fornita una soluzione alternativa per la limitazione fino a quando non viene presente una correzione software.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174408"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Aggirare le impostazioni del firewall per l'account di archiviazione BLOB

Una particolare impostazione usata nei firewall dell'account di archiviazione può causare l'esito negativo della creazione della destinazione di archiviazione BLOB. Il team della cache HPC di Azure sta lavorando a una correzione software per questo problema, ma è possibile aggirarlo seguendo le istruzioni riportate in questo articolo.

L'impostazione del firewall che consente l'accesso solo da "reti selezionate" può impedire alla cache di creare una destinazione di archiviazione BLOB. Questa configurazione si trova nella pagina delle impostazioni **Firewall e reti virtuali** dell'account di archiviazione.

Il problema è che il servizio cache utilizza una rete virtuale del servizio nascosto separato dagli ambienti dei clienti. Non è possibile autorizzare esplicitamente questa rete per accedere all'account di archiviazione.

Quando si crea una destinazione di archiviazione BLOB, il servizio cache usa questa rete per verificare se il contenitore è vuoto o meno. Se il firewall non consente l'accesso dalla rete nascosta, il controllo ha esito negativo e la creazione della destinazione di archiviazione ha esito negativo.

Per risolvere il problema, modificare temporaneamente le impostazioni del firewall durante la creazione della destinazione di archiviazione:

1. Passare alla pagina **Firewall e reti virtuali** dell'account di archiviazione e modificare l'impostazione "Consenti accesso da" in Tutte le **reti**.
1. Creare la destinazione di archiviazione BLOB nella cache HPC di Azure.Create the Blob storage target in your Azure HPC Cache.
1. Dopo aver creato correttamente la destinazione di archiviazione, modificare l'impostazione del firewall dell'account in **Reti selezionate**.

La cache HPC di Azure non usa la rete virtuale del servizio per accedere alla destinazione di archiviazione completata.

Per assistenza con questa soluzione alternativa, [contattare il servizio Microsoft e supporto tecnico](hpc-cache-support-ticket.md).
