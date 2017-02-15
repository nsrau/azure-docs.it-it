---
title: 'Procedura dettagliata: Monitorare Microsoft Dynamics CRM con Application Insights'
description: Ottenere i dati di telemetria da Microsoft Dynamics CRM Online tramite Application Insights. Procedura dettagliate relative a installazione, recupero dei dati, visualizzazione ed esportazione.
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9304b26711226fc9a7e672f59441ae65c0d5a023


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
1. Ottenere un [account in Microsoft Azure](http://azure.com/pricing). 
2. Accedere al [portale di Azure](https://portal.azure.com) e aggiungere una nuova risorsa di Application Insights, in cui i dati verranno elaborati e visualizzati.
   
    ![Clic su +, Servizi per gli sviluppatori, Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Scegliere ASP.NET come tipo di applicazione.
3. Aprire la scheda Avvio rapido e aprire lo script di codice.
   
    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenere aperta la pagina del codice** mentre si esegue il passaggio successivo in un'altra finestra del browser. È necessario tenere il codice a portata di mano. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Creare una risorsa Web JavaScript in Microsoft Dynamics CRM
1. Aprire l'istanza di CRM Online ed eseguire l'accesso con privilegi di amministratore.
2. In Microsoft Dynamics CRM fare clic su Impostazioni, su Personalizzazioni e su Personalizza il Sistema
   
    ![](./media/app-insights-sample-mscrm/04.png)
   
    ![](./media/app-insights-sample-mscrm/05.png)

    ![](./media/app-insights-sample-mscrm/06.png)

1. Creare una risorsa JavaScript.
   
    ![](./media/app-insights-sample-mscrm/07.png)
   
    Assegnarle un nome, selezionare **Script (JScript)** e aprire l'editor di testo.
   
    ![](./media/app-insights-sample-mscrm/08.png)
2. Copiare il codice da Application Insights. Durante la copia assicurarsi di ignorare i tag di script. Fare riferimento alla schermata illustrata di seguito:
   
    ![](./media/app-insights-sample-mscrm/09.png)
   
    Il codice contiene la chiave di strumentazione che identifica la risorsa di Application Insights.
3. Fare clic su Salva e quindi su Pubblica.
   
    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Form strumentazione
1. In Microsoft CRM Online aprire il form Account
   
    ![](./media/app-insights-sample-mscrm/11.png)
2. Aprire il form Proprietà
   
    ![](./media/app-insights-sample-mscrm/12.png)
3. Aggiungere la risorsa Web JavaScript creata in precedenza
   
    ![](./media/app-insights-sample-mscrm/13.png)
   
    ![](./media/app-insights-sample-mscrm/14.png)
4. Salvare e pubblicare le personalizzazioni dei form.

## <a name="metrics-captured"></a>Metriche acquisite
È stata configurata l'acquisizione dei dati telemetria per il form. A ogni utilizzo, i dati verranno inviati alla risorsa di Application Insights.

Ecco alcuni esempi dei dati che verranno visualizzati.

#### <a name="application-health"></a>Integrità dell'applicazione
![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Eccezioni del browser:

![](./media/app-insights-sample-mscrm/17.png)

Fare clic sul grafico per ottenere altri dettagli:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso
![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Browser
![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Georilevazione
![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Richieste visualizzazione pagina interna
![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Codice di esempio
[Sfoglia il codice di esempio](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Per eseguire analisi più approfondite, è possibile [esportare i dati in Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Soluzione di esempio Microsoft Dynamics CRM
[Ecco la soluzione di esempio implementata in Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Altre informazioni
* [Informazioni su Azure Application Insights](app-insights-overview.md)
* [Application Insights per pagine Web](app-insights-javascript.md)
* [Altri esempi e procedure dettagliate](app-insights-code-samples.md)




<!--HONumber=Nov16_HO3-->


