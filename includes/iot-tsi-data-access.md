---
title: File di inclusione
description: File di inclusione
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125022"
---
## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Eseguire la procedura seguente per concedere l'accesso ai dati per un'entità utente:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights. Digitare **Time Series** nella casella di **ricerca**. Selezionare **ambiente Time Series** nei risultati della ricerca. 

3. Selezionare l'ambiente Time Series Insights nell'elenco.

4. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.
    ![Gestire l'origine Time Series Insights: ambiente](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Selezionare **Seleziona utente**.  Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Fare clic su **Seleziona** per confermare la selezione. 

    ![Gestire l'origine Time Series Insights: aggiunta](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Selezionare **Selezionare un ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:
   - Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente. 
   - In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali (non condivise) nell'ambiente.

     Selezionare **OK** per confermare la scelta del ruolo.

     ![Gestire l'origine Time Series Insights: selezione dell'utente](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Fare clic su **OK** nella pagina **Selezionare il ruolo utente**.

    ![Gestire l'origine Time Series Insights: selezione del ruolo](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. La pagina **Criteri di accesso ai dati** elenca gli utenti e i ruoli per ogni utente.

    ![Gestire l'origine Time Series Insights: risultati](media/iot-tsi-data-access/getstarted-grant-data-access5.png)