<properties
	pageTitle="Esempio di IoT di Azure MyDriving: Avvio rapido | Microsoft Azure"
	description="Introduzione a un'app che rappresenta una dimostrazione completa di come progettare un sistema IoT con Microsoft Azure che include l'analisi di flusso, Machine Learning e hub eventi."
	services=""
    documentationCenter=".net"
    suite=""
	authors="harikmenon"
	manager="douge"/>

<tags
	ms.service="multiple"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="harikm"/>

# Sistema IoT MyDriving: Avvio rapido

MyDriving è un sistema che mostra la progettazione e l'implementazione di una tipica soluzione [Internet delle cose](iot-suite-overview.md) (IoT) che raccoglie dati di telemetria dai dispositivi, li elabora nel cloud e applica Machine Learning per fornire una risposta adattiva. La dimostrazione registra i dati sui viaggi in automobile usando sia il telefono cellulare che un adattatore che raccoglie informazioni dal sistema di controllo dell'automobile. Questi dati vengono poi usati per fornire commenti e suggerimenti sullo stile di guida rispetto ad altri utenti.

Il vero scopo di MyDriving consiste nel creare una soluzione IoT, ma, prima di questo, occorre iniziare a usare l'app MyDriving come membro del team di utenti di test. Questo approccio consente di consente di provare l'app e il sistema sottostante in qualità di consumatore, prima di esaminarne l'architettura. Viene introdotto anche HockeyApp, che offre un modo interessante per gestire le distribuzioni alfa e beta delle app agli utenti di test.

## Usare l'esperienza mobile

È possibile usare l'app MyDriving se è disponibile un dispositivo Android, iOS o Windows 10.

### Installazione di Android e Windows 10 Mobile

Nel dispositivo:

1.  Consentire le app di sviluppo:

    -   Android: in **Impostazioni**, **Sicurezza**, consentire le app da **Origini sconosciute**.

    -   Windows 10: in **Impostazioni**, **Aggiornamenti**, **Per gli sviluppatori**, impostare **Modalità sviluppatore**.

