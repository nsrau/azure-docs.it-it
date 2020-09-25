---
title: Eseguire il debug di criteri di gestione API di Azure in Visual Studio Code | Microsoft Docs
description: Informazioni su come eseguire il debug dei criteri di gestione API di Azure usando l'estensione Visual Studio Code di gestione API di Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 4eb32243df219d721d7baae80984c45d0fc4cf25
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343020"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Eseguire il debug di criteri di gestione API di Azure in Visual Studio Code

I [criteri](api-management-policies.md) in gestione API di Azure forniscono potenti funzionalità che consentono agli editori di API di risolvere problemi trasversali, come l'autenticazione, l'autorizzazione, la limitazione, la memorizzazione nella cache e la trasformazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. 

Questo articolo descrive come eseguire il debug dei criteri di gestione API usando l' [estensione gestione API di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

Per creare un'istanza di livello Developer di gestione API, seguire prima questa [esercitazione](get-started-create-service-instance.md) .

Installare [Visual Studio Code](https://code.visualstudio.com/) e la versione più recente dell' [estensione gestione API di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="restrictions-and-limitations"></a>Restrizioni e limitazioni

Questa funzionalità è disponibile solo nel livello Developer di gestione API. Ogni istanza di gestione API supporta una sola sessione di debug simultanea.

## <a name="initiate-a-debugging-session"></a>Avviare una sessione di debug

1. Avviare Visual Studio Code
2. Passare all'estensione gestione API in Azure Extensions
3. Trovare l'istanza di gestione API di cui eseguire il debug
4. Trovare l'API e l'operazione di cui eseguire il debug
5. Fare clic con il pulsante destro del mouse sull'operazione e scegliere **Avvia debug criterio**

A questo punto, l'estensione tenterà di avviare e stabilire una sessione di debug con il gateway di gestione API.

![avviare il debug](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Inviare una richiesta di test
Quando viene stabilita la sessione di debug, l'estensione apre un nuovo editor che consente di creare e inviare una richiesta HTTP di test a questa operazione sfruttando l' [estensione client REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Si noterà che l'intestazione **OCP-gestione API-Debug** è già stata aggiunta alla richiesta. Questa intestazione è obbligatoria e il valore deve essere impostato sulla chiave di sottoscrizione di tutti gli accessi a livello di servizio per attivare la funzionalità di debug nel gateway di gestione API.

Modificare la richiesta HTTP nell'editor in base allo scenario di test. Quindi fare clic su **Send request** per inviare la richiesta di test al gateway di gestione API.

![inviare una richiesta di test](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Criteri di debug
Dopo l'invio della richiesta HTTP di test, l'estensione apre la finestra di debug che mostra i criteri effettivi di questa operazione e si arresta in corrispondenza del primo criterio effettivo. 

![criteri di debug](media/api-management-debug-policies/main-window.png)

Per seguire la pipeline dei criteri, è possibile eseguire un singolo passaggio attraverso singoli criteri o impostare un punto di interruzione in corrispondenza di un criterio e passare direttamente a tale criterio. 

Nel pannello **variabili** è possibile esaminare i valori delle variabili create dal sistema e create dall'utente. Nel pannello punti di **interruzione** è possibile visualizzare l'elenco di tutti i punti di interruzione impostati. Nel pannello **stack di chiamate** è possibile visualizzare l'ambito dei criteri effettivo corrente. 

Se si verifica un errore durante l'esecuzione del criterio, vengono visualizzati i dettagli dell'errore in corrispondenza del criterio in cui si è verificato il problema. 

![eccezioni](media/api-management-debug-policies/exception.png)

> [!TIP]
> Ricordarsi di uscire dalla sessione di debug facendo clic sul pulsante **Interrompi** al termine dell'operazione.


## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni sull' [estensione gestione API per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Segnala i problemi nel [repository GitHub](https://github.com/Microsoft/vscode-apimanagement)

