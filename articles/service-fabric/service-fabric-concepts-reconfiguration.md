---
title: Riconfigurazione con Azure Service Fabric | Microsoft Docs
description: Comprendere la riconfigurazione delle partizioni con Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 39f9bec12d6fde1576f283ac80f72e62581efc9c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="reconfiguration-in-azure-service-fabric"></a>Riconfigurazione con Azure Service Fabric
Una *configurazione* è definita come le repliche e i relativi ruoli per una partizione di un servizio con stato.

Una *riconfigurazione* è il processo di spostamento di una *configurazione* a un'altra *configurazione*. Apporta una modifica al set di repliche per una partizione di un servizio con stato. La configurazione precedente viene chiamata *configurazione precedente (CP)* e la nuova configurazione viene chiamata *configurazione corrente (CC)*. Il protocollo di riconfigurazione in Service Fabric consente di mantenere la coerenza e la disponibilità durante le modifiche al set di repliche.

La Gestione failover avvia le riconfigurazioni in risposta a eventi diversi nel sistema. Ad esempio, se il primario ha esito negativo, viene avviata una riconfigurazione per promuovere un secondario attivo a un primario. Un altro esempio è in risposta agli aggiornamenti dell'applicazione quando potrebbe essere necessario spostare il primario in un altro nodo per aggiornare il nodo.

## <a name="reconfiguration-types"></a>Tipi di riconfigurazione
Le riconfigurazioni possono essere classificate in due tipi:

- Riconfigurazioni in cui il primario è in fase di modifica
    - **Failover**: i failover sono riconfigurazioni in risposta all'errore di un primario in esecuzione
    - **SwapPrimary**: gli swap sono riconfigurazioni in cui Service Fabric deve spostare un primario in esecuzione da un nodo a un altro, in genere in risposta al bilanciamento del carico o a un aggiornamento e così via.

- Riconfigurazioni in cui il primario non è in fase di modifica

## <a name="reconfiguration-phases"></a>Fasi di riconfigurazione
Una riconfigurazione passa per diverse fasi:

- **Phase0**: questa fase viene eseguita in riconfigurazioni del primario swap in cui il primario corrente trasferisce il suo stato al nuovo primario e passa al secondario attivo.

- **Phase1**: questa fase viene eseguita durante le riconfigurazioni in cui il primario è in fase di modifica. Durante questa fase, Service Fabric identifica tra le repliche correnti qual è il primario corretto. Questa fase non è necessaria durante le riconfigurazioni del primario swap perché il nuovo primario è già stato selezionato. 

- **Phase2**: durante questa fase, Service Fabric assicura che tutti i dati siano disponibili nella maggior parte delle repliche della configurazione corrente.

Esistono altre fasi che sono solo per uso interno.

## <a name="stuck-reconfigurations"></a>Riconfigurazioni bloccate
Le riconfigurazioni possono *bloccarsi* per svariati motivi. Alcuni dei motivi più comuni includono:

- **Repliche non attive**: alcune fasi di riconfigurazione richiedono che la maggior parte delle repliche nella configurazione siano attive
- **Problemi di rete o di comunicazione**: le riconfigurazioni richiedono la connettività di rete tra nodi diversi
- **Errori API**: il protocollo di riconfigurazione richiede che le implementazioni del servizio completino determinate API. Ad esempio: se non viene rispettato il token di annullamento in un servizio affidabile, le riconfigurazioni SwapPrimary si bloccano

I report sull'integrità dei componenti di sistema, ad esempio System.FM, System.RA, System.RAP, possono essere usati per diagnosticare dove si è bloccata una riconfigurazione. La [pagina Report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descrive questi report sull'integrità.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Ciclo di vita di Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
- [Report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Istanze e repliche](service-fabric-concepts-replica-lifecycle.md)
