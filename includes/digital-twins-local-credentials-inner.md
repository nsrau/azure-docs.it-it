---
author: baanders
description: file di inclusione per la configurazione dell'autenticazione locale per DefaultAzureCredential negli esempi di Gemelli digitali di Azure, senza introduzione
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329522"
---
Con `DefaultAzureCredential`, l'esempio cercherà le credenziali nell'ambiente locale, ad esempio un account di accesso di Azure nell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) locale o in Visual Studio o Visual Studio Code. Per questo motivo è necessario *accedere ad Azure in locale* tramite uno di questi meccanismi per configurare le credenziali per l'esempio.

Se si usa Visual Studio o Visual Studio Code per eseguire l'esempio di codice, assicurarsi di aver eseguito l'accesso a tale editor con le stesse credenziali di Azure che si vogliono usare per accedere all'istanza di Gemelli digitali di Azure.

In caso contrario, è possibile [installare l'interfaccia della riga di comando di Azure locale](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), avviare un prompt dei comandi nel computer ed eseguire il comando `az login` per accedere all'account Azure. Dopo l'accesso, quando si esegue l'esempio di codice, si dovrebbe accedere automaticamente.