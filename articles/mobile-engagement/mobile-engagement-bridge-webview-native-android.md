---
title: Creare un bridge tra WebView di Android e Mobile Engagement SDK per Android nativo
description: Viene descritto come creare un bridge tra WebView che esegue JavaScript e Mobile Engagement SDK nativo per Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Creare un bridge tra WebView di Android e Mobile Engagement SDK per Android nativo
> [!div class="op_single_selector"]
> * [Bridge Android](mobile-engagement-bridge-webview-native-android.md)
> * [Bridge iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Alcune app per dispositivi mobili vengono progettate come app ibride dove l'app stessa è sviluppata con Android nativo, ma le schermate vengono restituite, totalmente o in parte, in WebView di Android. È comunque possibile usare Mobile Engagement SDK per Android all'interno di tali app e questa esercitazione illustra come farlo. Il codice di esempio riportato sotto si basa sulla documentazione Android disponibile [qui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Si descrive come usare questo approccio documentato per implementare gli stessi metodi comunemente usati per Mobile Engagement SDK per Android in modo che WebView possa avviare richieste da un'app ibrida per tenere traccia di eventi, processi, errori e informazioni app durante il piping via SDK per Android. 

1. Prima di tutto, è necessario verificare di aver terminato l' [esercitazione introduttiva](mobile-engagement-android-get-started.md) per integrare Mobile Engagement SDK per Android nell'app ibrida. Al termine dell'operazione, il metodo `OnCreate` avrà un aspetto simile al seguente.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Verificare che l'app ibrida visualizzi WebView sulla schermata. Il codice sarà simile al seguente, dove è in corso il caricamento del file HTML locale **Sample.html** in WebView nel metodo `onCreate` della schermata. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Creare un file bridge denominato **WebAppInterface** per generare un wrapper su alcuni metodi comunemente usati di Mobile Engagement SDK per Android tramite l'approccio `@JavascriptInterface` descritto nella [documentazione Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Dopo aver creato il file bridge di cui sopra, è necessario verificare che sia associato a WebView. A tale scopo, è necessario modificare il metodo `SetWebview` che deve somigliare al seguente:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. Nel frammento precedente, `addJavascriptInterface` è stato chiamato per associare la classe bridge a WebView ed è stato creato anche un handle denominato **EngagementJs** per chiamare i metodi dal file bridge. 
6. Creare nel progetto il seguente file denominato **Sample.html** all'interno di una cartella dal nome **assets**, che viene caricata in WebView e in cui verranno chiamati i metodi dal file bridge.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Notare i punti seguenti riguardanti il file HTML indicato sopra:
   
   * Contiene un set di caselle di input in cui è possibile fornire i dati da usare come nomi per Event, Job, Error e AppInfo. Quando si fa clic sul pulsante accanto, viene eseguita una chiamata a JavaScript che a sua volta chiama i metodi dal file bridge per passare la chiamata a Mobile Engagement SDK per Android. 
   * Vengono aggiunte alcune informazioni statiche extra agli eventi, ai processi e anche agli errori per mostrare come eseguire questa operazione. Queste informazioni aggiuntive vengono inviate come stringa JSON che, guardando il file `WebAppInterface`, viene analizzata e inserita in un `Bundle` Android, quindi passata con l'invio di Events, Jobs ed Errors. 
   * Un processo di Mobile Engagement viene avviato con il nome specificato nella casella di input, viene eseguito per 10 secondi, quindi arrestato. 
   * Un appinfo o tag Mobile Engagement viene passato con 'customer_name' come chiave statica e con il valore immesso nell'input come valore del tag. 
8. Eseguire l'app per visualizzare quanto segue. Assegnare un nome a un evento test simile a quello seguente, quindi fare clic su **Send** sotto. 
   
    ![][1]
9. È possibile visualizzare questo evento insieme alle informazioni app statiche inviate passando alla scheda **Monitoraggio** dell'app e guardando in **Eventi -> Dettagli**. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
