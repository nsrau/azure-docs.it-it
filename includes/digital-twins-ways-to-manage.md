---
author: baanders
description: "file di inclusione per i dispositivi gemelli digitali di Azure: modalità di gestione dell'istanza"
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231413"
---
Questo articolo illustra come completare diverse operazioni di gestione usando l' [ **SDK** di .NET per dispositivi digitali gemelli di Azure (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). È anche possibile creare queste stesse chiamate di gestione usando gli SDK di altri linguaggi descritti in [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Tenere presente che tutti i metodi SDK sono disponibili in versioni sincrone e asincrone. Per le chiamate di paging, i metodi asincroni restituiscono `AsyncPageable<T>` mentre le versioni sincrone restituiscono `Pageable<T>` .

Un'altra opzione di gestione consiste nel chiamare direttamente le [**API REST**](/rest/api/azure-digitaltwins/) di Azure Digital gemelli per l'area di questo argomento tramite un client REST, ad esempio postazione. Per istruzioni su come eseguire questa operazione, vedere [*How-to: make requests with postazione*](../articles/digital-twins/how-to-use-postman.md).

Infine, è possibile completare le stesse operazioni di gestione usando l' **interfaccia** della riga di comando di Azure Digital gemelli. Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](../articles/digital-twins/how-to-use-cli.md).