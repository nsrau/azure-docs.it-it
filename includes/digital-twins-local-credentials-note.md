---
author: baanders
description: file di inclusione per DefaultAzureCredential negli esempi di dispositivi gemelli digitali di Azure-Nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494487"
---
>[!NOTE]
> Questo esempio usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte della `Azure.Identity` libreria) per autenticare gli utenti con l'istanza di Azure Digital Twins quando viene eseguita nel computer locale. Con questo tipo di autenticazione, l'esempio cercherà le credenziali di Azure nell'ambiente locale, ad esempio un account di accesso da un'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) locale o in Visual Studio/Visual Studio Code.
>
> Per altre informazioni sull'uso di `DefaultAzureCredential` e altre opzioni di autenticazione, vedere [*procedura: scrivere codice di autenticazione dell'app*](../articles/digital-twins/how-to-authenticate-client.md).