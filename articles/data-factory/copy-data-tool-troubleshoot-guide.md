---
title: Risolvere i problemi relativi allo strumento Copia dati in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi agli strumenti Copia dati in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388404"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Risolvere i problemi relativi allo strumento Copia dati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi relativi allo strumento Copia dati in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Codice di errore: non è possibile convalidare lo strumento Copia dati

- **Sintomi**: nel primo passaggio dello strumento copia dati viene visualizzato il messaggio di avviso "Impossibile convalidare".
- **Cause**: questa situazione può verificarsi quando tutti i cookie di terze parti sono disabilitati.
- **Risoluzione**: 
    - Usare Internet Explorer o il browser Microsoft Edge.
    - Se si usa il browser Chrome, seguire le istruzioni riportate di seguito per aggiungere l'eccezione dei cookie per *microsoftonline.com* e *Windows.NET*.
        1.  Aprire il browser Chrome.
        2.  Fare clic sulla chiave o su tre righe a destra (personalizzare e controllare Google Chrome).
        3.  Fare clic su **Impostazioni**.
        4.  Cerca i **cookie** o passa alla **privacy** in impostazioni avanzate.
        5.  Selezionare **impostazioni contenuto**.    
        6.  I cookie devono essere impostati in modo da **consentire l'impostazione dei dati locali (scelta consigliata)**.
        7.  Fare clic su **Gestisci eccezioni**. In **schema hostname** immettere quanto segue e verificare che **Consenti** sia il set di comportamenti.
            - login.microsoftonline.com
            - login.windows.net
        8.  Chiudere il browser e riavviarlo.
    - Se si usa il browser Firefox, seguire le istruzioni riportate di seguito per aggiungere l'eccezione dei cookie.
        1. Dal menu Firefox passare a **strumenti**  >  **Opzioni**.
        2. In **Privacy**  >  **cronologia**privacy, è possibile che l'impostazione corrente **usi impostazioni personalizzate per la cronologia**.
        3. In **accetta cookie di terze parti**, l'impostazione corrente potrebbe **non essere mai**, quindi è necessario fare clic su **eccezioni** a destra per aggiungere i siti seguenti.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Chiudere il browser e riavviarlo. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Codice di errore: Impossibile aprire la pagina di accesso e immettere la password

- **Sintomi**: copia dati strumento reindirizza l'utente alla pagina di accesso, ma la pagina di accesso non viene visualizzata correttamente.
- **Cause**: questo problema può verificarsi se l'ambiente di rete è stato modificato dalla rete di Office alla rete domestica. Sono presenti alcune cache nei browser. 
- **Risoluzione**: 
    1.  Chiudere il browser e riprovare. Se il problema persiste, procedere al passaggio successivo.   
    2.  Se si utilizza il browser Internet Explorer, provare ad aprirlo in modalità privata (premere "Ctrl" + "Maiusc" + "P"). Se si usa il browser Chrome, provare ad aprirlo in modalità in incognito (premere "Ctrl" + "Maiusc" + "N"). Se il problema persiste, procedere al passaggio successivo. 
    3.  Provare a usare un altro browser. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida sulle prestazioni dei flussi di dati di mapping di Azure Data Factory](concepts-data-flow-performance.md)
