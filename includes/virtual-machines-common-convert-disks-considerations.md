---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84237687"
---
* La conversione richiede un riavvio della macchina virtuale, quindi pianificare la migrazione delle macchine virtuali in una finestra di manutenzione preesistente. 

* La conversione non è reversibile. 

* Tenere presente che tutti gli utenti con il ruolo [Collaboratore macchine virtuali](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) non potranno modificare le dimensioni della macchina virtuale, come invece potevano in fase di pre-conversione. Il motivo è che le macchine virtuali con dischi gestiti richiedono che gli utenti abbiano l'autorizzazione Microsoft.Compute/disks/write per i dischi del sistema operativo.

* Assicurarsi di testare la conversione. Eseguire la migrazione di una macchina virtuale di test prima di eseguire la migrazione nell'ambiente di produzione.

* Durante la conversione la macchina virtuale verrà deallocata. La macchina virtuale riceve un nuovo indirizzo IP quando viene avviata dopo la conversione. Se necessario, è possibile [assegnare un indirizzo IP statico](../articles/virtual-network/public-ip-addresses.md) alla VM.

* Verificare la versione minima dell'agente di macchine virtuali di Azure necessario per supportare il processo di conversione. Per informazioni su come verificare e aggiornare la versione dell'agente, vedere [Minimum version support for VM agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Versione minima supportata per gli agenti di macchine virtuali in Azure)