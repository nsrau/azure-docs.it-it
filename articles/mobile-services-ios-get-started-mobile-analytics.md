<properties urlDisplayName="Get Started with Mobile Analytics" pageTitle="Introduzione a Mobile Analytics | Dev Center di Servizi mobili" metaKeywords="" description="Introduzione a Mobile Analytics." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Introduzione a Mobile Analytics" authors="mahender" manager="kirillg"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/10/2014" ms.author="mahender" />

# Introduzione a Mobile Analytics (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

In questa esercitazione verranno aggiunte funzionalità di analisi per dispositivi mobili all'app usando [Capptain][Capptain]. Mobile Analytics consente di determinare in che modo gli utenti interagiscono con la propria app e quali sezioni determinano la maggior parte delle attività. Per iniziare a ottenere questi dati, verrà instrumentata l'app con il Capptain SDK.

> [AZURE.NOTE] Capptain.com, di proprietà di Microsoft, offre soluzioni di analisi per app mobili gratuitamente fino a 100.000 utenti attivi al mese per i clienti del Livello standard di Servizi mobili di Azure. Per sfruttare questa offerta, contattare mobileservices@microsoft.com per ricevere ulteriori istruzioni. Nell'esercitazione seguente sono riepilogate le funzionalità di Capptain.com e fornite istruzioni per il loro utilizzo.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Inizializzare Capptain SDK][Inizializzare Capptain SDK]
2.  [Overload di UIViewController][Overload di UIViewController]

Per completare questa esercitazione, è necessario disporre di:

-   Un account di [Capptain][Capptain]
-   Un'app del [Livello standard di Servizi mobili][Livello standard di Servizi mobili]

## <a name="initialize"></a>Inizializzare Capptain SDK

1.  Passare alla pagina **Application Details** dell'app registrata in Capptain. Selezionare la scheda SDK e scaricare il pacchetto.

2.  In XCode, aggiungere il Capptain SDK al proprio progetto facendo clic con il pulsante destro del mouse sul progetto e selezionando "Add files to...". Scegliere la cartella CapptainSDK.

3.  Selezionare il progetto. Nella scheda **Build Phases** selezionare **Link Binary With Libraries** e aggiungere i seguenti framework:

    -   AdSupport.framework: impostare il collegamento come facoltativo
    -   SystemConfiguration.framework
    -   CoreTelephony.framework
    -   CFNetwork.framework
    -   CoreLocation.framework
    -   libxml2.dylib

    > [AZURE.NOTE] È possibile rimuovere il framework AdSupport. Capptain necessita di questo framework per raccogliere l'identificatore IDFA (Identifier for Advertising). È tuttavia possibile disabilitare la raccolta di identificatori IDFA per conformarsi ai criteri Apple riguardo questo ID.

4.  Nel file di implementazione Application Delegate, importare Capptain Agent.

        #import "CapptainAgent.h"

5.  All'interno di `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:`, inizializzare Capptain Agent passando `registerapp:identifiedby:` con l'ID app e la chiave dell'SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Overload di UIViewController

1.  Trovare ogni figlio di `UIViewController` nel proprio progetto e assicurarsi che ciascuno di essi erediti invece da `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2.  Trovare ogni figlio di `UITableViewController` nel proprio progetto e assicurarsi che ciascuno di essi erediti invece da `CapptainTableViewController`.

    L'app è ora configurata per inviare dati analitici a Capptain.

## Passaggi successivi

Per altre informazioni su cosa può fare Capptain per la propria app, visitare <http://www.capptain.com>

<!-- Anchors. --> <!-- URLs. -->

  [Capptain]: http://www.capptain.com
  [Inizializzare Capptain SDK]: #initialize
  [Overload di UIViewController]: #instrument
  [Livello standard di Servizi mobili]: /it-it/pricing/details/mobile-services/
