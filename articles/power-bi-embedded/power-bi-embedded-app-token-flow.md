<properties
   pageTitle="Informazioni sul flusso dei token delle app in Power BI Embedded"
   description="Power BI Embedded -Informazioni sul flusso dei token delle app per autenticazione e autorizzazione"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Informazioni sul flusso dei token delle app in Power BI Embedded

Il servizio **Power BI Embedded** usa **token delle app** per l'autenticazione e l'autorizzazione anziché usare l'autenticazione esplicita dell'utente finale. Nel modello con **token delle app** è l'applicazione a gestire l'autenticazione e autorizzazione degli utenti finali. Se necessario, l'app crea e invia i **token delle app** al nostro servizio per richiedere il rendering del report necessario. Questo modello non richiede all'app di usare **Azure Active Directory** per l'autenticazione e l'autorizzazione dell'utente, sebbene sia possibile.

**Di seguito viene spiegato il funzionamento del flusso delle chiavi dei token delle app**

1. È necessario copiare le chiavi API nell'applicazione. È possibile ottenere le chiavi nel **portale di Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Il token esegue l'asserzione di un'attestazione e ha una scadenza.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Con le chiavi di accesso API si accede al token.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. L'utente richiede di visualizzare un report.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	Il token viene convalidato con chiavi di accesso API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded invia un report all'utente.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Dopo che **Power BI Embedded** invia un report all'utente, l'utente può visualizzare il report nella propria app. Ad esempio, se è stato importato l'esempio [Analyzing Sales Data PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l'app Web di esempio sarà simile alla seguente:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## Vedere anche
- [Introduzione all'esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)
- [Cos'è Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Scenari comuni della versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Introduzione alla versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0420_2016-->