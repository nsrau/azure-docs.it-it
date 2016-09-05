<properties 
	pageTitle="Azure Mobile Engagement - Integrazione del back-end" 
	description="Informazioni su come connettere Azure Mobile Engagement a un back-end di SharePoint per creare campagne da SharePoint." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />  

# Azure Mobile Engagement - Integrazione di API

In un sistema marketing automatizzato, vengono eseguite automaticamente anche la creazione e l'attivazione delle campagne di marketing. A questo scopo, Azure Mobile Engagement permette la creazione di tali campagne marketing automatizzate anche mediante le API.

In genere i clienti usano l'interfaccia del front-end di Mobile Engagement per creare annunci/sondaggi e così via come parte delle campagne di marketing. Nel corso della maturazione delle campagne di marketing, tuttavia, diventa necessario sfruttare i dati bloccati nei sistemi back-end (ad esempio qualsiasi sistema CRM o un sistema CMS come SharePoint), in modo che sia possibile creare una pipeline automatizzata completa, che crea a sua volta in modo dinamico campagne in Mobile Engagement in base ai dati provenienti dai sistemi back-end.

![][5]

Questa esercitazione illustra uno scenario di questo tipo, in cui un utente aziendale di SharePoint popola un elenco di SharePoint con dati di marketing e un processo automatizzato sceglie elementi dall'elenco e li connette al sistema Mobile Engagement usando le API REST disponibili per creare una campagna di marketing dai dati di SharePoint.
 
> [AZURE.IMPORTANT] In generale, è possibile usare questo esempio come punto di partenza per capire come chiamare qualsiasi API REST di Mobile Engagement, poiché illustra nel dettaglio i due aspetti chiave della chiamata delle API, ovvero l'autenticazione e il passaggio di parametri.

## Integrazione con SharePoint
1. L'elenco di SharePoint di esempio ha un aspetto analogo al seguente. I valori di **Title**, **Category**, **NotificationTitle**, **Message** e **URL** vengono usati per la creazione dell'annuncio. La colonna **IsProcessed** viene usata dal processo di automazione di esempio sotto forma di programma console. È possibile eseguire questo programma console come processo Web di Azure, in modo che sia possibile pianificarlo, oppure si può usare direttamente il flusso di lavoro di SharePoint per programmare la creazione e l'attivazione dell'annuncio quando un elemento viene inserito nell'elenco di SharePoint. In questo esempio viene usato il programma console, che esamina gli elementi dell'elenco di SharePoint, crea annunci in Azure Mobile Engagement per ogni elemento e infine contrassegna il flag **IsProcessed** come true in caso di creazione riuscita dell'annuncio.

	![][1]

2. Per l'autenticazione nell'elenco di SharePoint viene usato il codice dell'esempio di *Autenticazione remota in SharePoint Online mediante il modello a oggetti client*, disponibile [qui](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c).
 
3. Dopo l'autenticazione, gli elementi dell'elenco verranno esaminati in ciclo per individuare eventuali elementi appena creati, il cui valore **IsProcessed** sarà false.

 		static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## Integrazione con Mobile Engagement
1.  Quando viene rilevato un elemento che richiede l'elaborazione, vengono estratte le informazioni necessarie per creare un annuncio dall'elemento dell'elenco, viene chiamato `CreateAzMECampaign` per creare l'annuncio e infine `ActivateAzMECampaign` per attivarlo. Si tratta essenzialmente di chiamate all'API REST per il back-end di Mobile Engagement.

2.  Le API REST di Mobile Engagement necessitano di un'**intestazione HTTP dell'autorizzazione dello schema di autorizzazione di base**, costituita da `ApplicationId` e `ApiKey`, disponibili nel portale di Azure. Assicurarsi di usare la chiave della sezione **Chiavi API** e *non* della sezione **Chiavi SDK**.

	![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Per la creazione di una campagna di tipo annuncio, fare riferimento alla [documentazione](https://msdn.microsoft.com/library/azure/mt683750.aspx). È necessario assicurarsi di specificare l'elemento `kind` della campagna come *announcement* e il [payload](https://msdn.microsoft.com/library/azure/mt683751.aspx) e infine di passarlo come FormUrlEncodedContent.

		static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Al termine della creazione dell'annuncio, il portale di Mobile Engagement avrà un aspetto analogo al seguente. Si noti che Stato=Bozza e Attivato = N/D.

	![][3]

5. `CreateAzMECampaign` crea una campagna di tipo annuncio e restituisce il rispettivo ID al chiamante. `ActivateAzMECampaign` richiede questo ID come parametro per l'attivazione della campagna.

		static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Dopo l'attivazione dell'annuncio, il portale di Mobile Engagement avrà un aspetto analogo al seguente:

	![][4]

7. Non appena viene attivata la campagna, tutti i dispositivi che soddisfano i criteri della campagna inizieranno a visualizzare notifiche.

8. Si potrà anche notare che l'elemento dell'elenco contrassegnato come IsProcessed = false è stato impostato su True dopo la creazione della campagna.

Questo esempio ha creato una semplice campagna di tipo annuncio, specificando principalmente le proprietà necessarie. È possibile personalizzare come si desidera l'esempio nel portale, usando le informazioni disponibili [qui](https://msdn.microsoft.com/library/azure/mt683751.aspx).

<!-- Images. -->  
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 

<!---HONumber=AcomDC_0824_2016-->