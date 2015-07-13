<properties 
	pageTitle="Introduzione a Mobile Analytics | Dev Center di Servizi mobili" 
	description="Introduzione a Mobile Analytics." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# Introduzione a Mobile Analytics (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

In questa esercitazione verranno aggiunte funzionalità di analisi per dispositivi mobili all'app usando [Capptain]. Mobile Analytics consente di determinare in che modo gli utenti interagiscono con la propria app e quali sezioni determinano la maggior parte delle attività. Per iniziare a ottenere questi dati, verrà instrumentata l'app con il Capptain SDK.


>[AZURE.NOTE]Capptain.com, di proprietà di Microsoft, offre soluzioni di analisi per app mobili gratuitamente fino a 100.000 utenti attivi al mese per i clienti del Livello standard di Servizi mobili di Azure. Per sfruttare questa offerta, contattare mobileservices@microsoft.com per ricevere ulteriori istruzioni. L'esercitazione seguente riepiloga e fornisce istruzioni per l'uso delle funzionalità di Capptain.com.


Questa esercitazione descrive le operazioni di base seguenti:

1. [Inizializzare Capptain SDK]
2. [Eseguire l'overload di UIViewController]

Per completare questa esercitazione, è necessario disporre di:

* Un account di [Capptain]
* Un'app del [Livello standard di Servizi mobili]

## <a name="initialize"></a>Inizializzare Capptain SDK

1. Passare alla pagina **Application Details** dell'app registrata in Capptain. Selezionare la scheda SDK e scaricare il pacchetto.

2. In Xcode aggiungere Capptain SDK al proprio progetto facendo clic con il pulsante destro del mouse sul progetto e selezionando "Add files to". Scegliere la cartella CapptainSDK.

3. Selezionare il progetto. Nella scheda **Build Phases** selezionare **Link Binary With Libraries** e aggiungere i framework seguenti:
    * AdSupport.framework: impostare il collegamento come facoltativo
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]È possibile rimuovere il framework AdSupport. Capptain necessita di questo framework per raccogliere l'identificatore IDFA (Identifier for Advertising). È tuttavia possibile disabilitare la raccolta di identificatori IDFA per conformarsi ai criteri Apple riguardo questo ID.

4. Nel file di implementazione Application Delegate, importare Capptain Agent.


        #import "CapptainAgent.h"


5. In `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:` inizializzare Capptain Agent passando `registerapp:identifiedby:` con l'ID app e la chiave dell'SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Eseguire l'overload di UIViewController

1. Trovare ogni figlio di `UIViewController` nel proprio progetto e assicurarsi che ciascuno di essi erediti invece da `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Trovare ogni figlio di `UITableViewController` nel proprio progetto e assicurarsi che ciascuno di essi erediti invece da `CapptainTableViewController`.

    L'app è ora configurata per inviare dati analitici a Capptain.

## Passaggi successivi
Per altre informazioni sui vantaggi offerti da Capptain per la propria app, visitare [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Inizializzare Capptain SDK]: #initialize
[Eseguire l'overload di UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Livello standard di Servizi mobili]: /pricing/details/mobile-services/
 

<!---HONumber=July15_HO1-->