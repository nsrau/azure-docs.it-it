---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056696"
---
1. Per eseguire la funzione, premere F5 in Visual Studio. Può essere necessario abilitare un'eccezione del firewall in modo che gli strumenti possano gestire le richieste HTTP. I livelli di autorizzazione non vengono mai applicati quando si esegue una funzione in locale.

2. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Runtime locale di Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<YOUR_NAME>` a questo URL ed eseguire la richiesta. L'immagine seguente mostra la risposta nel browser alla richiesta GET locale restituita dalla funzione: 

    ![Risposta localhost della funzione nel browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Per arrestare il debug, premere MAIUSC+F5 in Visual Studio.