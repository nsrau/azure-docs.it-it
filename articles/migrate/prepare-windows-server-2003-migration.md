---
title: Preparare i server Windows Server 2003 per la migrazione con Azure Migrate
description: Informazioni su come preparare i server Windows Server 2003 per la migrazione con Azure Migrate.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 5e33742d59972d491c1efb8d0f1453c1226d4625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86103943"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Preparare i computer Windows Server 2003 per la migrazione

Questo articolo descrive come preparare i computer che eseguono Windows Server 2003 per la migrazione ad Azure. 

- È possibile usare la migrazione senza agenti per eseguire la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) e [VM VMware](tutorial-migrate-vmware.md) in Azure.
- Per connettersi alle macchine virtuali di Azure dopo la migrazione, è necessario che Hyper-V Integration Services sia installato nella macchina virtuale di Azure. Per i computer Windows Server 2003 non è installato per impostazione predefinita.
- Non è disponibile alcun collegamento di download diretto per l'installazione di Hyper-V Integration Services, quindi è necessario eseguire le operazioni seguenti:
    - Per le macchine virtuali Hyper-V in cui non è installato, estrarre i file di installazione per Integration Services in un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V e quindi copiare il programma di installazione nel computer Windows Server 2003. I file di installazione non sono disponibili nei computer che eseguono Windows Server 2016.
    - Per le macchine virtuali VMware, si crea un'attività di avvio che installa Integration Services quando la macchina virtuale di Azure viene avviata dopo la migrazione.


## <a name="install-on-hyper-v-vms"></a>Installare nelle macchine virtuali Hyper-V

Prima della migrazione, controllare se Hyper-V Integration Services è installato, quindi installare se necessario.

1. Seguire [queste istruzioni](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) per verificare se è installato.
2. Se non è installato, accedere a un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V.
3. Passare al file di installazione in **C:\Windows\System32\vmguest.ISO**e montare il file.
2. Copiare la cartella di installazione nel computer Windows Server 2003 e installare Integration Services.
4. Dopo l'installazione, è possibile lasciare le impostazioni predefinite in Integration Services. 

## <a name="install-on-vmware-vms"></a>Installare nelle macchine virtuali VMware

1. Accedere a un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V.
2. Passare al file di installazione in **C:\Windows\System32\vmguest.ISO**e montare il file.
3. Copiare la cartella di installazione nella macchina virtuale VMware.
4. Dalla riga di comando nella macchina virtuale eseguire ```gpedit.msc``` .
5. Aprire **Configurazione computer**  >  script**Impostazioni Windows**  >  **(avvio/arresto)**.
6. In **avvio**  >  **Aggiungi**  >  **nome script**digitare l'indirizzo del setup.exe.
7. Dopo la migrazione ad Azure, lo script viene eseguito la prima volta che la macchina virtuale di Azure viene avviata.
8. Riavviare manualmente la macchina virtuale di Azure. È disponibile un popup nella diagnostica di avvio per indicare che è necessario un riavvio.
9. Dopo l'esecuzione dello script e l'installazione di Hyper-V Integration Services nella macchina virtuale di Azure, è possibile rimuovere lo script dall'avvio.
10. Dopo l'installazione, è possibile lasciare le impostazioni predefinite in Integration Services. 

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i requisiti di migrazione per le macchine virtuali [VMware](migrate-support-matrix-vmware-migration.md) e [Hyper-V](migrate-support-matrix-hyper-v-migration.md) .
- Eseguire la migrazione di macchine virtuali [VMware](server-migrate-overview.md) e [Hyper-V](tutorial-migrate-hyper-v.md) .
