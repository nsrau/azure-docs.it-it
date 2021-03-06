---
title: Concetti-monitoraggio e ripristino di cloud privati della soluzione VMware di Azure
description: Scopri in che modo la soluzione VMware di Azure monitora e ripristina VMware ESXi Server in un cloud privato della soluzione VMware di Azure.
ms.topic: conceptual
ms.custom: contperfq2
ms.date: 11/20/2020
ms.openlocfilehash: 6c37ff6cc0715182453669f67306094af2f00cdf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024348"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorare e ripristinare i cloud privati della soluzione VMware di Azure

La soluzione VMware di Azure monitora costantemente i server VMware ESXi in un cloud privato della soluzione VMware di Azure. 

## <a name="what-azure-vmware-solution-monitors"></a>Quali monitoraggi della soluzione VMware di Azure

La soluzione VMware di Azure monitora le condizioni di errore seguenti per l'host:  

- Stato processore 
- Stato memoria 
- Stato di connessione e di alimentazione 
- Stato della ventola hardware 
- Perdita di connettività di rete 
- Stato della lavagna del sistema hardware 
- Si sono verificati errori sui dischi di un host rete VSAN 
- Tensione hardware 
- Stato temperatura hardware 
- Stato di alimentazione hardware 
- Stato di archiviazione 
- Errore di connessione 

> [!NOTE]
> Gli amministratori tenant della soluzione VMware di Azure non devono modificare o eliminare gli avvisi di VMware vCenter definiti sopra, perché sono gestiti dal piano di controllo della soluzione VMware di Azure in vCenter. Questi avvisi vengono usati dal monitoraggio della soluzione VMware di Azure per attivare il processo di correzione dell'host della soluzione VMware di Azure.

## <a name="azure-vmware-solution-host-remediation"></a>Monitoraggio e aggiornamento dell'host della soluzione VMware di Azure  

Quando la soluzione VMware di Azure rileva un calo o un errore in un nodo della soluzione VMware di Azure nel cloud privato di un tenant, attiva il processo di monitoraggio e aggiornamento dell'host. La correzione host comporta la sostituzione del nodo difettoso con un nuovo nodo integro.  

Il processo di monitoraggio e aggiornamento dell'host inizia con l'aggiunta di un nuovo nodo integro nel cluster. Quindi, quando possibile, l'host difettoso viene inserito in modalità di manutenzione VMware vSphere. VMware vMotion viene usato per spostare le macchine virtuali dall'host difettoso ad altri server disponibili nel cluster, in modo da consentire la migrazione in tempo reale senza tempi di inattività dei carichi di lavoro. Negli scenari in cui l'host difettoso non può essere inserito in modalità di manutenzione, l'host viene rimosso dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati alcuni argomenti per cui è possibile ottenere ulteriori informazioni su:

- [Aggiornamenti del cloud privato della soluzione VMware di Azure](concepts-upgrades.md)
- [Gestione del ciclo di vita delle VM della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Proteggi le VM della soluzione VMware di Azure con l'integrazione del Centro sicurezza di Azure](azure-security-integration.md)
- [Eseguire il backup di macchine virtuali della soluzione VMware di Azure con server di Backup di Azure](backup-azure-vmware-solution-virtual-machines.md)
- [Completare il ripristino di emergenza di macchine virtuali con la soluzione VMware di Azure](disaster-recovery-for-virtual-machines.md)
