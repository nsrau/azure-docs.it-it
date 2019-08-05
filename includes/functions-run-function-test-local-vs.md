---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592893"
---
1. Per eseguire la funzione, premere **F5**. Può essere necessario abilitare un'eccezione del firewall per consentire agli strumenti di gestire le richieste HTTP. I livelli di autorizzazione non vengono mai applicati durante l'esecuzione in locale.

2. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Runtime locale di Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<YOUR_NAME>` all'URL ed eseguire la richiesta. Di seguito è illustrata la risposta nel browser alla richiesta GET locale restituita dalla funzione: 

    ![Risposta localhost della funzione nel browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Per interrompere il debug, premere **MAIUSC+F5**.