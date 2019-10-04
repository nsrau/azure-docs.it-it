---
title: Prestazioni e test di carico con Azure Application Insights | Microsoft Docs
description: Configurare i test di carico con Azure Application Insights e delle prestazioni
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305230"
---
# <a name="performance-testing"></a>Test delle prestazioni

> [!NOTE]
> Servizio di test di carico basati sul cloud è stato deprecato. Sono disponibili altre informazioni sulla deprecazione, la disponibilità del servizio e servizi alternativi [qui](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights consente di generare i test di carico per i siti Web. Ad esempio [test di disponibilità](monitor-web-app-availability.md), è possibile inviare entrambi richieste basilare o [richieste in più passaggi](availability-multistep.md) da Azure agenti tutto il mondo di test. Test delle prestazioni consentono di simulare fino a 20.000 utenti simultanei fino a 60 minuti.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Per creare un test delle prestazioni, è necessario creare una risorsa di Application Insights. Se già stata creata una risorsa di procedere alla sezione successiva.

Dal portale di Azure, selezionare **crea una risorsa** > **strumenti di sviluppo** > **Application Insights** e creare un Application Insights risorsa.

## <a name="configure-performance-testing"></a>Configurare il test delle prestazioni

Se questa è la prima volta la creazione di selezione di test delle prestazioni **organizzazione impostare** e scegliere un'organizzazione di DevOps di Azure come origine per i test delle prestazioni.

Sotto **configura**passare alla **test delle prestazioni** e fare clic su **New** per creare un test.

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

Per creare un test delle prestazioni di base, selezionare un tipo di test **Test manuale** e compilare le impostazioni desiderate per il test.

|Impostazione| Valore massimo
|----------|------------|
| Carico utente | 20.000 |
| Durata (minuti)  | 60 |  

Dopo aver creato il test, fare clic su **eseguire test**.

Una volta completato il test, verranno visualizzati risultati simili ai risultati seguenti:

![Risultati dei test](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurare test web di Visual Studio

I progetti di test delle prestazioni avanzate funzionalità di test si avvalgono di carico e prestazioni di Visual Studio Application Insights.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Passaggi successivi

* [Test web in più passi](availability-multistep.md)
* [Test di ping URL](monitor-web-app-availability.md)