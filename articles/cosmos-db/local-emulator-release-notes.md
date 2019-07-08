---
title: 'Emulatore Azure Cosmos: download e note sulla versione'
description: Leggere le note sulla versione dell'emulatore Azure Cosmos e scaricarla.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332146"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Usare l'emulatore Azure Cosmos per sviluppo e test locali

Questo articolo include le note sulla versione dell'emulatore Azure Cosmos con un elenco di aggiornamenti delle funzionalità apportati in ogni versione. Inoltre, include la versione più recente dell'emulatore disponibile per il download.

## <a name="download"></a>Download

| | |
|---------|---------|
|**Download MSI**|[Area download Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introduzione**|[Sviluppare in locale con l'emulatore Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Note sulla versione

### <a name="243"></a>2.4.3

- Avvio del servizio MongoDB disattivato per impostazione predefinita. Per impostazione predefinita è abilitato solo l'endpoint SQL. L'utente deve avviare l'endpoint manualmente usando l'opzione "/EnableMongoDbEndpoint" della riga di comando dell'emulatore. Adesso è come tutti gli altri endpoint di servizio, ad esempio Gremlin, Cassandra e Table.
- Risolto un bug nell'emulatore quando si inizia con "/AllowNetworkAccess" in cui gli endpoint Gremlin, Cassandra e Table non gestiscono correttamente le richieste provenienti da client esterni.
- Aggiungere porte di connessione diretta alle impostazioni delle regole del Firewall.

### <a name="240"></a>2.4.0

- Risolto un problema con l'emulatore relativo al mancato avvio se nel computer host sono presenti app di monitoraggio di rete, come Pulse Client.
