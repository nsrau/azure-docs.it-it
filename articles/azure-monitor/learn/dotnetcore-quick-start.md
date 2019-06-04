---
title: Guida introduttiva per Azure Application Insights | Microsoft Docs
description: Fornisce istruzioni per configurare rapidamente un'app Web ASP.NET Core per il monitoraggio con Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a4bbb43cb00eeb5a9e741016e1648ce915935eb9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236584"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Iniziare a monitorare l'applicazione Web ASP.NET Core

Con Azure Application Insights, è possibile monitorare facilmente la disponibilità, le prestazioni e l'uso dell'applicazione Web. È anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente. 

Questa guida introduttiva illustra l'aggiunta di Application Insights SDK a un'applicazione Web ASP.NET Core esistente. Per informazioni sulla configurazione di Application Insights senza Visual Studio, vedere questo [articolo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

- [Installare Visual Studio 2019](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
  - Sviluppo Web e ASP.NET
  - Sviluppo di Azure
- [Installare .NET Core 2.0 SDK](https://www.microsoft.com/net/core)
- Saranno necessarie una sottoscrizione di Azure e un'applicazione Web .NET Core esistente.

Se non si ha un'applicazione Web ASP.NET Core, è possibile usare la guida dettagliata per [creare un'app ASP.NET Core e aggiungere Application Insights.](../../azure-monitor/app/asp-net-core.md)

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Abilitare Application Insights

Application Insights può raccogliere dati di telemetria da un'applicazione connessa a Internet, indipendentemente dal fatto che sia in esecuzione in locale o nel cloud. Usare la procedura seguente per iniziare a visualizzare questi dati.

1. Selezionare **Crea una risorsa** > **Strumenti di sviluppo** > **Application Insights**.

   ![Aggiunta di una risorsa di Application Insights](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    Verrà visualizzata una casella di configurazione. Usare la tabella seguente per completare i campi di input.

    | Impostazioni        |  Valore           | DESCRIZIONE  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore globalmente univoco | Nome che identifica l'app da monitorare |
   | **Tipo di applicazione** | Applicazione Web ASP.NET | Tipo di app da monitorare |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del nuovo gruppo di risorse per l'hosting dei dati di Application Insights |
   | **Posizione** | Stati Uniti orientali | Scegliere una località nelle vicinanze o vicina a quella in cui è ospitata l'app |

2. Fare clic su **Create**(Crea).

## <a name="configure-app-insights-sdk"></a>Configurare Application Insights SDK

1. Aprire il **progetto** dell'app Web ASP.NET Core in Visual Studio, quindi fare clic con il pulsante destro del mouse sul nome dell'app in **Esplora soluzioni** e scegliere **Aggiungi** > **Application Insights Telemetry**.

    ![Aggiungere Application Insights Telemetry](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Fare clic sul pulsante **Inizia**

3. Selezionare l'account e la sottoscrizione > selezionare la **risorsa esistente** creata nel portale di Azure > fare clic su **Registra**.

4. Selezionare **Debug** > **Avvia senza eseguire debug** (CTRL+F5) per avviare l'app.

    ![Menu della panoramica di Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Prima che i dati vengano visualizzati nel portale trascorrono 3-5 minuti. Se questa app è un'app di test a basso traffico, occorre ricordare che la maggior parte delle metriche viene acquisita solo in presenza di operazioni o richieste attive.

## <a name="start-monitoring-in-the-azure-portal"></a>Avviare il monitoraggio nel portale di Azure

1. Riaprire la pagina **Panoramica** di Application Insights nel portale di Azure selezionando **Home** e nelle risorse recenti selezionare la risorsa creata in precedenza per visualizzare informazioni dettagliate sull'applicazione attualmente in esecuzione.

   ![Menu della panoramica di Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Fare clic su **Mappa delle applicazioni** per ottenere un layout visivo delle relazioni di dipendenza tra i componenti dell'applicazione. Ogni componente mostra indicatori KPI come carico, prestazioni, errori e avvisi.

   ![Mappa delle applicazioni](./media/dotnetcore-quick-start/5appmap.png)

3. Fare clic sull'icona di **App Analytics** ![Icona Mappa app](./media/dotnetcore-quick-start/006.png) **Visualizza in Analytics**. Verrà aperta la finestra **Application Insights - Analisi**, che fornisce un linguaggio di query avanzato per l'analisi di tutti i dati raccolti da Application Insights. In questo caso viene generata una query che esegue il rendering del conteggio delle richieste sotto forma di grafico. È possibile scrivere query personalizzate per analizzare altri dati.

   ![Grafico di analisi delle richieste di un utente in un periodo di tempo](./media/dotnetcore-quick-start/6analytics.png)

4. Tornare alla pagina **Panoramica** ed esaminare i dashboard degli indicatori KPI.  Questo dashboard fornisce statistiche relative all'integrità dell'applicazione, ad esempio il numero di richieste in ingresso, la durata delle richieste ed eventuali errori che si sono verificati. 

   ![Grafici Integrità - Panoramica sequenza temporale](./media/dotnetcore-quick-start/7kpidashboards.png)

5. A sinistra fare clic su **Metrica**. Usare Esplora metriche per esaminare l'integrità e l'utilizzo della risorsa. È possibile fare clic su **Aggiungi nuovo grafico** per creare altre visualizzazioni personalizzate oppure selezionare **Modifica** per modificare tipi, altezze, tavolozza dei colori, raggruppamenti e metriche dei grafici esistenti. È ad esempio possibile creare un grafico che visualizza il tempo medio di caricamento delle pagine del browser selezionando "Tempo di caricamento della pagina del browser" nel menu a discesa Metriche e "Media" nell'aggregazione. Per altre informazioni su Esplora metriche di Azure, vedere [Introduzione Esplora metriche di Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Scheda Metriche: grafico sul tempo medio di caricamento delle pagine del browser](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Video esterno dettagliato sulla [configurazione da zero di Application Insights con .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Video esterno dettagliato sulla [configurazione da zero di Application Insights con .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync).

## <a name="clean-up-resources"></a>Pulire le risorse
Dopo aver completato i test, è possibile eliminare il gruppo di risorse e le risorse correlate. A tale scopo, seguire questa procedura.

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic su **myResourceGroup**.
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **myResourceGroup** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Rilevare e diagnosticare le eccezioni di run-time ](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
