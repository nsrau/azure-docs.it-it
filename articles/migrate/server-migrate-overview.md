---
title: Selezionare un'opzione di migrazione VMware con la migrazione di Server eseguire la migrazione di Azure | Microsoft Docs
description: Fornisce una panoramica delle opzioni per la migrazione di VM VMware in Azure con la migrazione di Server eseguire la migrazione di Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811570"
---
# <a name="select-a-vmware-migration-option"></a>Selezionare un'opzione di migrazione VMware

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento di migrazione del Server eseguire la migrazione di Azure. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione tramite la replica senza agente. Eseguire la migrazione di macchine virtuali senza dover installare nulla su di essi.
- Migrazione con un agente per la replica. Installare un agente nella macchina virtuale per la replica.


Anche se la replica senza agente è più semplice dal punto di vista della distribuzione, ha attualmente alcune limitazioni.

## <a name="agentless-migration-limitations"></a>Limitazioni per la migrazione senza agenti

Come indicato di seguito sono riportate le limitazioni:

- **Replica simultanea**: È possibile replicare simultaneamente un massimo di 50 macchine virtuali da un Server vCenter.<br/> Se si dispone di più di 50 macchine virtuali per la migrazione, è possibile creare più batch di macchine virtuali.<br/> La replica di più in una sola volta influirà sulle prestazioni.
- **Dischi delle macchine Virtuali**: Una macchina virtuale che si desidera eseguire la migrazione deve essere minore o uguale a 60 dischi.
- **Sistemi operativi della macchina virtuale**: In generale, Azure Migrate può eseguire la migrazione di qualsiasi sistema operativo Linux o un Server Windows, ma potrebbe richiedere modifiche nelle macchine virtuali in modo che possano eseguire in Azure. Azure Migrate determina le modifiche automaticamente per questi sistemi operativi:
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Per altri sistemi operativi, è necessario apportare le modifiche manualmente prima della migrazione. Il [eseguire la migrazione di esercitazione](tutorial-migrate-vmware.md) spiega come eseguire questa operazione.
- **Avvio di Linux**: Se /boot si trova in una partizione dedicata, dovrebbero risiedere su disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se /boot fa parte della partizione radice (/), quindi la partizione '/' deve essere il disco del sistema operativo e non su altri dischi.
- **Avvio UEFI**: Le macchine virtuali con UEFI boot non sono supportate per la migrazione.
- **I dischi/volumi (BitLocker, cryptfs) crittografati**: Le macchine virtuali con dischi/volumi crittografati non sono supportate per la migrazione.
- **Dischi RDM/pass-through**: Se le macchine virtuali hanno dischi RDM o pass-through, questi dischi non verranno replicati in Azure
- **NFS**: Non è possibile replicare volumi NFS montati come volumi nelle macchine virtuali.
- **Archiviazione di destinazione**: È possibile migrare solo le macchine virtuali VMware in macchine virtuali di Azure con dischi gestiti (unità disco rigido Standard, Premium SSD).



## <a name="deployment-steps-comparison"></a>Confronto tra i passaggi di distribuzione

Dopo aver esaminato le limitazioni, comprensione dei passaggi coinvolti nella distribuzione di ogni soluzione può aiutarti a decidere quale opzione scegliere.

**Attività** | **Dettagli** |**Senza agenti** | **Basata su agente**
--- | --- | --- | ---
**Preparare i server e VMware le macchine virtuali per la migrazione** | Configurare una serie di impostazioni nel server e VM VMware. | Obbligatoria | Obbligatoria
**Aggiungere lo strumento di migrazione del Server** | Aggiungere lo strumento di migrazione del Server eseguire la migrazione di Azure nel progetto Azure Migrate. | Obbligatoria | Obbligatoria
**Distribuisce il dispositivo di Azure Migrate** | Configurare un'appliance leggera in una VM VMware per l'individuazione delle macchine Virtuali e valutazione. | Obbligatoria | Non obbligatorio.
**Installare il servizio Mobility nelle macchine virtuali** | Installare il servizio Mobility in ogni macchina virtuale da replicare | Facoltativo | Obbligatoria
**Distribuire l'appliance di replica di migrazione del Server eseguire la migrazione di Azure** | Configurare un'appliance in una VM VMware per individuare le macchine virtuali e collegare tramite ponte tra il servizio Mobility in esecuzione in macchine virtuali e la migrazione di Server eseguire la migrazione di Azure | Facoltativo | Obbligatoria
**Replicare le macchine virtuali**. Abilitare la replica della macchina virtuale. | Configurare le impostazioni di replica e selezionare macchine virtuali da replicare | Obbligatoria | Obbligatoria
**Eseguire una migrazione di test** | Eseguire una migrazione di test per assicurarsi che tutto funzioni come previsto. | Obbligatoria | Obbligatoria
**Eseguire una migrazione completa** | Eseguire la migrazione di macchine virtuali. | Obbligatoria | Obbligatoria




## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione di macchine virtuali VMware](tutorial-migrate-vmware.md) utilizzando la migrazione senza agente.



