---
title: Architettura del dispositivo di replica Azure Migrate
description: Viene fornita una panoramica dell'appliance di replica Azure Migrate per la migrazione basata su agenti.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186547"
---
# <a name="replication-appliance"></a>Appliance di replica

Questo articolo descrive l'appliance di replica usato da Azure Migrate: server Assessment quando si esegue la migrazione di VM VMware, computer fisici e macchine virtuali cloud private/pubbliche ad Azure, usando una migrazione basata su agenti. 

Lo strumento è disponibile nell'hub [Azure migrate](migrate-overview.md) . L'hub fornisce strumenti nativi per la valutazione e la migrazione, nonché strumenti di altri servizi di Azure e di fornitori di software indipendenti (ISV) di terze parti.


## <a name="appliance-overview"></a>Panoramica dell'appliance

Il dispositivo di replica viene distribuito come singolo computer locale, come macchina virtuale VMware o server fisico. Esegue:
- **Appliance di replica**: l'appliance di replica coordina le comunicazioni e gestisce la replica dei dati per le macchine virtuali VMware e i server fisici locali che eseguono la replica in Azure.
- **Server di elaborazione**: il server di elaborazione, installato per impostazione predefinita nell'appliance di replica, ed esegue le operazioni seguenti:
    - **Gateway di replica**: funge da gateway di replica. Riceve i dati di replica dai computer abilitati per la replica. Ottimizza i dati di replica con la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.
    - **Programma**di installazione dell'agente: esegue un'installazione push del servizio Mobility. Questo servizio deve essere installato e in esecuzione in ogni computer locale che si vuole replicare per la migrazione.

## <a name="appliance-deployment"></a>Distribuzione dell'appliance

**Distribuisci come** | **Usata per** | **Dettagli**
--- | --- |  ---
Macchina virtuale VMware | Usato in genere quando si esegue la migrazione di macchine virtuali VMware con lo strumento di migrazione Azure Migrate con migrazione basata su agenti. | Scaricare il modello OVA dall'hub Azure Migrate e importare in server vCenter per creare la macchina virtuale dell'appliance.
Un computer fisico | Usato quando si esegue la migrazione di server fisici locali se non si ha un'infrastruttura VMware o se non si riesce a creare una VM VMware usando un modello OVA. | Scaricare un programma di installazione software dall'hub Azure Migrate ed eseguirlo per configurare il computer dell'appliance.

## <a name="appliance-deployment-requirements"></a>Requisiti di distribuzione dell'appliance

[Esaminare](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) i requisiti di distribuzione.



## <a name="appliance-license"></a>Licenza Appliance
L'Appliance viene fornita con una licenza di valutazione di Windows Server 2016, valida per 180 giorni. Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della VM del dispositivo.

## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale. Vengono registrate le modifiche rilevate per un computer.
4. Le comunicazioni avvengono nel modo seguente:
    - Le macchine virtuali comunicano con l'appliance di replica sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - L'appliance di replica orchestra la replica con Azure tramite la porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione nell'appliance di replica) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati della replica, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure attraverso la porta 443 in uscita.
5. I dati di replica registrano il primo terreno in un account di archiviazione della cache in Azure. Questi log vengono elaborati e i dati vengono archiviati in un disco gestito di Azure.

![Architettura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Aggiornamenti Appliance

L'Appliance viene aggiornata manualmente dall'hub Azure Migrate. Si consiglia di eseguire sempre la versione più recente.

1. In Azure Migrate server > > Azure Migrate: server Assessment, server di infrastruttura, fare clic su **server di configurazione**.
2. Nei **server di configurazione**viene visualizzato un collegamento nella **versione dell'agente** quando è disponibile una nuova versione dell'appliance di replica. 
3. Scaricare il programma di installazione nel computer dell'appliance di replica e installare l'aggiornamento. Il programma di installazione rileva la versione corrente in esecuzione nell'appliance.
 
## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurare l'appliance per VMware.
[Informazioni su come](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurare l'appliance per Hyper-V.

