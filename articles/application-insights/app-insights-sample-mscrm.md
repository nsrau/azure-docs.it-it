<properties 
	pageTitle="Procedura dettagliata: Monitorare Microsoft CRM con Application Insights" 
	description="Ottenere i dati di telemetria da Microsoft CRM Online tramite Application Insights. Procedura dettagliate relative a installazione, recupero dei dati, visualizzazione ed esportazione." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="klin"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# Procedura dettagliata: Abilitazione della telemetria per Microsoft CRM Online tramite Application Insights

Questo articolo descrive come ottenere i dati di telemetria da [Microsoft CRM Online](https://www.dynamics.com/) usando [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/). Verrà illustrato il processo completo che prevede l'aggiunta di uno script di Application Insights all'applicazione, l'acquisizione dei dati e la visualizzazione dei dati.

>[AZURE.NOTE] [Browse the sample code](https://dynamicsandappinsights.codeplex.com/).

## Aggiungere Application Insights a un'istanza di CRM Online nuova o esistente 

Per monitorare l'applicazione, è necessario aggiungere un SDK di Application Insights all'applicazione. L'SDK invia dati di telemetria al [portale di Application Insights](https://portal.azure.com), dove è possibile usare potenti strumenti di analisi e diagnostica o esportare i dati nell'archivio.

### Creare una risorsa di Application Insights in Azure

1. Ottenere un [account in Microsoft Azure](http://azure.com/pricing). 
2. Accedere al [portale di Azure](https://portal.azure.com) e aggiungere una nuova risorsa di Application Insights, in cui i dati verranno elaborati e visualizzati.

    ![Clic su +, Servizi per gli sviluppatori, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Scegliere ASP.NET come tipo di applicazione.

3. Aprire la scheda Avvio rapido e aprire lo script di codice.

    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenere aperta la pagina del codice** mentre si esegue il passaggio successivo in un'altra finestra del browser. È necessario tenere il codice a portata di mano.

### Creare una risorsa Web JavaScript in Microsoft CRM

1. Aprire l'istanza di CRM Online ed eseguire l'accesso con privilegi di amministratore.
2. In Microsoft Dynamics CRM fare clic su Impostazioni, su Personalizzazioni e su Sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Creare una risorsa JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Assegnarle un nome, selezionare **Script (JScript)** e aprire l'editor di testo.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copiare il codice da Application Insights.

    ![](./media/app-insights-sample-mscrm/09.png)

    Il codice contiene la chiave di strumentazione che identifica la risorsa di Application Insights.

5. Fare clic su Salva e quindi su Pubblica.

    ![](./media/app-insights-sample-mscrm/10.png)

### Form strumentazione

1. In Microsoft CRM Online aprire il form Account

    ![](./media/app-insights-sample-mscrm/11.png)

2. Aprire il form Proprietà

    ![](./media/app-insights-sample-mscrm/12.png)

3. Aggiungere la risorsa Web JavaScript creata in precedenza

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Salvare e pubblicare le personalizzazioni dei form.


## Metriche acquisite

È stata configurata l'acquisizione dei dati telemetria per il form. A ogni utilizzo, i dati verranno inviati alla risorsa di Application Insights.

Ecco alcuni esempi dei dati che verranno visualizzati.

#### Integrità dell'applicazione

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Eccezioni del browser:

![](./media/app-insights-sample-mscrm/17.png)

Fare clic sul grafico per ottenere altri dettagli:

![](./media/app-insights-sample-mscrm/18.png)

#### Uso

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### Browser

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### Georilevazione

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### Richieste visualizzazione pagina interna

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## Codice di esempio

[Sfoglia il codice di esempio](https://dynamicsandappinsights.codeplex.com/).

## Power BI

Per eseguire analisi più approfondite, è possibile [esportare i dati in Microsoft Power BI](app-insights-export-power-bi.md).

## Soluzione di esempio CRM

Di seguito viene affrontata la soluzione campione in CRM per iniziare ad acquisire i dati di telemetria.https://dynamicsandappinsights.codeplex.com/

## Altre informazioni

* [Informazioni su Azure Application Insights](app-insights-overview.md)
* [Application Insights per pagine Web](app-insights-javascript.md)
* [Altri esempi e procedure dettagliate](app-insights-code-samples.md)

 

<!---HONumber=August15_HO6-->