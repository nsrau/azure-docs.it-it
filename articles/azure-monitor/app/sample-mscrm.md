---
title: Microsoft Dynamics CRM e Azure Application Insights | Documentazione Microsoft
description: Ottenere i dati di telemetria da Microsoft Dynamics CRM Online tramite Application Insights. Procedura dettagliate relative a installazione, recupero dei dati, visualizzazione ed esportazione.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534288"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Procedura dettagliata: Abilitazione della telemetria per Microsoft Dynamics CRM Online tramite Application Insights
Questo articolo descrive come ottenere i dati di telemetria da [Microsoft Dynamics CRM Online](https://www.dynamics.com/) usando [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Verrà illustrato il processo completo che prevede l'aggiunta di uno script di Application Insights all'applicazione, l'acquisizione dei dati e la visualizzazione dei dati.

> [!NOTE]
> [Esplorare la soluzione di esempio](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Aggiungere Application Insights a un'istanza di CRM Online nuova o esistente
Per monitorare l'applicazione, è necessario aggiungere un SDK di Application Insights all'applicazione. L'SDK invia dati di telemetria al [portale di Application Insights](https://portal.azure.com), dove è possibile usare potenti strumenti di analisi e diagnostica o esportare i dati nell'archivio.

### <a name="create-an-application-insights-resource-in-azure"></a>Creare una risorsa di Application Insights in Azure
1. Ottenere un [account in Microsoft Azure](https://azure.com/pricing). 
2. Accedere al [portale di Azure](https://portal.azure.com) e aggiungere una nuova risorsa di Application Insights, in cui i dati verranno elaborati e visualizzati.

    ![Clic su +, Servizi per gli sviluppatori, Application Insights.](./media/sample-mscrm/01.png)

    Scegliere ASP.NET come tipo di applicazione.
3. Seguire le istruzioni per [ottenere lo script JavaScript SDK per l'app](../../azure-monitor/app/javascript.md), copiare il frammento JavaScript e verificare che la chiave di strumentazione sia sostituita con il valore corretto per la risorsa Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Creare una risorsa Web JavaScript in Microsoft Dynamics CRM
1. Aprire l'istanza di CRM Online ed eseguire l'accesso con privilegi di amministratore.
2. In Microsoft Dynamics CRM fare clic su Impostazioni, su Personalizzazioni e su Personalizza il Sistema

    ![Impostazioni di Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Impostazioni > Personalizzazioni](./media/sample-mscrm/00002.png)

1. Creare una risorsa JavaScript.

    ![Finestra di dialogo Nuova risorsa Web](./media/sample-mscrm/07.png)

    Assegnarle un nome, selezionare **Script (JScript)** e aprire l'editor di testo.

    ![Aprire l'editor di testo](./media/sample-mscrm/00004.png)
2. Copiare il codice dall'SDK di Application Insights JavaScript in cui è stata configurata la chiave di strumentazione in precedenza. Durante la copia, assicurarsi di ignorare i tag di script. Vedere la schermata seguente:

    ![Impostare la chiave di strumentazione](./media/sample-mscrm/000005.png)

    Il codice contiene la chiave di strumentazione che identifica la risorsa di Application Insights.
3. Fare clic su Salva e quindi su Pubblica.

    ![Fare clic su Salva e quindi su Pubblica](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Form strumentazione
1. In Microsoft CRM Online aprire il form Account

    ![Modulo account](./media/sample-mscrm/00007.png)
2. Aprire il form Proprietà

    ![Proprietà del modulo](./media/sample-mscrm/00008.png)
3. Aggiungere la risorsa Web JavaScript creata in precedenza

    ![Menu Aggiungi](./media/sample-mscrm/13.png)

4. Salvare e pubblicare le personalizzazioni dei form.

## <a name="metrics-captured"></a>Metriche acquisite
È stata configurata l'acquisizione dei dati telemetria per il form. A ogni utilizzo, i dati verranno inviati alla risorsa di Application Insights.

Ecco alcuni esempi dei dati che verranno visualizzati.

#### <a name="application-health"></a>Integrità dell'applicazione
![Tempo di caricamento pagina di esempio](./media/sample-mscrm/15.png)

![Grafico delle visualizzazioni pagina di esempio](./media/sample-mscrm/16.png)

Eccezioni del browser:

![Grafico delle eccezioni del browser](./media/sample-mscrm/17.png)

Fare clic sul grafico per ottenere altri dettagli:

![Elenco delle eccezioni](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Utilizzo
![Utenti, sessioni e visualizzazioni pagine](./media/sample-mscrm/19.png)

![Grafici di sessione](./media/sample-mscrm/20.png)

![Versioni del browser](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Browser
![Scomposizione del tempo di caricamento della pagina](./media/sample-mscrm/22.png)

![Conteggio delle sessioni in base alla versione del browser](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Georilevazione
![Conteggio delle sessioni in base al paese](./media/sample-mscrm/24.png)

![Sessioni e utenti in base al paese](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Richieste visualizzazione pagina interna
![Riepilogo della visualizzazione pagina](./media/sample-mscrm/26.png)

![Cercare negli eventi della visualizzazione pagina](./media/sample-mscrm/27.png)

![Visualizzazioni pagina simili](./media/sample-mscrm/28.png)

![Proprietà delle visualizzazioni di pagina](./media/sample-mscrm/29.png)

![Pagine per sessione](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Codice di esempio
[Sfoglia il codice di esempio](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Per eseguire analisi più approfondite, è possibile [esportare i dati in Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Soluzione di esempio Microsoft Dynamics CRM
[Ecco la soluzione di esempio implementata in Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Altre informazioni
* [Informazioni su Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights per pagine Web](../../azure-monitor/app/javascript.md)
* [Altri esempi e procedure dettagliate](../../azure-monitor/app/app-insights-overview.md)
