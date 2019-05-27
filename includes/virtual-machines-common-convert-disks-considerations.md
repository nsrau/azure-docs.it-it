---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bf26af7fa4b1b31514fb82c5e28a85154b2e274a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158474"
---
* La conversione richiede un riavvio della macchina virtuale, quindi pianificare la migrazione delle macchine virtuali in una finestra di manutenzione preesistente. 

* La conversione non è reversibile. 

* Tenere presente che tutti gli utenti con il ruolo [Collaboratore macchine virtuali](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) non potranno modificare le dimensioni della macchina virtuale, come invece potevano in fase di pre-conversione. Il motivo è che le macchine virtuali con dischi gestiti richiedono che gli utenti abbiano l'autorizzazione Microsoft.Compute/disks/write per i dischi del sistema operativo.

* Assicurarsi di testare la conversione. Eseguire la migrazione di una macchina virtuale di test prima di eseguire la migrazione nell'ambiente di produzione.

* Durante la conversione la macchina virtuale verrà deallocata. La macchina virtuale riceve un nuovo indirizzo IP quando viene avviata dopo la conversione. Se necessario, è possibile [assegnare un indirizzo IP statico](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) alla VM.

* I dischi rigidi virtuali originali e l'account di archiviazione usato dalla macchina virtuale prima della conversione non verranno eliminati e i costi correlati continueranno a essere addebitati. Per evitare addebiti per questi elementi, eliminare i BLOB VHD originali dopo aver verificato che la conversione sia stata completata.

* Verificare la versione minima dell'agente di macchine virtuali di Azure necessario per supportare il processo di conversione. Per informazioni su come verificare e aggiornare la versione dell'agente, vedere [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Versione minima supportata per gli agenti di macchine virtuali in Azure)