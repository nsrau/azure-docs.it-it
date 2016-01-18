<properties
    pageTitle="Annotazioni sulla distribuzione per Application Insights | Microsoft Azure"
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
	ms.date="01/05/2016"
    ms.author="awills"/>

# Annotazioni sulla versione in Application Insights

Le annotazioni sulla versione nei grafici di [Esplora metriche](app-insights-metrics-explorer.md) indicano dove è stata distribuita una nuova build e consentono di verificare facilmente se le modifiche hanno avuto effetto sulle prestazioni dell'applicazione. Possono essere create automaticamente dal [sistema di generazione di Visual Studio Team Services](https://www.visualstudio.com/it-IT/get-started/build/build-your-app-vs).

![Esempi di annotazioni con correlazione visibile con il tempo di risposta del server](./media/app-insights-annotations/00.png)

Le annotazioni sulla versione sono una funzionalità della build basata sul cloud e del servizio versione di Visual Studio Team Services.

## Installare l'estensione Annotazioni (una volta)

Per creare le annotazioni sulla versione, sarà necessario installare una delle numerose estensioni di Team Service disponibili in Visual Studio Marketplace.

1. Accedere al progetto in [Visual Studio Team Services](https://www.visualstudio.com/it-IT/get-started/setup/sign-up-for-visual-studio-online).
2. Aprire Visual Studio Marketplace, trovare l'estensione Annotazioni di Application Insights e aggiungerla all'account di Team Services.

![Nell'angolo in alto a destra della pagina Web di Team Services aprire il marketplace. Cercare e installare Annotazioni di Application Insights nell'account.](./media/app-insights-annotations/10.png)

È necessario eseguire questa operazione una sola volta per l'account di Visual Studio Team Services. Le annotazioni sulla versione ora possono essere configurate per qualsiasi progetto nell'account.



## Aggiungere un'attività di annotazione al modello di versione

Questa operazione è necessaria per ogni modello di versione per cui si vogliono creare annotazioni sulla versione.

Aprire (o creare) il modello di versione che gestisce le distribuzioni da Visual Studio Team Services.

Aggiungere un'attività e scegliere l'attività di annotazione sulla versione di Application Insights dal menu.

![Nell'angolo in alto a destra della pagina Web di Team Services aprire il marketplace. Cercare e installare Annotazioni di Application Insights nell'account.](./media/app-insights-annotations/40.png)

Per completare questo passaggio, saranno necessari alcuni dettagli della risorsa di Application Insights usata per monitorare l'applicazione.

Tenere aperta la finestra di Team Services mentre si recuperano i dettagli da Application Insights.

## Copiare una chiave API da Application Insights.

In una finestra del browser separata:

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com) e aprire la risorsa di Application Insights che monitora l'applicazione o [crearne una nuova](app-insights-overview.md), se necessario.
2. Aprire l'elenco a discesa **Informazioni di base** e copiare l'ID sottoscrizione, il gruppo di risorse e il nome della risorsa nell'attività di annotazione sulla versione. ![](./media/app-insights-annotations/50.png)
2. Aprire **Impostazioni**, **Chiavi API** e creare una nuova chiave. Copiare quanto indicato sotto. ![](./media/app-insights-annotations/30.png)

Infine fare clic su **Salva** per salvare la definizione della versione.

## Indicatori di distribuzione

Ora, quando si usa il modello di versione per distribuire una nuova versione, verrà inviata un'annotazione ad Application Insights. Le annotazioni verranno visualizzate nei grafici di Esplora metriche.

<!---HONumber=AcomDC_0107_2016-->