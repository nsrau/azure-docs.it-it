<properties 
	pageTitle="Uso dell'API REST per accedere alle API del servizio Azure Mobile Engagement" 
	description="Descrive come usare le API REST di Mobile Engagement per accedere alle API del servizio Azure Mobile Engagement"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="wesmc;ricksal" />

#Uso di REST per accedere alle API del servizio Azure Mobile Engagement

Azure Mobile Engagement offre un set di [API REST per Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) che consentono di gestire i dispositivi, le campagne push e di copertura e così via. In questo esempio verranno usate direttamente le API REST per creare una campagna di annuncio, attivarla e quindi eseguirne il push a un gruppo di dispositivi.

Per chi non desidera usare le API REST direttamente è disponibile un [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) che è possibile usare con gli strumenti per la generazione degli SDK per la lingua preferita. Per generare l'SDK dal file Swagger si consiglia di usare lo strumento [AutoRest](https://github.com/Azure/AutoRest). In modo simile è stato creato un .NET SDK che consente di interagire con queste API tramite un wrapper C#. Non è necessario eseguire manualmente la negoziazione di token di autenticazione e l'aggiornamento. Se si desidera esaminare un esempio simile usando il wrapper, vedere [l'esempio .NET SDK delle API del servizio](mobile-engagement-dotnet-sdk-service-api.md)

In questo esempio verranno usate direttamente le API REST per creare e attivare una campagna di annuncio.

## Aggiunta di un'appInfo user\_name all'app Mobile Engagement

Per questo esempio è richiesta l'aggiunta di un tag di info app all'app Mobile Engagement. Nel portale di Engagement per l'app è possibile aggiungere il tag facendo clic su **Impostazioni** > **Tag (info app)** > **Nuovo tag (info app)**. Aggiungere il nuovo tag denominato **user\_name** con il tipo **stringa**.

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



Se sono state seguite le istruzioni di [Introduzione a Azure Mobile Engagement per app di Windows universali](mobile-engagement-windows-store-dotnet-get-started.md), è possibile testare l'invio del tag **user\_name** sostituendo semplicemente `OnNavigatedTo()` nella classe `MainPage` per inviare il tag info app simile al codice seguente:

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## Creazione dell'app per le API del servizio

1. Prima di tutto, è necessario procurarsi quattro parametri di autenticazione da usare con questo esempio. Questi parametri sono **SubscriptionId**, **TenantId**, **ApplicationId** e **Secret**. Per ottenere questi parametri di autenticazione è consigliabile usare la procedura con script di PowerShell indicata nella sezione *Installazione singola (mediante script)* dell'esercitazione [Autenticazione](mobile-engagement-api-authentication.md#authentication).

2. Per illustrare l'uso delle API REST del servizio per creare e attivare una nuova campagna di annuncio verrà usata una semplice app console Windows. Aprire Visual Studio e creare una nuova **applicazione console**.

3. Successivamente, aggiungere il pacchetto **Newtonsoft.Json** NuGet al progetto.

4. Nel file `Program.cs` aggiungere le seguenti istruzioni `using` per gli spazi dei nomi seguenti:

		using System.IO;
		using System.Net;
		using Newtonsoft.Json.Linq;
		using Newtonsoft.Json;

5. Quindi, definire le costanti seguenti nella classe `Program`. Queste verranno usate per l'autenticazione e l'interazione con l'app Mobile Engagement in cui si sta creando la campagna di annuncio:


        // Parameters needed for authentication of API calls.
		// These are returned from the PowerShell script in the authentication tutorial. 
		// https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

		// The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/it-IT/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
		// Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

		// New campaign id returned from creating the new campaign and used to activate and push the campaign
		// a set of devices.
        static String NewCampaignID = null;

		// This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. Aggiungere i due metodi seguenti alla classe `Program`. Questi verranno usati per eseguire le API REST e visualizzare le risposte nella console.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. Aggiungere il codice seguente per il metodo `Main` per generare un token di autenticazione con i parametri di autenticazione raccolti:

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. Ora che abbiamo un token di autenticazione valido è possibile creare una nuova campagna di copertura usando l'API REST [Creare campagna](https://msdn.microsoft.com/library/azure/mt683742.aspx). La nuova campagna sarà una semplice campagna di annuncio **AnyTime** e **Notification-only** con un titolo e un messaggio. Questa sarà una campagna di push manuale come illustrato nella schermata seguente. Ciò significa che il push avverrà solo tramite le API.


	![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

	Aggiungere il codice seguente al metodo `Main` per creare la campagna di annuncio:

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. Prima di poter essere distribuita ai dispositivi, la campagna deve essere attivata. L'ID per la nuova campagna è stato salvato nella variabile `NewCampaignID` e verrà usato come parametro di percorso URI per attivare la campagna tramite l'API REST [Attivare campagna](https://msdn.microsoft.com/library/azure/mt683745.aspx) . Lo stato della campagna dovrebbe quindi diventare **pianificato** anche se il push avverrà solo manualmente tramite le API.

	Aggiungere il codice seguente al metodo `Main` per attivare la campagna di annuncio:

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. Per eseguire il push della campagna è necessario fornire gli ID dispositivo degli utenti che si desidera ricevano la notifica. Per ottenere tutti gli ID dispositivo si userà l'API REST [Query dispositivi](https://msdn.microsoft.com/library/azure/mt683826.aspx). Si aggiungerà all'elenco ogni ID dispositivo con appInfo **user\_name** associata.

	Aggiungere il codice seguente al metodo `Main` per ottenere tutti gli ID dispositivo e popolare deviceList:

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
			return;
        }


11. Infine, si effettuerà il push della campagna a tutti gli ID dispositivo dell'elenco usando l'API REST [Campagna di push](https://msdn.microsoft.com/library/azure/mt683734.aspx). Poiché si tratta di una notifica **in-app**, affinché possa essere ricevuta dall'utente l'app dovrà essere in esecuzione sul dispositivo.

	Aggiungere il codice seguente al metodo `Main` per eseguire il push della campagna ai dispositivi di deviceList:

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. Compilare ed eseguire l'app console. L'output dovrebbe essere simile al seguente:


		C:\Users\Wesley\AzME_Service_API_Rest\test.exe

		Getting authorization token...
		
		HTTP Status 200 : OK
		{
		  "token_type": "Bearer",
		  "expires_in": "3599",
		  "expires_on": "1458085162",
		  "not_before": "1458081262",
		  "resource": "https://management.core.windows.net/",
		  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
		b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
		NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
		MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
		F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
		hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
		}
		
		Got authorization token...
		Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
		aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
		Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
		OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
		iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
		vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
		
		Creating new campaign...
		
		HTTP Status 201 : Created
		{
		  "id": 24,
		  "state": "draft"
		}
		
		Created new campaign...
		New Campaign Id    : 24
		
		Activating new campaign (ID : 24)...
		
		HTTP Status 200 : OK
		{
		  "id": 24,
		  "state": "scheduled"
		}
		
		Activated new campaign...
		New Campaign State : scheduled
		
		Getting device IDs...
		
		HTTP Status 200 : OK
		{
		  "value": [
		    {
		      "meta": {
		        "lastSeen": 1458080534371,
		        "firstSeen": 1458080534371,
		        "lastLocation": 1458081389617,
		        "lastInfo": 1458080571603
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
		    },
		    {
		      "meta": {
		        "lastSeen": 1457990584698,
		        "firstSeen": 1457949384025,
		        "lastLocation": 1457990914895,
		        "lastInfo": 1457990584597
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "302486644890e26045884ee5aa0619ec"
		    }
		  ]
		}
		
		Got devices.
		Adding device for push campaign...
		Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
		user_name    : Wesley
		Adding device for push campaign...
		Device ID    : 302486644890e26045884ee5aa0619ec
		user_name    : Wesley
		
		Triggering push for new campaign (ID : 24)...
		
		
		{"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
		
		HTTP Status 200 : OK
		{
		  "invalidDeviceIds": []
		}
		
		Triggered push on new campaign.
		


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0713_2016-->