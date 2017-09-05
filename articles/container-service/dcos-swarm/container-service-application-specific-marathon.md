---
title: Servizio Marathon specifico per un'applicazione o un'utente | Documentazione Microsoft
description: Creare un servizio Marathon specifico per un'applicazione o un'utente
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contenitori, Marathon, Micro-Service, controller di dominio/sistema operativo, Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 3134872eb59f2f6219499f3d5a92673f680af04d
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="create-an-application-or-user-specific-marathon-service"></a>Creare un servizio Marathon specifico per un'applicazione o un'utente
Il servizio contenitore di Azure fornisce un set di server master in cui vengono preconfigurati Apache Mesos e Marathon. È possibile usarli per orchestrare le applicazioni nel cluster, ma è consigliabile non usare i server master a questo scopo. La modifica della configurazione di Marathon richiede ad esempio l'accesso ai server master stessi per apportare modifiche. Per questa operazione sono consigliabili server master univoci, che risultano leggermente diversi dai server standard e devono essere gestiti in modo specifico e indipendente. La configurazione necessaria per un team potrebbe non essere ottimale per un altro team.

Questo articolo illustra come aggiungere un servizio Marathon specifico per un'applicazione o un'utente.

Dato che il servizio apparterrà a un singolo utente o team, sarà possibile configurarlo in base alle esigenze specifiche dell'utente o del team. Il servizio contenitore di Azure assicurerà la continuazione dell'esecuzione del servizio. In caso di errore, il servizio verrà riavviato dal servizio contenitore di Azure. Nella maggior parte dei casi il tempo di inattività non verrà percepito dall'utente.

## <a name="prerequisites"></a>Prerequisiti
[Distribuire un'istanza del servizio contenitore di Azure](container-service-deployment.md) con un agente di orchestrazione di tipo DC/OS e [assicurarsi che il client possa connettersi al cluster](../container-service-connect.md). Seguire anche questa procedura.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Creare un servizio Marathon specifico per un'applicazione o un'utente
Creare prima di tutto un file di configurazione JSON che definisce il nome del servizio dell'applicazione da creare. In questo caso viene usato `marathon-alice` come nome del framework. Salvare il file con un nome analogo a `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Usare quindi l'interfaccia della riga di comando del controller di dominio/sistema operativo per installare l'istanza di Marathon con le opzioni impostate nel file di configurazione:

```bash
dcos package install --options=marathon-alice.json marathon
```

Dovrebbe essere visualizzato il servizio `marathon-alice` in esecuzione nella scheda dei servizi dell'interfaccia della riga di comando del controller di dominio/sistema operativo. L'interfaccia utente sarà `http://<hostname>/service/marathon-alice/` , se si vuole accedere direttamente.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Impostare l'interfaccia della riga di comando di DC/OS per accedere al servizio
Facoltativamente è possibile configurare l'interfaccia della riga di comando del controller di dominio/sistema operativo per accedere a questo nuovo servizio. A tale scopo, impostare la proprietà `marathon.url` in modo che punti all'istanza `marathon-alice`, come illustrato di seguito:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Per verificare quale istanza di Marathon stia usando l'interfaccia della riga di comando, è possibile usare il comando `dcos config show` . Per ripristinare l'uso del servizio Marathon master, è possibile usare il comando `dcos config unset marathon.url`.


