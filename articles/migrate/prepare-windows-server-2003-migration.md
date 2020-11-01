---
title: Preparare i server Windows Server 2003 per la migrazione con Azure Migrate
description: Informazioni su come preparare i server Windows Server 2003 per la migrazione con Azure Migrate.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 350eab98a2b40d5ca1382bbfc24245e7cb47b48e
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146842"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Preparare i computer Windows Server 2003 per la migrazione

Questo articolo descrive come preparare i computer che eseguono Windows Server 2003 per la migrazione ad Azure. 


> [!NOTE]
> Il [supporto esteso di Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) è terminato il 14 luglio 2015.  Il team di supporto di Azure continua a contribuire alla risoluzione dei problemi che riguardano l'esecuzione di Windows Server 2003 in Azure. Tuttavia, questo supporto è limitato a problemi che non richiedono la risoluzione dei problemi a livello di sistema operativo o le patch. La migrazione delle applicazioni nelle istanze di Azure che eseguono una versione più recente di Windows Server è l'approccio consigliato per assicurarsi di sfruttare efficacemente la flessibilità e l'affidabilità del cloud di Azure. Tuttavia, se si sceglie di eseguire la migrazione di Windows Server 2003 ad Azure, è possibile usare lo strumento di migrazione Azure Migrate: server se Windows Server è una macchina virtuale in esecuzione in VMware o Hyper-V.


- È possibile usare la migrazione senza agenti per eseguire la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) e [VM VMware](tutorial-migrate-vmware.md) in Azure.
- Per connettersi alle macchine virtuali di Azure dopo la migrazione, è necessario che Hyper-V Integration Services sia installato nella macchina virtuale di Azure. Per i computer Windows Server 2003 non è installato per impostazione predefinita.
- Non è disponibile alcun collegamento di download diretto per l'installazione di Hyper-V Integration Services, quindi è necessario eseguire le operazioni seguenti:
    - Per le macchine virtuali Hyper-V in cui non è installato, estrarre i file di installazione per Integration Services in un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V e quindi copiare il programma di installazione nel computer Windows Server 2003. I file di installazione non sono disponibili nei computer che eseguono Windows Server 2016.
    - Per le macchine virtuali VMware, si crea un'attività di avvio che installa Integration Services quando la macchina virtuale di Azure viene avviata dopo la migrazione.


## <a name="install-on-hyper-v-vms"></a>Installare nelle macchine virtuali Hyper-V

Prima della migrazione, controllare se Hyper-V Integration Services è installato, quindi installare se necessario.

1. Seguire [queste istruzioni](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) per verificare se è installato.
2. Se non è installato, accedere a un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V.
3. Passare al file di installazione in **C:\Windows\System32\vmguest.ISO** e montare il file.
2. Copiare la cartella di installazione nel computer Windows Server 2003 e installare Integration Services.
4. Dopo l'installazione, è possibile lasciare le impostazioni predefinite in Integration Services. 

## <a name="install-on-vmware-vms"></a>Installare nelle macchine virtuali VMware

1. Accedere a un computer che esegue Windows Server 2012 R2/Windows Server 2012 con il ruolo Hyper-V.
2. Passare al file di installazione in **C:\Windows\System32\vmguest.ISO** e montare il file.
3. Copiare la cartella di installazione nella macchina virtuale VMware.
4. Dalla riga di comando nella macchina virtuale eseguire ```gpedit.msc``` .
5. Aprire **Configurazione computer**  >  script **Impostazioni Windows**  >  **(avvio/arresto)** .
6. In **avvio**  >  **Aggiungi**  >  **nome script** digitare l'indirizzo del setup.exe.
7. Dopo la migrazione ad Azure, lo script viene eseguito la prima volta che la macchina virtuale di Azure viene avviata.
8. Riavviare manualmente la macchina virtuale di Azure. È disponibile un popup nella diagnostica di avvio per indicare che è necessario un riavvio.
9. Dopo l'esecuzione dello script e l'installazione di Hyper-V Integration Services nella macchina virtuale di Azure, è possibile rimuovere lo script dall'avvio.
10. Dopo l'installazione, è possibile lasciare le impostazioni predefinite in Integration Services. 

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i requisiti di migrazione per le macchine virtuali [VMware](migrate-support-matrix-vmware-migration.md) e [Hyper-V](migrate-support-matrix-hyper-v-migration.md) .
- Eseguire la migrazione di macchine virtuali [VMware](server-migrate-overview.md) e [Hyper-V](tutorial-migrate-hyper-v.md) .
