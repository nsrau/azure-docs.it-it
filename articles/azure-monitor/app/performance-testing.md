---
title: Test di carico e prestazioni con Azure Application Insights Documenti Microsoft
description: Configurare test di carico e prestazioni con Azure Application InsightsSet up performance and load tests with Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669863"
---
# <a name="performance-testing"></a>Test delle prestazioni

> [!NOTE]
> Il servizio di test di carico basato su cloud è deprecato. Ulteriori informazioni sulla deprecazione, sulla disponibilità del servizio e sui servizi alternativi sono disponibili [qui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights consente di generare test di carico per i siti Web. Analogamente ai test di [disponibilità,](monitor-web-app-availability.md)è possibile inviare richieste di base o [richieste in più passaggi](availability-multistep.md) da agenti di test di Azure in tutto il mondo. I test delle prestazioni consentono di simulare fino a 20.000 utenti simultanei per un massimo di 60 minuti.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Per creare un test delle prestazioni, è innanzitutto necessario creare una risorsa di Application Insights.In order to create a performance test, you first need to create an Application Insights resource. Se è già stata creata una risorsa, passare alla sezione successiva.

Nel portale di Azure selezionare **Crea una risorsa** > Strumenti di sviluppo Application Insights e creare una risorsa di Application Insights.From the Azure portal, select Create a**resource Developer Tools** > **Application Insights** and create an Application Insights resource.

## <a name="configure-performance-testing"></a>Configurare i test delle prestazioni

Se è la prima volta che si crea il test delle prestazioni, selezionare **Imposta organizzazione** e scegliere un'organizzazione DevOps di Azure come origine per i test delle prestazioni.

In **Configura**passare a **Test delle prestazioni** e fare clic su **Nuovo** per creare un test.

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

Per creare un test delle prestazioni di base, selezionare un tipo di test **Test manuale** e compilare le impostazioni desiderate per il test.

|Impostazione| Valore massimo
|----------|------------|
| Carico utente | 20.000 |
| Durata (minuti)  | 60 |  

Dopo aver creato il test, fare clic su **Esegui test**.

Una volta completato il test, vedrai risultati simili ai risultati seguenti:

![Risultati dei test](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurare il test Web di Visual StudioConfigure Visual Studio web test

Le funzionalità di test delle prestazioni avanzate di Application Insights si basano sui progetti di test di carico e prestazioni di Visual Studio.Application Insights advanced performance testing capabilities are built on top of Visual Studio performance and load test projects.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passaggi successivi

* [Test Web in più passaggi](availability-multistep.md)
* [Test ping url](monitor-web-app-availability.md)
