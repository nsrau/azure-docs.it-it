<properties
    pageTitle="Annotazioni sulla versione per Application Insights | Microsoft Azure"
    description="Aggiungere indicatori della distribuzione o della build ai grafici di Esplora metriche in Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="06/09/2016"
    ms.author="awills"/>

# Annotazioni sulla versione in Application Insights

Le annotazioni sulla versione nei grafici di [Esplora metriche](app-insights-metrics-explorer.md) indicano dove è stata distribuita una nuova build e consentono di verificare facilmente se le modifiche hanno avuto effetto sulle prestazioni dell'applicazione. Possono essere create automaticamente dal [sistema di compilazione di Visual Studio Team Services](https://www.visualstudio.com/it-IT/get-started/build/build-your-app-vs) ed è anche possibile [crearle da PowerShell](#create-annotations-from-powershell).

![Esempi di annotazioni con correlazione visibile con il tempo di risposta del server](./media/app-insights-annotations/00.png)

Le annotazioni sulla versione sono una funzionalità della build basata sul cloud e del servizio versione di Visual Studio Team Services.

## Installare l'estensione Annotazioni (una volta)

Per creare le annotazioni sulla versione, sarà necessario installare una delle numerose estensioni di Team Service disponibili in Visual Studio Marketplace.

1. Accedere al progetto in [Visual Studio Team Services](https://www.visualstudio.com/it-IT/get-started/setup/sign-up-for-visual-studio-online).
2. Aprire Visual Studio Marketplace, [individuare l'estensione delle annotazioni sulla versione](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) e aggiungerla all'account di Team Services.

![Nell'angolo in alto a destra della pagina Web di Team Services aprire il marketplace. Scegliere Visual Studio Team Services, quindi nella sezione relativa a compilazione e rilascio scegliere Altre informazioni.](./media/app-insights-annotations/10.png)

È necessario eseguire questa operazione una sola volta per l'account di Visual Studio Team Services. Le annotazioni sulla versione ora possono essere configurate per qualsiasi progetto nell'account.

## Ottenere una chiave API da Application Insights.

Questa operazione è necessaria per ogni modello di versione per cui si vogliono creare annotazioni sulla versione.


1. Accedere al [portale di Microsoft Azure](https://portal.azure.com) e aprire la risorsa di Application Insights che monitora l'applicazione o [crearne una nuova](app-insights-overview.md), se necessario.
2. Aprire **Impostazioni**, **Accesso all'API** ed eseguire una copia di **ID di Application Insights**.

    ![In portal.azure.com, aprire la risorsa di Application Insights e scegliere Settings. Aprire API Access. Copiare l'ID applicazione](./media/app-insights-annotations/20.png)

2. In una finestra del browser separata, aprire (o creare) il modello di versione che gestisce le distribuzioni da Visual Studio Team Services.

    Aggiungere un'attività e scegliere l'attività di annotazione sulla versione di Application Insights dal menu.

    Incollare l'**ID di Application Insights** copiato dal pannello di accesso all'API.

    ![In Visual Studio Team Services, aprire Release, selezionare una versione di rilascio e scegliere Edit. Fare clic su Add Task e scegliere l'annotazione sulla versione di Application Insights. Incollare l'Id di Application Insights.](./media/app-insights-annotations/30.png)

3. Impostare il campo **Chiave API** su una variabile `$(ApiKey)`.

4. Nel Pannello di accesso all'API, creare una nuova chiave API e richiedere una copia.

    ![Nel Pannello di accesso all'API nella finestra di Azure, fare clic su Crea chiave API. Inserire un commento, controllare le annotazioni di scrittura e fare clic su Genera chiave. Copiare la nuova chiave.](./media/app-insights-annotations/40.png)

4. Aprire la scheda Configurazione del modello di versione.

    Creare una definizione di variabile per `ApiKey`.

    Incollare la chiave API per la definizione della variabile della chiave API.

    ![Nella finestra Servizi per i team, selezionare la scheda Configurazione e fare clic su Aggiungi variabile. Impostare il nome della chiave API e incollare la chiave appena generata nel Valore.](./media/app-insights-annotations/50.png)


5. Infine fare clic su **Salva** per salvare la definizione della versione.

## Creare annotazioni da PowerShell

È anche possibile creare le annotazioni da qualsiasi processo desiderato, senza usare VS Team System.

Ottenere lo [script di Powershell da GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Usarlo nel modo seguente:

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Ottenere `applicationId` e `apiKey` dalla risorsa di Application Insights: aprire le impostazioni, accedere all'API e copiare l'ID applicazione. Fare clic su Crea chiave API e copiare la chiave.

## Annotazioni sulle versioni

Ora, quando si usa il modello di versione per distribuire una nuova versione, verrà inviata un'annotazione ad Application Insights. Le annotazioni verranno visualizzate nei grafici di Esplora metriche.

Fare clic su un marcatore di annotazione per visualizzare i dettagli sulla versione, tra cui richiedente, ramo di controllo del codice sorgente, definizione della versione, ambiente e così via.


![Fare clic su un marcatore di annotazione della versione.](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0615_2016-->