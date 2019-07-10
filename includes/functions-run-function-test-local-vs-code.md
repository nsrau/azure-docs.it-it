---
title: File di inclusione
description: File di inclusione
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455167"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Azure Functions Core Tools consente di eseguire un progetto Funzioni di Azure nel computer di sviluppo locale.

1. Per testare la funzione, impostare un punto di interruzione nel codice della funzione e premere F5 per avviare il progetto di app per le funzioni. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**.

1. Nel pannello **Terminale** copiare l'endpoint dell'URL della funzione attivata da HTTP. Questo URL include la chiave di funzione, che viene passata per il `code` parametro di query.

    ![Output locale di Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. L'esecuzione viene sospesa quando viene raggiunto il punto di interruzione.

1. Quando si continua l'esecuzione, viene mostrata la risposta seguente nel browser per la richiesta GET:

    ![Risposta localhost della funzione nel browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Per interrompere il debug, premere MAIUSC+F5.