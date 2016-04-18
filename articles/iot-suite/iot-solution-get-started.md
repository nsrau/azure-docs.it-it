<properties 
	pageTitle="Esempio di IoT di Azure MyDriving - Avvio rapido | Microsoft Azure" 
	description="Introduzione a un'app che rappresenta una dimostrazione completa di come progettare un sistema IoT con Microsoft Azure che include l'analisi di flusso, Machine Learning e gli hub eventi." 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>

# Sistema IoT MyDriving: Avvio rapido

MyDriving è un sistema che mostra la progettazione e l'implementazione di una tipica soluzione [Internet delle cose](iot-suite-overview.md) (IoT) che raccoglie dati di telemetria dai dispositivi, li elabora nel cloud e applica Machine Learning per fornire una risposta adattiva. La dimostrazione registra i dati sui viaggi in automobile usando sia il telefono cellulare che un adattatore di diagnostica di bordo (OBD) che raccoglie informazioni dal sistema di controllo dell'automobile. Questi dati vengono poi usati per fornire commenti e suggerimenti sullo stile di guida rispetto ad altri utenti.


![](./media/iot-solution-get-started/image5.png)

Il vero scopo di MyDriving consiste nel creare una soluzione IoT, ma, prima di questo, occorre iniziare a usare l'app MyDriving come un membro del team di utenti di test. Questa prospettiva consente di approcciare l'app e il sistema sottostante in qualità di consumatore, prima di esaminarne l'architettura. Viene introdotto anche HockeyApp, che offre un modo interessante per gestire le distribuzioni alfa e beta delle app agli utenti di test.

## Usare l'esperienza mobile

**Prerequisiti**:

Dispositivo Android, iOS o Windows 10.

## Installazione di Android e Windows Phone 10

Nel dispositivo:

1.  **Consentire le app di sviluppo**

    -   Android: in **Impostazioni**, **Sicurezza**, consentire le app da **Origini sconosciute**.

    -   Windows 10: in **Impostazioni**, **Aggiornamenti**, **Per sviluppatori**, impostare **Modalità di sviluppo**.

2.  **Unirsi al team di test beta**.

    HockeyApp semplifica la distribuzione dei rilasci anticipati dell'app agli utenti di test.

    Nel dispositivo mobile:

    -   **Iscriversi/accedere a** HockeyApp all'indirizzo <https://rink.hockeyapp.net>.
    
        Se è installato Windows 10, usare il browser Edge.

        *Partecipanti con la build 2016*: accedere con lo stesso indirizzo di posta elettronica dell'account Microsoft (MSA) registrato per la conferenza, usando uno dei pulsanti Microsoft. L'iscrizione a HockeyApp è già stata eseguita.

        ![](./media/iot-solution-get-started/image1.png)

3.  **Scaricare e installare** l'app dai siti Web seguenti:

    -   Android: <http://rink.io/spMyDrivingAndroid>

    -   Windows 10 Phone: <http://rink.io/spMyDrivingUWP>

        Sono disponibili due elementi. Installare il certificato in Persone attendibili, quindi installare l'app.
    
*Problemi di avvio in Windows 10 Phone* Il telefono potrebbe non essere aggiornato. Verificare che siano installati gli ultimi aggiornamenti oppure installare:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx) 
   

## Installazione di iOS

### Partecipanti con la build 2016

Se è installata la build 2016, scaricare l'app come membro del team di test in HockeyApp.

1.  Nel dispositivo iOS accedere a <https://rink.hockeyapp.net>. Usare uno dei pulsanti di accesso Microsoft e accedere con lo stesso indirizzo di posta elettronica dell'account Microsoft (MSA) registrato per la conferenza. Non usare i campi della posta elettronica e della password.

    ![](./media/iot-solution-get-started/image1.png)

2.  Nel dashboard di HockeyApp selezionare MyDriving e scaricarlo.

3.  Autorizzare la versione beta da HockeyApp:

    Andare in **Settings** &gt;**General** &gt;**Profiles and Device Management.**

    Considerare attendibile il certificato **Bit Stadium GmbH**.

### Se non si usa la build 2016

L'app verrà rilasciata a breve in iOS Store.

Per ora, è possibile compilare e distribuire autonomamente l'app:

-   Scaricare il codice [da GitHub].

-   Compilare e distribuire l'app [con Xamarin].

