---
title: Linguaggio di query
titleSuffix: Azure Digital Twins
description: Informazioni sulle nozioni di base del linguaggio di query di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0faa0af3bb793cbd75139ab42edd0aa7e20de78a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543844"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informazioni sul linguaggio di query per i dispositivi gemelli digitali di Azure

Tenere presente che il centro dei dispositivi gemelli digitali di Azure è il [**grafo gemello**](concepts-twins-graph.md), costruito dai **gemelli digitali** e dalle **relazioni**. È possibile eseguire query su questo grafico per ottenere informazioni sui gemelli digitali e sulle relazioni in esso contenute. Queste query sono scritte in un linguaggio di query personalizzato simile a SQL denominato **linguaggio di query di Azure Digital gemelli**.

Per inviare una query al servizio da un'app client, si userà l' [**API di query**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)dei dispositivi digitali gemelli di Azure. Questo consente agli sviluppatori di scrivere query e applicare filtri per trovare set di dispositivi gemelli digitali nel grafico gemello e altre informazioni sullo scenario dei gemelli digitali di Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come scrivere query e vedere esempi di codice client in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).
