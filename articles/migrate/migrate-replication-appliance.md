---
title: Architettura dello strumento di Azure Migrate replica | Microsoft Docs
description: Viene fornita una panoramica dell'appliance replica Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811440"
---
# <a name="replication-appliance"></a>Appliance di replica

Questo articolo descrive l'appliance di replica usato da Azure Migrate: Valutazione di server quando si pubblica/privata migrazione di VM VMware e computer fisici cloud macchine virtuali in Azure, usando una migrazione basata su agente. 

Lo strumento è disponibile nel [Azure Migrate](migrate-overview.md) hub. L'hub fornisce gli strumenti nativi per la valutazione e migrazione, nonché strumenti da altri servizi di Azure e da fornitori di terze parti di software indipendenti (ISV).


## <a name="appliance-overview"></a>Panoramica delle Appliance

L'appliance di replica viene distribuita come un singolo computer locale, ad esempio una VM VMware o server fisico. Viene eseguito:
- **Appliance replica**: L'appliance replica coordina le comunicazioni e gestisce la replica dei dati, per le macchine virtuali VMware locali e server fisici in Azure.
- **Server di elaborazione** Il server di elaborazione, che viene installato per impostazione predefinita nell'appliance di replica ed esegue le operazioni seguenti:
    - **Gateway di replica**: Funge da gateway di replica. Riceve i dati di replica dalle macchine abilitate per la replica. Ottimizza i dati di replica attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.
    - **Programma di installazione dell'agente**: Esegue un'installazione push del servizio Mobility. Questo servizio deve essere installato e in esecuzione in ogni computer locale che si vuole replicare per la migrazione.

## <a name="appliance-deployment"></a>Distribuzione di Appliance

**Distribuire come** | **Usata per** | **Dettagli**
--- | --- |  ---
Macchina virtuale VMware | In genere usato durante la migrazione di VM VMware usando lo strumento di migrazione eseguire la migrazione di Azure con la migrazione basata su agente. | Scaricare il modello con estensione OVA dall'hub di Azure Migrate e importare in vCenter Server per la creazione della macchina virtuale dell'appliance.
Un computer fisico | Usato durante la migrazione di server fisici in locale se non si dispone di un'infrastruttura VMware o se non si riesce a creare una VM VMware usando un modello con estensione OVA. | Scaricare un programma di installazione software dall'hub di Azure Migrate ed eseguire il codice per configurare il computer di appliance.

## <a name="appliance-deployment-requirements"></a>Requisiti di distribuzione del dispositivo

[Revisione](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) i requisiti di distribuzione.



## <a name="appliance-license"></a>Licenza dispositivo
L'appliance viene fornito con una licenza di valutazione di Windows Server 2016, questa operazione è valida per 180 giorni. Se il periodo di valutazione sta scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure di attivare la licenza del sistema operativo della macchina virtuale dell'appliance.

## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale. Vengono registrate le modifiche rilevate per un computer.
4. Le comunicazioni avvengono nel modo seguente:
    - Le macchine virtuali comunicano con l'appliance di replica sulla porta HTTPS 443 in ingresso, per la gestione delle repliche.
    - L'appliance replica Orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione nell'appliance di replica) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.
5. I dati di replica registra ' s land prima in un account di archiviazione della cache di Azure. Questi log vengono elaborati e i dati vengono archiviati in Azure il disco gestito.

![Architettura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Aggiornamenti delle Appliance

L'appliance viene aggiornata manualmente dall'hub di Azure Migrate. È consigliabile eseguire sempre la versione più recente.

1. In Azure esegue la migrazione > server > Azure Migrate: Valutazione dei server, server di infrastruttura, fare clic su **server di configurazione**.
2. Nelle **server di configurazione**, verrà visualizzato un collegamento **versione dell'agente** quando è disponibile una nuova versione dell'appliance replica. 
3. Scaricare il programma di installazione nel computer di appliance di replica e installare l'aggiornamento. Il programma di installazione rileva la versione corrente è in esecuzione nell'appliance.
 
## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](tutorial-assess-vmware.md#set-up-the-appliance-vm) per configurare l'appliance per VMware.
[Informazioni su come](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) per configurare l'appliance per Hyper-V.

