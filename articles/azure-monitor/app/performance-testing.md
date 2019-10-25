---
title: Test di carico e prestazioni con applicazione Azure Insights | Microsoft Docs
description: Configurare test di carico e prestazioni con applicazione Azure Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 9c86b69239bed1a15c754ce28232b97e8439942b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819098"
---
# <a name="performance-testing"></a>Test delle prestazioni

> [!NOTE]
> Il servizio di test di carico basato sul cloud è stato deprecato. Altre informazioni sulla deprecazione, sulla disponibilità del servizio e sui servizi alternativi sono disponibili [qui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights consente di generare test di carico per i siti Web. Analogamente ai [test di disponibilità](monitor-web-app-availability.md), è possibile inviare richieste di base o richieste in più [passaggi](availability-multistep.md) dagli agenti di test di Azure in tutto il mondo. I test delle prestazioni consentono di simulare fino a 20.000 utenti simultanei per un massimo di 60 minuti.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Per creare un test delle prestazioni, è prima di tutto necessario creare una risorsa Application Insights. Se è già stata creata una risorsa, procedere alla sezione successiva.

Dal portale di Azure selezionare **Crea una risorsa** > **strumenti di sviluppo** > **Application Insights** e creare una risorsa Application Insights.

## <a name="configure-performance-testing"></a>Configurare il test delle prestazioni

Se è la prima volta che si crea un test delle prestazioni, selezionare **imposta organizzazione** e scegliere un'organizzazione DevOps di Azure come origine per i test delle prestazioni.

In **Configura**passare a **test delle prestazioni** e fare clic su **nuovo** per creare un test.

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

Per creare un test delle prestazioni di base, selezionare un tipo di test **manuale** e compilare le impostazioni desiderate per il test.

|Impostazione| Valore massimo
|----------|------------|
| Carico utente | 20.000 |
| Durata (minuti)  | 60 |  

Dopo aver creato il test, fare clic su **Esegui test**.

Al termine del test, vengono visualizzati risultati simili ai risultati seguenti:

![Risultati test](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurare il test Web di Visual Studio

Application Insights funzionalità avanzate di test delle prestazioni sono basate sui progetti di test di carico e prestazioni di Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passaggi successivi

* [Test Web in più passaggi](availability-multistep.md)
* [Test ping URL](monitor-web-app-availability.md)