Altre informazioni sono disponibili nella [guida di riferimento a MyDriving](http://aka.ms/mydrivingdocs).

## Ottenere un adattatore OBD (facoltativo)

Questa è la parte che rende il sistema un vero sistema Internet delle cose (IoT). L'app può essere anche usata senza adattatore, ma non è altrettanto divertente. E, in più, gli adattatori non sono costosi.

La diagnostica di bordo (OBD) è la funzionalità dell'automobile che l'officina usa per risolvere e diagnosticare le cause dei rumori anomali e delle spie di avvertenza. A meno che l'auto non sia d'epoca, sotto il cruscotto dovrebbe esserci una presa. Con il connettore giusto, è possibile ottenere le metriche delle prestazioni del motore e apportare alcune modifiche. Un connettore OBD può essere acquistato a prezzi modici dai normali rivenditori. Può essere connesso a un'app del telefono tramite Bluetooth o WiFi.

Tuttavia, in questo caso, l'automobile verrà connessa al cloud. Certo, la connessione diretta dal dispositivo OBD è con il telefono, ma l'app viene usata per l'inoltro. I dati di telemetria dell'automobile vengono inviati direttamente all'hub IoT di MyDriving, dove vengono elaborati per registrare i percorsi dei viaggi e valutare lo stile di guida.

### Connettere un dispositivo di diagnostica di bordo (OBD)


1.  Verificare che l'automobile abbia il connettore OBD, normalmente presente in tutti i modelli di automobili tranne quelli d'epoca. Il connettore dovrebbe trovarsi all'interno dell'abitacolo, generalmente dietro una linguetta sotto il cruscotto.

2.  Ottenere un adattatore OBD. Sono stati usati i tipi seguenti:

    Se si usa:

    -   un telefono **Android o Windows**, è necessario un adattatore **OBD II abilitato per Bluetooth**. In questo caso, è stato usato [BAFX Products 34t5 Bluetooth OBDII Scan Tool].

    -   un telefono **iOS** è necessario un adattatore OBD **abilitato per WiFi**. In questo caso, è stato usato [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner].

3.  Seguire le istruzioni fornite con l'adattatore OBD per connetterlo al telefono.

    -   Un adattatore Bluetooth deve essere abbinato al telefono nella pagina delle impostazioni.

    -   Un adattatore WiFi deve avere un indirizzo nell'intervallo 192.168.xxx.xxx.

4.  Se si hanno diverse automobili, è possibile usare un adattatore per ogni macchina (per un massimo di 3).

Se non si ha un adattatore OBD, l'app invia comunque i dati su posizione e velocità dal ricevitore GPS del telefono al back-end e chiede se si vuole simulare un dispositivo OBD.

Per altre informazioni su come l'app usa i dati dall'adattatore OBD e sulle opzioni per creare il proprio dispositivo OBD, vedere la sezione 2.1 sui dispositivi IoT nella [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs).

## Uso dell'app

**Avviare** l'app. È disponibile una Guida introduttiva iniziale che fornisce all'utente indicazioni dettagliate sul funzionamento.

-   **Tenere traccia dei viaggi.** Toccare il pulsante di registrazione (il grande cerchio rosso nella parte inferiore della schermata) per avviare il viaggio e toccarlo di nuovo per arrestarlo.


    ![](./media/iot-solution-get-started/image2.png)

-   Ogni volta che si avvia un viaggio, se non è presente un dispositivo OBD, viene chiesto se si vuole usare il simulatore.

-   Alla fine di un viaggio, fare clic sul pulsante di arresto per visualizzare un riepilogo:

    ![](./media/iot-solution-get-started/image3.png)

-   **Esaminare i viaggi:**

    ![](./media/iot-solution-get-started/image4.png)

-   **Esaminare il profilo:**

    ![](./media/iot-solution-get-started/image5.png)

-   **Inviare commenti e suggerimenti di test** con il pulsante nell'app oppure agitare il telefono. In questo modo viene applicato automaticamente uno screenshot in modo che il destinatario sappia di cosa si sta parlando. In caso di arresti anomali del sistema, HockeyApp raccoglie i relativi log per consentire l'inoltro delle informazioni pertinenti.

## Commenti e suggerimenti 

MyDriving è stato creato per aiutare gli utenti a usare rapidamente i propri sistemi IoT. Per questo motivo sarà molto apprezzato qualsiasi commento sul funzionamento. Inviare commenti in caso di problemi o difficoltà, se un punto di estensione renderebbe la soluzione più adatta a uno specifico scenario, se si trova un modo più efficace per soddisfare particolari esigenze o se si hanno altri suggerimenti per migliorare MyDriving o questa documentazione.

All'interno dell'app MyDriving è possibile usare il meccanismo per commenti e suggerimenti predefinito di HockeyApp: in iOS e Android è sufficiente agitare il telefono oppure usare il comando del menu Commenti e suggerimenti. I commenti possono essere inviati anche dal [portale di HockeyApp].

È anche possibile inviare un [problema in GitHub] o lasciare un commento qui sotto (versione it-IT).

Tutti i commenti e suggerimenti saranno apprezzati.

## Passaggi successivi

-   Esplorare la [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs) per capire come è stato progettato e compilato l'intero sistema di MyDriving.

-   [Creare e distribuire un sistema personalizzato](iot-solution-build-system.md) con gli script di Azure Resource Manager. La [guida di riferimento di MyDriving](http://aka.ms/mydrivingdocs) fornisce anche istruzioni relative alle aree con maggiori possibilità di personalizzazione.

  [da GitHub]: https://github.com/Azure-Samples/MyDriving
  [con Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [portale di HockeyApp]: https://rink.hockeyapp.org
  [problema in GitHub]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0406_2016-->