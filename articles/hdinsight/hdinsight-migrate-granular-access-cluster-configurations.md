---
title: Modifiche al cluster l'accesso configuration - Azure HDInsight
description: Informazioni sulle modifiche per l'accesso ai campi di configurazione sensibili del cluster.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610215"
---
# <a name="changes-to-cluster-configuration-access"></a>Modifiche alla configurazione del cluster

La versione più recente di Azure HDInsight SDK introduce alcune importanti modifiche per supportare ulteriori accessi con granularità fine in base al ruolo per ottenere informazioni riservate. Come parte di queste modifiche, alcune **azione potrebbe essere necessaria** se si usa uno dei metodi interessati.

## <a name="sdk-for-net-500"></a>SDK per .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) verrà **non venga più restituito parametri sensibili** come chiavi di archiviazione (core-site) o credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, utilizzare `ConfigurationOperationsExtensions.List` in futuro.  Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di utilizzare questo metodo. Ciò consente un controllo granulare su cui gli utenti possono accedere ai dati sensibili per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) è ora deprecata ed è stata sostituita da `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) ora sono deprecate. HTTP ora è sempre abilitata, in modo che questi metodi non sono più necessari.