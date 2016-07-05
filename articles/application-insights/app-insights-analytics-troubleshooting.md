<properties 
	pageTitle="Risolvere i problemi di Analytics - Il potente strumento di ricerca di Application Insights | Microsoft Azure" 
	description="Problemi con Application Insights Analytics? Inizia da qui." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2016" 
	ms.author="awills"/>


# Risoluzione dei problemi di Analytics in Application Insights


Problemi con [Application Insights Analytics](app-insights-analytics.md)? Inizia da qui. Analytics è il potente strumento di ricerca incluso in Visual Studio Application Insights.



## Limiti

* Attualmente sono disponibili i risultati delle query per una settimana di dati.
* Browser su cui eseguiamo i test: versioni più recenti di Chrome, Edge e Internet Explorer.

## "Errore imprevisto"

![Schermata di errore imprevisto](./media/app-insights-analytics-troubleshooting/010.png)

Si è verificato un errore interno durante il runtime del portale: eccezione non gestita.

* Svuotare la cache del browser. 

## 403 ... please try to reload

![403 \... please try to reload](./media/app-insights-analytics-troubleshooting/020.png)

Si è verificato un errore correlato all'autenticazione (durante l'autenticazione o durante la generazione del token di accesso). Potrebbe non essere possibile ripristinare il portale senza modificare le impostazioni del browser.

* Verificare che nel browser siano attivati i cookie di terze parti. 

    Vedere [come disabilitare il cookie di terze parti](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), tenendo conto che in questo caso è necessario **abilitarli**.

## 403 ... verify security zone

![403 \... verify security zone](./media/app-insights-analytics-troubleshooting/030.png)

Si è verificato un errore correlato all'autenticazione (durante l'autenticazione o durante la generazione del token di accesso). Potrebbe non essere possibile ripristinare il portale senza modificare le impostazioni del browser.

1. Verificare che nel browser siano attivati i cookie di terze parti. 

    Vedere [come disabilitare i cookie di terze parti](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), tenendo conto che in questo caso è necessario **abilitarli**.

2. Il portale Analytics è stato aperto utilizzando preferiti, segnalibri o un collegamento salvato? L'accesso è stato eseguito con credenziali diverse da quelle utilizzate quando è stato salvato il collegamento?

 2. Provare a utilizzare una finestra del browser privata/in incognito (dopo aver chiuso tutte le finestre di questo tipo). Sarà necessario fornire le credenziali.

 2. Aprire un'altra finestra del browser (normale) e accedere ad [Azure](https://portal.azure.com). Uscire, quindi aprire il collegamento ed effettuare l'accesso con le credenziali corrette.

2. Gli utenti di Edge e Internet Explorer possono ricevere questo errore anche quando le impostazioni delle zone attendibili non sono supportate.

	Verificare che sia il [portale Analytics](https://analytics.applicationinsights.io) sia il [portale di Azure Active Directory](https://portal.azure.com) si trovino nella stessa area di sicurezza:

 * In Internet Explorer aprire **Opzioni Internet**, **Sicurezza**, **Siti attendibili**, **Siti**:

    ![Finestra di dialogo Opzioni Internet, aggiungere un sito all'elenco dei siti attendibili](./media/app-insights-analytics-troubleshooting/033.png)

    Se nell'elenco dei siti Web compaiono i seguenti URL, controllare che siano inclusi anche gli altri:

    https://analytics.applicationinsights.io<br/> https://login.microsoftonline.com<br/> https://login.windows.net


## 404 ... Resource not found

![404 \... resource not found](./media/app-insights-analytics-troubleshooting/040.png)

Una risorsa dell'applicazione è stata eliminata da Application Insights e non è più disponibile. Questa situazione può verificarsi se è stato salvato l'URL alla pagina di Analytics.


## 403 ... No authorization

![403 \... not authorized](./media/app-insights-analytics-troubleshooting/050.png)

Non si dispone dell'autorizzazione ad aprire questa applicazione in Analytics.

* Se il collegamento è stato fornito da un altro utente, chiedergli conferma che l'utente destinatario sia un [lettore o collaboratore di questo gruppo di risorse](app-insights-resources-roles-access-control.md).
* Se il collegamento è stato salvato con credenziali diverse, aprire il [portale di Azure](https://portal.azure.com), disconnettersi e quindi riprovare a usare questo collegamento, fornendo le credenziali corrette.

## 403 ... HTML5 Storage

Il portale utilizza HTML5 localStorage e sessionStorage.

* Chrome: Impostazioni, Privacy, Impostazioni contenuti.
* Internet Explorer: Opzioni Internet, scheda Avanzate, Sicurezza, Abilita archiviazione DOM


![403 \... try to enable HTML5 storage](./media/app-insights-analytics-troubleshooting/060.png)

## 404 ... Subscription not found


![404 \... Subscription not found](./media/app-insights-analytics-troubleshooting/070.png)

L'URL non è valido.

* Aprire la risorsa dell'app nel [portale di Application Insights](https://portal.azure.com), quindi utilizzare il pulsante di analisi.

## 404 ... page doesn't exist

![404 \... Page does not exist](./media/app-insights-analytics-troubleshooting/080.png)

L'URL non è valido.

* Aprire la risorsa dell'app nel [portale di Application Insights](https://portal.azure.com), quindi utilizzare il pulsante di analisi.

## Se non si riesce a risolvere il problema,    

aprire [un ticket di supporto](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0622_2016-->