<properties
	pageTitle="Integrazione di Azure Mobile Engagement SDK per iOS"
	description="Ultimi aggiornamenti e procedure relativi a iOS SDK per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

#Come integrare Engagement in iOS

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Questa procedura descrive il modo più semplice per attivare le funzioni di analisi e monitoraggio di Engagement in un'applicazione per iOS.

> [AZURE.IMPORTANT]L'SDK di Engagement richiede iOS6 o versione successiva: la destinazione della distribuzione dell'applicazione deve essere almeno iOS 6.

I passaggi seguenti sono sufficienti per attivare la segnalazione dei log necessari per calcolare tutte le statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici. La segnalazione dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve essere eseguita manualmente mediante l'API di Engagement (vedere [Come usare l'API di Engagement in iOS](mobile-engagement-ios-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

##Incorporare l'SDK di Engagement nel progetto iOS

Scaricare l’SDK per iOS da [qui](http://aka.ms/qk2rnj).
Aggiungere l'SDK di Engagement nel progetto iOS: in Xcode fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **"Add files to ..."** e infine selezionare la cartella `EngagementSDK`.

Per il funzionamento di Engagement sono necessari framework aggiuntivi: nell'area di esplorazione dei progetti aprire il riquadro del progetto, quindi selezionare la destinazione corretta. Aprire quindi la scheda **"Build phases"** e aggiungere i framework seguenti dal menu **"Link Binary With Libraries"**:

 -   `AdSupport.framework` : impostare il collegamento come `Optional`
 -   `SystemConfiguration.framework`
 -   `CoreTelephony.framework`
 -   `CFNetwork.framework`
 -   `CoreLocation.framework`
 -   `libxml2.dylib`

> [AZURE.NOTE] È possibile rimuovere il framework AdSupport. Engagement necessita di questo framework per raccogliere l'identificatore IDFA (Identifier for Advertising). È tuttavia possibile disabilitare la raccolta di identificatori IDFA \<ios-sdk-engagement-idfa\> per conformarsi ai nuovi criteri Apple relativi a questo ID.


##Inizializzare l'SDK di Engagement

È necessario modificare il delegato dell'applicazione:

-   Nella parte superiore del file di implementazione importare l'agente di Engagement:

		[...]
		#import "EngagementAgent.h"

-   Inizializzare Engagement all'interno del metodo '**applicationDidFinishLaunching:**' o '**application:didFinishLaunchingWithOptions:**':

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		  [...]
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
		  [...]
		}

##Segnalazione di base

### Metodo consigliato: eseguire l'overload delle classi `UIViewController`

Per attivare la segnalazione di tutti i log richiesti da Engagement per il calcolo delle statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici, è sufficiente fare in modo che tutte le sottoclassi `UIViewController` ereditino dalle classi `EngagementViewController` (stessa regola per `UITableViewController` -> `EngagementTableViewController`).

**Senza Engagement:**

	#import <UIKit/UIKit.h>

	@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Con Engagement:**

	#import <UIKit/UIKit.h>
	#import "EngagementViewController.h"

	@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Metodo alternativo: chiamare `startActivity()` manualmente

Se non si può o non si vuole eseguire l'overload delle classi `UIViewController`, è possibile avviare le attività chiamando direttamente i metodi di `EngagementAgent`.

> [AZURE.IMPORTANT]iOS SDK chiama automaticamente il metodo `endActivity()` quando viene chiusa l'applicazione. Di conseguenza, è *ALTAMENTE* consigliabile chiamare il metodo `startActivity` ogni volta che l'attività dell'utente cambia e non chiamare *MAI* il metodo `endActivity` poiché questo metodo forza la chiusura della sessione corrente.

##Segnalazione della posizione

Le condizioni del servizio Apple non permettono alle applicazioni di usare la verifica della posizione per scopi puramente statistici. È quindi consigliabile abilitare la segnalazione della posizione solo se l'applicazione usa la verifica della posizione anche per altri scopi.

A partire da iOS 8, è necessario fornire una descrizione dell'uso dei servizi di posizione da parte dell'app, impostando una stringa per la chiave [NSLocationWhenInUseUsageDescription] o [NSLocationAlwaysUsageDescription] nel file Info.plist dell'app. Se si vuole segnalare la posizione in background con Engagement, aggiungere la chiave NSLocationAlwaysUsageDescription. In tutti gli altri casi, aggiungere la chiave NSLocationWhenInUseUsageDescription.

### Segnalazione differita della posizione

La segnalazione differita della posizione consente di segnalare il paese, l'area geografica e la località associati ai dispositivi. Questo tipo di segnalazione della posizione usa solo le posizioni di rete, sulla base dell'ID di cella o della connessione Wi-Fi. L'area del dispositivo viene segnalata al massimo una volta per sessione. Il GPS non viene mai usato, per cui l'impatto di questo tipo di segnalazione della posizione sulla batteria è minimo, se non addirittura nullo.

Le aree segnalate vengono usate per calcolare statistiche geografiche relative a utenti, sessioni, eventi ed errori. Possono essere usate anche come criteri nelle campagne Reach. L'ultima area conosciuta segnalata per un dispositivo può essere recuperata grazie all'[API del dispositivo].

Per abilitare la segnalazione differita della posizione, aggiungere la riga seguente dopo l'inizializzazione dell'agente di Engagement:

	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	{
	  [...]
	  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
	  [...]
	}

### Segnalazione della posizione in tempo reale

La segnalazione della posizione in tempo reale consente di segnalare la latitudine e la longitudine associate ai dispositivi. Per impostazione predefinita, la segnalazione differita della posizione usa solo posizioni di rete (in base all'ID di cella o alla connessione Wi-Fi) ed è attiva solo quando l'applicazione viene eseguita in primo piano, ad esempio durante una sessione.

Le posizioni in tempo reale *NON* sono usate per calcolare dati statistici. Il loro unico scopo è consentire l'uso del criterio di definizione del recinto virtuale in tempo reale <Reach-Audience-geofencing> nelle campagne Reach.

Per abilitare la segnalazione della posizione in tempo reale, aggiungere la riga seguente dopo l'inizializzazione dell'agente di Engagement:

	[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### Segnalazione basata su GPS

Per impostazione predefinita, la segnalazione della posizione in tempo reale usa solo posizioni di rete. Per abilitare l'uso di posizioni basate su GPS (che sono molto più precise), aggiungere:

	[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Segnalazione in background

Per impostazione predefinita, la segnalazione della posizione in tempo reale è attiva solo quando l'applicazione viene eseguita in primo piano, ad esempio durante una sessione. Per abilitare la segnalazione anche in background, aggiungere:

	[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE]Quando l'applicazione viene eseguita in background, vengono segnalate solo le posizioni basate sulla rete, anche se è abilitato il GPS.

Se si implementa questa funzione, viene chiamato [startMonitoringSignificantLocationChanges] quando l'applicazione passa in background. Si noti che l'applicazione verrà riavviata automaticamente in background in caso di arrivo di un nuovo evento relativo alla posizione.

##Segnalazione avanzata

Facoltativamente, per segnalare eventi, errori e processi specifici dell'applicazione, è necessario usare l'API di Engagement mediante i metodi della classe `EngagementAgent`. Un oggetto di questa classe può essere recuperato chiamando il metodo statico `[EngagementAgent shared]`.

L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement ed è descritta in dettaglio nell'argomento dedicato all'uso dell'API in iOS, oltre che nella documentazione tecnica relativa alla classe `EngagementAgent`.

##Disabilitare la raccolta IDFA

Per impostazione predefinita, Engagement usa l'identificatore [IDFA] per identificare un utente in modo univoco. Se però si usano annunci pubblicitari altrove nell'app, è possibile che l'app venga respinta dal processo di verifica di App Store. La raccolta IDFA può essere disabilitata mediante l'aggiunta della macro del preprocessore `ENGAGEMENT_DISABLE_IDFA` nel file con estensione pch (o nella sezione `Build Settings` dell'applicazione). In questo modo è possibile assicurarsi che la build dell'applicazione non includa riferimenti a `ASIdentifierManager`, `advertisingIdentifier` o `isAdvertisingTrackingEnabled`.

Integrazione nel file **prefix.pch**:

	#define ENGAGEMENT_DISABLE_IDFA
	...

È possibile verificare la corretta disabilitazione della raccolta IDFA nell'applicazione controllando i log di test di Engagement. Per altre informazioni, vedere la documentazione sul test di integrazione <ios-sdk-engagement-test-idfa>.

##Disabilitare la segnalazione di log

### Uso di una chiamata del metodo

Se si vuole che Engagement non invii più log, è possibile chiamare:

	[[EngagementAgent shared] setEnabled:NO];

Questa chiamata è persistente: usa `NSUserDefaults` per archiviare le informazioni.

È possibile abilitare di nuovo la segnalazione di log chiamando la stessa funzione con `YES`.

### Integrazione nel bundle di impostazioni

Anziché chiamare questa funzione, è possibile integrare questa impostazione direttamente nel file `Settings.bundle` esistente. La stringa `engagement_agent_enabled` deve essere usata come identificatore di preferenze e deve essere associata a un'opzione di attivazione/disattivazione (`PSToggleSwitchSpecifier`).

L'esempio seguente di `Settings.bundle` mostra come implementarla:

	<dict>
	    <key>PreferenceSpecifiers</key>
	    <array>
	        <dict>
	            <key>DefaultValue</key>
	            <true/>
	            <key>Key</key>
	            <string>engagement_agent_enabled</string>
	            <key>Title</key>
	            <string>Log reporting enabled</string>
	            <key>Type</key>
	            <string>PSToggleSwitchSpecifier</string>
	        </dict>
	    </array>
	    <key>StringsTable</key>
	    <string>Root</string>
	</dict>

<!-- URLs. -->
[API del dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]: http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]: https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!----HONumber=AcomDC_1217_2015-->
