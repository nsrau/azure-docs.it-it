---
title: Registrare l'applicazione - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Guida dettagliata sulla procedura per registrare una nuova app con il Servizio decisionale personalizzato di Azure.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: f642e0d4e21c180a92b4b76d05fb2c9bac9f2976
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219737"
---
# <a name="register-your-application"></a>Registrare l'applicazione

Per usare il Servizio decisionale personalizzato per l'applicazione, registrarsi nel portale. Questo articolo spiega come.

1. Passare alla [pagina iniziale](https://ds.microsoft.com/) del Servizio decisionale personalizzato. Sulla barra multifunzione fare clic su **My Portal** (Il mio portale), come mostrato nell'immagine:

    ![My Portal (Il mio portale)](./media/portal.png)

    Se non si è eseguito l'accesso, il portale chiede di eseguire l'accesso con l'[account Microsoft](https://account.microsoft.com/account). Dopo aver eseguito l'accesso, il portale mostra l'account Microsoft nell'angolo superiore destro della pagina.

2. Per registrare l'applicazione fare clic sul pulsante **Nuova app**.

3. Nella finestra di dialogo scegliere un ID app per l'applicazione. Il Servizio decisionale personalizzato richiede un ID univoco per ogni applicazione. Se questo ID è già assegnato a un altro utente, viene chiesto di selezionarne uno diverso.

4. Specificare un'API del set di azioni. Questa impostazione è un feed RSS o Atom che comunica il contenuto disponibile per l'applicazione al Servizio decisionale personalizzato. Immettere un nome per il feed e immettere l'URL da cui è fornito. Per eseguire questo passaggio in un secondo momento, scegliere il pulsante **Feed** e quindi fare clic sul pulsante **Nuovo feed**. Un esempio che crea un feed RSS è descritto più avanti.

5. Per registrare l'applicazione, selezionare la casella di controllo **App personalizzata** nell'angolo inferiore sinistro. Immettere una [stringa di connessione](../../storage/common/storage-configure-connection-string.md) per l'account di archiviazione di Azure in cui i dati dell'applicazione sono registrati. Per altre informazioni su come creare un account di archiviazione, vedere [Come creare, gestire o eliminare un account di archiviazione](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Passaggi successivi

* Iniziare a ottimizzare una [pagina Web](custom-decision-service-get-started-browser.md) o un'[app per smartphone](custom-decision-service-get-started-app.md).
* Esaminare un'[esercitazione](custom-decision-service-tutorial-news.md) per un esempio più dettagliato.
* Consultare le [informazioni di riferimento sull'API](custom-decision-service-api-reference.md) per altre informazioni sulla funzionalità fornita.
