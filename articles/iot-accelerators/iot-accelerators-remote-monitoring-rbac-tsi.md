---
title: Controllo - Azure di accesso ai dati di monitoraggio remoti | Microsoft Docs
description: Questo articolo illustra come configurare i controlli di accesso ai dati di telemetria di Time Series Insights Explorer nell'acceleratore di soluzioni di monitoraggio remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827266"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Configurare i controlli di accesso per i dati di telemetria di Time Series Insights Explorer

Questo articolo illustra come configurare i controlli di accesso di Time Series Insights Explorer nell'acceleratore di soluzioni di monitoraggio remoto. Per consentire agli utenti dell'acceleratore di soluzioni di accedere a Time Series Insights Explorer, è necessario concedere a ogni utente l'accesso ai dati.

I criteri di accesso ai dati concedono le autorizzazioni per eseguire query sui dati, modificare i dati di riferimento nell'ambiente e condividere le prospettive e le query salvate associate all'ambiente.

## <a name="grant-data-access"></a>Concedere l'accesso ai dati

Eseguire la procedura seguente per concedere l'accesso ai dati per un'entità utente:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights. Digitare **Time Series** nella casella di **ricerca**. Selezionare **ambiente Time Series** nei risultati della ricerca. 

3. Selezionare l'ambiente Time Series Insights nell'elenco.

4. Selezionare **Criteri di accesso ai dati** e quindi selezionare **+ Aggiungi**.
    ![Gestire l'origine Time Series Insights: ambiente](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Selezionare **Seleziona utente**.  Cercare il nome utente o l'indirizzo di posta elettronica per individuare l'utente che si vuole aggiungere. Fare clic su **Seleziona** per confermare la selezione. 

    ![Gestire l'origine Time Series Insights: aggiunta](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Selezionare **Selezionare un ruolo**. Scegliere il ruolo di accesso appropriato per l'utente:
   - Selezionare **Collaboratore** se si vuole consentire all'utente di modificare i dati di riferimento e condividere le prospettive e le query salvate con altri utenti dell'ambiente. 
   - In alternativa, selezionare **Lettore** per consentire all'utente di eseguire query sui dati e salvare query personali (non condivise) nell'ambiente.

     Selezionare **OK** per confermare la scelta del ruolo.

     ![Gestire l'origine Time Series Insights: selezione dell'utente](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Fare clic su **OK** nella pagina **Selezionare il ruolo utente**.

    ![Gestire l'origine Time Series Insights: selezione del ruolo](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. La pagina **Criteri di accesso ai dati** elenca gli utenti e i ruoli per ogni utente.

    ![Gestire l'origine Time Series Insights: risultati](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come vengono concessi i controlli di accesso per Time Series Insights Explorer nell'acceleratore di soluzioni di monitoraggio remoto.

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Architettura della soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Per altre informazioni sulla personalizzazione della soluzione di monitoraggio remoto, vedere [Customize and redeploy a microservice](iot-accelerators-microservices-example.md) (Personalizzare e ridistribuire un microservizio)
<!-- Next tutorials in the sequence -->