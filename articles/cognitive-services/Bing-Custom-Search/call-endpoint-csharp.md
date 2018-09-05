---
title: Chiamare l'endpoint tramite C# - Ricerca personalizzata Bing - Servizi cognitivi Microsoft
description: Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando C# per chiamare l'endpoint di Ricerca personalizzata Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 32644fe0cf0a6e1666d2d1ee6efb826bf753f001
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814864"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Chiamare l'endpoint di Ricerca personalizzata Bing (C#)

Questa guida introduttiva mostra come richiedere risultati delle ricerche dall'istanza di ricerca personalizzata usando C# per chiamare l'endpoint di Ricerca personalizzata Bing. 

## <a name="prerequisites"></a>Prerequisiti

-  Un'istanza di Ricerca personalizzata pronta per l'uso. Vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
-  [.Net Core](https://www.microsoft.com/net/download/core) installato.
- Un [account dell'API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **API di ricerca Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.  

  >[!NOTE]  
  >I clienti di Ricerca personalizzata Bing esistenti che hanno effettuato il provisioning di una chiave di anteprima entro il 15 ottobre 2017 potranno continuare a usare le chiavi fino al 30 novembre 2017 o fino al raggiungimento del numero massimo di query consentite. In seguito dovranno eseguire la migrazione alla versione disponibile a livello generale in Azure. 
 
## <a name="run-the-code"></a>Eseguire il codice

Per eseguire l'esempio, seguire questa procedura:

1. Creare una cartella per il codice.
2. Da un terminale o un prompt dei comandi passare alla cartella appena creata.
3. Eseguire i comandi seguenti:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Copiare il codice seguente in Program.cs.
5. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave e l'ID configurazione.

    ``` CSharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Compilare l'applicazione usando il comando seguente. Prendere nota del percorso della DLL a cui viene fatto riferimento nell'output del comando.

    <pre>
    dotnet build 
    </pre>
    
7. Eseguire l'applicazione usando il comando seguente e sostituendo **PATH TO OUTPUT** con il percorso a cui viene fatto riferimento nel passaggio di compilazione.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Passaggi successivi
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
