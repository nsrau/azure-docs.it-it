---
title: Riconfigurazione con Azure Service Fabric | Microsoft Docs
description: Informazioni sulla riconfigurazione delle partizioni in Service Fabric
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
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 8371c4b268e1181e61542261ad7fc5fd04f6e59c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Riconfigurazione con Azure Service Fabric
Una *configurazione* è definita come le repliche e i relativi ruoli per una partizione di un servizio con stato.

Una *riconfigurazione* è il processo di spostamento di una configurazione in un'altra configurazione. Apporta una modifica al set di repliche per una partizione di un servizio con stato. La configurazione precedente viene chiamata *configurazione precedente* e la nuova configurazione viene chiamata *configurazione corrente*. Il protocollo di riconfigurazione in Azure Service Fabric consente di mantenere la coerenza e la disponibilità durante le modifiche al set di repliche.

Failover Manager avvia le riconfigurazioni in risposta a eventi diversi nel sistema. Ad esempio, se il primario ha esito negativo, viene avviata una riconfigurazione per promuovere un secondario attivo a un primario. Un altro esempio è la risposta agli aggiornamenti dell'applicazione quando potrebbe essere necessario spostare il primario in un altro nodo per aggiornare il nodo.

## <a name="reconfiguration-types"></a>Tipi di riconfigurazione
Le riconfigurazioni possono essere classificate in due tipi:

- Riconfigurazioni in cui il primario è in fase di modifica:
    - **Failover**: i failover sono riconfigurazioni in risposta all'errore di un primario in esecuzione.
    - **SwapPrimary**: gli swap sono riconfigurazioni in cui Service Fabric deve spostare un primario in esecuzione da un nodo a un altro, in genere in risposta al bilanciamento del carico o a un aggiornamento.

- Riconfigurazioni in cui il primario non è in fase di modifica.

## <a name="reconfiguration-phases"></a>Fasi di riconfigurazione
Una riconfigurazione passa per diverse fasi:

- **Fase 0**: questa fase viene eseguita in riconfigurazioni di scambio del primario in cui il primario corrente trasferisce lo stato al nuovo primario e passa al secondario attivo.

- **Fase 1**: questa fase viene eseguita durante le riconfigurazioni in cui il primario è in fase di modifica. Durante questa fase, Service Fabric identifica il primario corretto tra le repliche correnti. Questa fase non è necessaria durante le riconfigurazioni di scambio del primario perché il nuovo primario è già stato selezionato. 

- **Fase 2**: durante questa fase, Service Fabric verifica che tutti i dati siano disponibili nella maggior parte delle repliche della configurazione corrente.

Esistono altre fasi che sono solo per uso interno.

## <a name="stuck-reconfigurations"></a>Riconfigurazioni bloccate
Le riconfigurazioni possono *bloccarsi* per svariati motivi. Alcuni dei motivi più comuni includono:

- **Repliche non attive**: alcune fasi di riconfigurazione richiedono che la maggior parte delle repliche nella configurazione siano attive.
- **Problemi di rete o di comunicazione**: le riconfigurazioni richiedono la connettività di rete tra nodi diversi.
- **Errori API**: il protocollo di riconfigurazione richiede che le implementazioni del servizio terminino determinate API. Ad esempio, se non viene rispettato il token di annullamento in un servizio Reliable Services, le riconfigurazioni SwapPrimary si bloccano.

Usare i report sull'integrità dei componenti di sistema, ad esempio System.FM, System.RA e System.RAP, per diagnosticare dove si è bloccata una riconfigurazione. La [pagina Report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descrive questi report sull'integrità.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui concetti relativi a Service Fabric, vedere gli articoli seguenti:

- [Ciclo di vita di Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
- [Report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Istanze e repliche](service-fabric-concepts-replica-lifecycle.md)
