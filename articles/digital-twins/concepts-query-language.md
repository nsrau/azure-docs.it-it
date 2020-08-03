---
title: Linguaggio di query
titleSuffix: Azure Digital Twins
description: Informazioni sulle nozioni di base del linguaggio Query Store di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 960fff073353375cd50b31bc7284134ca733f142
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488024"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informazioni sul linguaggio di query per i dispositivi gemelli digitali di Azure

Tenere presente che il centro dei dispositivi gemelli digitali di Azure è il [**grafo gemello**](concepts-twins-graph.md), costruito dai **gemelli digitali** e dalle **relazioni**. È possibile eseguire query su questo grafico per ottenere informazioni sui gemelli digitali e sulle relazioni in esso contenute. Queste query sono scritte in un linguaggio di query personalizzato simile a SQL denominato **Azure Digital gemells query Store linguaggio**.

Per inviare una query al servizio da un'app client, si userà l' [**API di query**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)dei dispositivi digitali gemelli di Azure. Questo consente agli sviluppatori di scrivere query e applicare filtri per trovare set di dispositivi gemelli digitali nel grafico gemello e altre informazioni sullo scenario dei gemelli digitali di Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come scrivere query e vedere esempi di codice client in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).
