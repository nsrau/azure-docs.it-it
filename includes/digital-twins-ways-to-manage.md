---
author: baanders
description: "file di inclusione per i dispositivi gemelli digitali di Azure: modalità di gestione dell'istanza"
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533893"
---
Questo articolo illustra come completare diverse operazioni di gestione usando l' [ **SDK** di .NET per dispositivi digitali gemelli di Azure (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). È anche possibile creare queste stesse chiamate di gestione usando gli SDK di altri linguaggi descritti in [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Tenere presente che tutti i metodi SDK sono disponibili in versioni sincrone e asincrone. Per le chiamate di paging, i metodi asincroni restituiscono `AsyncPageable<T>` mentre le versioni sincrone restituiscono `Pageable<T>` .

Un'altra opzione di gestione consiste nel chiamare direttamente le [**API REST**](/rest/api/azure-digitaltwins/) di Azure Digital gemelli per questa area dell'argomento.

Infine, è possibile completare le stesse operazioni di gestione usando l' **interfaccia** della riga di comando di Azure Digital gemelli. Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](../articles/digital-twins/how-to-use-cli.md).