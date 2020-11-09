---
author: baanders
description: file di inclusione per la configurazione dell'autenticazione locale per DefaultAzureCredential negli esempi di Gemelli digitali di Azure, con introduzione
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329514"
---
### <a name="set-up-local-azure-credentials"></a>Configurare le credenziali di Azure locali

Questo esempio usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte della libreria `Azure.Identity`) per l'autenticazione degli utenti con l'istanza di Gemelli digitali di Azure eseguita nel computer locale. Per altre informazioni sui diversi modi con cui un'app client può eseguire l'autenticazione con Gemelli digitali di Azure, vedere [*Procedura: Scrivere il codice di autenticazione dell'app*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]