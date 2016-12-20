---
title: Creare un bridge tra WebView di iOS e Mobile Engagement SDK per iOS nativo
description: Viene descritto come creare un bridge tra WebView che esegue JavaScript e Mobile Engagement SDK per iOS nativo
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f68e0578afe31cdd5b1c27222d31b646f201fc32


---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Creare un bridge tra WebView di iOS e Mobile Engagement SDK per iOS nativo
> [!div class="op_single_selector"]
> * [Bridge Android](mobile-engagement-bridge-webview-native-android.md)
> * [Bridge iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Alcune app per dispositivi mobili vengono progettate come app ibride dove l'app stessa è sviluppata con Objective-C iOS nativo, ma le schermate vengono restituite, totalmente o in parte, in WebView di iOS. È comunque possibile usare Mobile Engagement SDK per iOS all'interno di tali app e questa esercitazione illustra come farlo. 

Esistono due approcci per ottenere questo risultato anche se non è disponibile una documentazione al riguardo:

* Il primo viene descritto in questo [collegamento](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) e comporta la registrazione di `UIWebViewDelegate` nella visualizzazione Web e il rilevamento con cancellazione immediata di una modifica del percorso eseguito in JavaScript. 
* Il secondo, che si basa su questa [sessione WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), rappresenta un approccio più lineare rispetto al primo ed è quello che viene adottato in questa guida. Si noti che questo approccio funziona solo con iOS7 e versioni successive. 

Seguire la procedura riportata sotto per un esempio di bridge iOS:

1. Prima di tutto, è necessario verificare di aver terminato l' [esercitazione introduttiva](mobile-engagement-ios-get-started.md) per integrare Mobile Engagement SDK per iOS nell'app ibrida. Si può anche scegliere di abilitare la registrazione test per poter vedere i metodi SDK quando vengono attivati da WebView. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Verificare che l'app ibrida visualizzi WebView sulla schermata. È possibile aggiungerla a `Main.storyboard` dell'applicazione. 
3. Associare questa WebView a **ViewController** facendo clic sulla WebView e trascinandola dalla finestra del controller alla schermata di modifica `ViewController.h` per posizionarla appena sotto la riga `@interface`. 
4. Dopo aver eseguito questa operazione viene visualizzata una finestra di dialogo con la richiesta di un nome. Assegnare il nome **webView**. Il file `ViewController.h` deve apparire come segue:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Il file `ViewController.m` sarà aggiornato in un secondo momento e prima viene creato il file bridge che a sua volta genera un wrapper su alcuni metodi comunemente usati di Mobile Engagement SDK per iOS. Creare un nuovo file di intestazione denominato **EngagementJsExports.h** che usa il meccanismo `JSExport` descritto nella [sessione](https://developer.apple.com/videos/play/wwdc2013/615) indicata sopra per esporre i metodi nativi iOS. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Successivamente, viene creata la seconda parte del file bridge. Creare un file denominato **EngagementJsExports.m** contenente l'implementazione di creazione dei wrapper effettivi chiamando i metodi Mobile Engagement SDK per iOS. Si noti anche che è in corso l'analisi di `extras` passati da JavaScript di WebView e il relativo inserimento in un oggetto `NSMutableDictionary` da passare con le chiamate ai metodi Engagement SDK.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Si torna quindi a **ViewController.m** e lo si aggiorna con il codice seguente: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Notare i punti seguenti sul file **ViewController.m** :
   
   * Nel metodo `loadWebView` si sta caricando un file HTML locale chiamato **LocalPage.html** il cui codice verrà esaminato successivamente. 
   * Dal metodo `webViewDidFinishLoad` viene catturato `JsContext` e lo si associa alla classe wrapper. Questo consente la chiamata ai metodi wrapper SDK tramite l'handle **EngagementJs** da WebView. 
9. Creare un file denominato **LocalPage.html** con il codice seguente:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Notare i punti seguenti riguardanti il file HTML indicato sopra:
    
    * Contiene un set di caselle di input in cui è possibile fornire i dati da usare come nomi per Event, Job, Error e AppInfo. Quando si fa clic sul pulsante accanto, viene eseguita una chiamata a JavaScript che a sua volta chiama i metodi dal file bridge per passare la chiamata a Mobile Engagement SDK per iOS. 
    * Vengono aggiunte alcune informazioni statiche extra agli eventi, ai processi e anche agli errori per mostrare come eseguire questa operazione. Queste informazioni aggiuntive vengono inviate come stringa JSON che, guardando il file `EngagementJsExports.m` , viene analizzata e passata con l'invio di Events, Jobs ed Errors. 
    * Un processo di Mobile Engagement viene avviato con il nome specificato nella casella di input, viene eseguito per 10 secondi, quindi arrestato. 
    * Un appinfo o tag Mobile Engagement viene passato con 'customer_name' come chiave statica e con il valore immesso nell'input come valore del tag. 
11. Eseguire l'app per visualizzare quanto segue. Assegnare un nome a un evento test simile a quello seguente, quindi fare clic sul pulsante **Send** accanto. 
    
     ![][1]
12. È possibile visualizzare questo evento insieme alle informazioni app statiche inviate passando alla scheda **Monitoraggio** dell'app e guardando in **Eventi -> Dettagli**. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png



<!--HONumber=Nov16_HO3-->