2.  Unirsi al team di test beta mediante l'iscrizione o l'accesso a [HockeyApp](https://rink.hockeyapp.net). HockeyApp semplifica la distribuzione dei rilasci anticipati dell'app agli utenti di test.

    Se è installato Windows 10, usare il browser Edge.

    I partecipanti con la build 2016 possono accedere con lo stesso indirizzo di posta elettronica dell'account Microsoft registrato per la conferenza, usando uno dei pulsanti Microsoft. L'iscrizione a HockeyApp è già stata eseguita.

    ![Schermata di accesso di HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Scaricare e installare l'app qui:

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Sono disponibili due elementi. Installare il certificato in **Persone attendibili**, quindi installare l'app.

*In caso di problemi di avvio dell'app in Windows 10 Mobile* Il telefono potrebbe non essere aggiornato alla versione più recente. Verificare che siano installati gli ultimi aggiornamenti oppure installare:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx)

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx)

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### Installazione di iOS

I partecipanti con la build 2016 dovranno scaricare l'app come membro del team di test in HockeyApp.

1.  Nel dispositivo iOS accedere a [HockeyApp](https://rink.hockeyapp.net). Usare uno dei pulsanti di accesso Microsoft e accedere con lo stesso indirizzo di posta elettronica dell'account Microsoft registrato per la conferenza. Non usare i campi della posta elettronica e della password.

    ![Schermata di accesso di HockeyApp](./media/iot-solution-get-started/image1.png)

2.  Nel dashboard di HockeyApp selezionare MyDriving e scaricarla.

3.  Autorizzare la versione beta da HockeyApp:

    a. Passare a **Settings** > **General** > **Profiles and Device Management.**

    b. Considerare attendibile il certificato **Bit Stadium GmbH**.

Per chi non ha partecipato con la build 2016, è possibile compilare e distribuire l'applicazione manualmente:

1.   Scaricare il codice [da GitHub].

2.   Compilare e distribuire l'app [con Xamarin].

Altre informazioni sono disponibili nella [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs).

## Ottenere un adattatore OBD (facoltativo)

Questa è la parte che rende il sistema un vero sistema Internet delle cose (IoT). L'app può essere anche usata senza adattatore, ma non è altrettanto divertente. E, in più, gli adattatori non sono costosi.

La diagnostica a bordo (OBD) è la funzionalità dell'automobile che l'officina usa per risolvere e diagnosticare le cause dei rumori anomali e delle spie di avvertenza. A meno che l'automobile sia molto obsoleta, è possibile trovare una presa in qualche punto dell'abitacolo, in genere dietro una protezione sotto il cruscotto. Con il connettore giusto, è possibile ottenere le metriche delle prestazioni del motore e apportare alcune modifiche. Un connettore OBD può essere acquistato a prezzi modici dai normali rivenditori. Può essere connesso a un'app del telefono tramite Bluetooth o Wi-Fi.

Tuttavia, in questo caso, l'automobile verrà connessa al cloud. La connessione diretta dal dispositivo OBD avviene con il telefono, ma l'app viene usata per l'inoltro. I dati di telemetria dell'automobile vengono inviati direttamente all'hub IoT di MyDriving, dove vengono elaborati per registrare i percorsi dei viaggi e valutare lo stile di guida.

Per connettersi a un dispositivo di diagnostica a bordo (OBD)

1.  Verificare che il veicolo abbia una presa OBD.

2.  Ottenere un adattatore OBD:

    -   Se si usa un telefono Android o Windows, è necessario un adattatore OBD II abilitato per Bluetooth. In questo caso, è stato usato [BAFX Products 34t5 Bluetooth OBDII Scan Tool].

    -   Se si usa un telefono iOS, è necessario un adattatore OBD abilitato per Wi-Fi. In questo caso, è stato usato [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner].

3.  Seguire le istruzioni fornite con l'adattatore OBD per connetterlo al telefono. Tenere presente quanto segue:

    -   Un adattatore Bluetooth deve essere abbinato al telefono nella pagina delle **impostazioni**.

    -   Un adattatore Wi-Fi deve avere un indirizzo nell'intervallo 192.168.xxx.xxx.

4.  Se si hanno diverse automobili, è possibile usare un adattatore distinto per ogni macchina (per un massimo di 3).

Se non si ha un adattatore OBD, l'app invia comunque i dati su posizione e velocità dal ricevitore GPS del telefono al back-end e chiede se si vuole simulare un dispositivo OBD.

Per altre informazioni su come l'app usa i dati dall'adattatore OBD e sulle opzioni per creare il proprio dispositivo OBD, vedere la sezione 2.1 sui dispositivi IoT nella [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs).

## Usare l'app

Avviare l'app. È disponibile una Guida introduttiva iniziale che fornisce all'utente indicazioni dettagliate sul funzionamento.

### Tenere traccia dei viaggi.

Toccare il pulsante di registrazione, il grande cerchio rosso nella parte inferiore della schermata, per avviare un viaggio e toccarlo di nuovo per arrestarlo.

![Illustrazione del pulsante di registrazione di rilevamento dei viaggi](./media/iot-solution-get-started/image2.png)

Ogni volta che si avvia un viaggio, se non è presente un dispositivo OBD, viene chiesto se si vuole usare il simulatore.

Alla fine di un viaggio, fare clic sul pulsante di arresto per visualizzare un riepilogo:

![Esempio di riepilogo di un viaggio](./media/iot-solution-get-started/image3.png)

### Esaminare i viaggi

![Esempio di una viaggio precedente](./media/iot-solution-get-started/image4.png)

### Esaminare il profilo

![Esempio di un profilo dello stile di guida](./media/iot-solution-get-started/image5.png)

## Inviateci i commenti e suggerimenti sul test

MyDriving è stato creato per aiutare gli utenti a usare rapidamente i propri sistemi IoT. Per questo motivo sarà molto apprezzato qualsiasi commento sul funzionamento. Indicare se:

- Si sono riscontrate difficoltà o problematiche.

- Esiste un punto di estensione che potrebbe renderlo più adatto al proprio scenario.

- Si è trovato un modo più efficace per soddisfare determinate esigenze.

- Si hanno altri suggerimenti per migliorare MyDriving o questa documentazione.

All'interno dell'app MyDriving è possibile usare il meccanismo per commenti e suggerimenti predefinito di HockeyApp. In iOS e Android è sufficiente scuotere il telefono oppure usare il comando del menu **Feedback**. In questo modo viene applicato automaticamente uno screenshot in modo che il destinatario sappia di cosa si sta parlando. In caso di arresti anomali del sistema, HockeyApp raccoglie i relativi log per consentire l'inoltro delle informazioni pertinenti. I commenti possono essere inviati anche dal [portale di HockeyApp].

È anche possibile registrare un [problema in GitHub] o lasciare un commento qui di seguito (versione it-IT).

Tutti i commenti e suggerimenti saranno apprezzati.

## Passaggi successivi

-   Esplorare la [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs) per capire come è stato progettato e compilato l'intero sistema MyDriving.

-   [Creare e distribuire un sistema personalizzato](iot-solution-build-system.md) con gli script di Azure Resource Manager. La [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs) fornisce anche istruzioni relative alle aree con maggiori possibilità di personalizzazione.

  [da GitHub]: https://github.com/Azure-Samples/MyDriving
  [con Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portale di HockeyApp]: https://rink.hockeyapp.org
  [problema in GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0713_2016-->