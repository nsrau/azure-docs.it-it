---
title: Aggirare le impostazioni del firewall di archiviazione
description: Un'impostazione del firewall di rete dell'account di archiviazione può causare un errore durante la creazione di una destinazione di archiviazione BLOB di Azure nella cache HPC di Azure. Questo articolo fornisce una soluzione alternativa per la limitazione fino a quando non si verifica una correzione software.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174408"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Aggirare le impostazioni del firewall dell'account di archiviazione BLOB

Una particolare impostazione usata nei firewall dell'account di archiviazione può causare un errore di creazione della destinazione dell'archiviazione BLOB. Il team di cache HPC di Azure sta lavorando a una correzione software per questo problema, ma è possibile aggirarlo seguendo le istruzioni riportate in questo articolo.

L'impostazione del firewall che consente l'accesso solo da "reti selezionate" può impedire la creazione di una destinazione di archiviazione BLOB da parte della cache. Questa configurazione si trova nella pagina dei firewall e delle impostazioni delle **reti virtuali** dell'account di archiviazione.

Il problema è che il servizio cache usa una rete virtuale del servizio nascosta che è separata dagli ambienti del cliente. Non è possibile autorizzare in modo esplicito questa rete ad accedere all'account di archiviazione.

Quando si crea una destinazione di archiviazione BLOB, il servizio cache usa questa rete per verificare se il contenitore è vuoto o meno. Se il firewall non consente l'accesso dalla rete nascosta, il controllo ha esito negativo e la creazione della destinazione di archiviazione ha esito negativo.

Per risolvere il problema, modificare temporaneamente le impostazioni del firewall durante la creazione della destinazione di archiviazione:

1. Passare alla pagina **firewall e reti virtuali** dell'account di archiviazione e modificare l'impostazione "Consenti l'accesso da" a **tutte le reti**.
1. Creare la destinazione di archiviazione BLOB nella cache HPC di Azure.
1. Dopo che la destinazione di archiviazione è stata creata correttamente, modificare di nuovo l'impostazione del firewall dell'account nelle **reti selezionate**.

La cache HPC di Azure non usa la rete virtuale del servizio per accedere alla destinazione di archiviazione completata.

Per informazioni su questa soluzione, [contattare il supporto tecnico Microsoft](hpc-cache-support-ticket.md).
