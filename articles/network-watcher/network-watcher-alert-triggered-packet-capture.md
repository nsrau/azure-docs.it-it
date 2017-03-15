---
title: Usare l&quot;acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Funzioni di Azure | Microsoft Docs
description: Questo articolo descrive come creare un&quot;acquisizione di pacchetti attivata da un avviso con Azure Network Watcher
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Usare l'acquisizione di pacchetti per eseguire il monitoraggio proattivo della rete con Funzioni di Azure

Il servizio di acquisizione di pacchetti di Network Watcher crea sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Il file di acquisizione può avere un filtro che viene definito per tenere traccia solo del traffico che si vuole monitorare. Questi dati vengono quindi archiviati in un BLOB di archiviazione o in locale nel computer guest. Questa funzionalità può essere avviata in remoto da altri scenari di automazione, ad esempio Funzioni di Azure. L'acquisizione di pacchetti consente di eseguire acquisizioni proattive in base alle anomalie di rete definite. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora.

Le risorse distribuite in Azure sono in esecuzione 24 ore su 24, 7 giorni su 7. Nessuno può monitorare attivamente lo stato di tutte le risorse 24 ore su 24, 7 giorni su 7. Si pensi a che cosa può accadere se si verifica un problema alle 2 di mattina.

Usando Network Watcher, Creazione di avvisi e Funzioni dall'ecosistema di Azure, è possibile rispondere in modo proattivo alle problematiche della rete con i dati e gli strumenti per risolvere il problema.

## <a name="before-you-begin"></a>Prima di iniziare

In questo esempio la VM invia più segmenti TCP del solito ed è possibile essere avvisati. Come esempio vengono usati i segmenti TCP, ma è possibile usare qualsiasi condizione di avviso. Quando si viene avvisati, è opportuno avere i dati a livello di pacchetto per sapere perché la comunicazione è aumentata e poter eseguire le procedure che consentono di ripristinare la normale comunicazione del computer.
Questo scenario presuppone che esista già un'istanza di Network Watcher e un gruppo di risorse con una macchina virtuale valida da usare.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scenario

Per automatizzare questo processo, nella VM vengono creati e connessi un avviso da attivare quando si verifica l'evento imprevisto e una funzione di Azure per chiamare Network Watcher.

Questo scenario illustrerà come:

* Creare una funzione di Azure che avvia un'acquisizione di pacchetti
* Creare una regola di avviso in una macchina virtuale
* Configurare la regola di avviso per chiamare la funzione di Azure

## <a name="creating-an-azure-function-and-overview"></a>Creazione di una funzione di Azure e panoramica

Il primo passaggio è la creazione di una funzione di Azure per elaborare l'avviso e creare un'acquisizione di pacchetti. 

L'elenco seguente è una panoramica del flusso di lavoro effettivo.

1. Nella macchina virtuale viene attivato un avviso.
1. L'avviso chiama la funzione di Azure tramite un webhook.
1. La funzione di Azure elabora l'avviso e avvia una sessione di acquisizione di pacchetti di Network Watcher.
1. L'acquisizione di pacchetti viene eseguita nella macchina virtuale e raccoglie il traffico. 
1. Il file di acquisizione viene caricato in un account di archiviazione per la revisione e diagnosi. 

È possibile creare una funzione di Azure nel portale seguendo le istruzioni contenute in [Creare la prima funzione di Azure](../azure-functions/functions-create-first-azure-function.md). Per questo esempio, viene scelta una funzione di tipo HttpTrigger-CSharp. 

> [!NOTE]
> Anche altri linguaggi includono le funzioni di Azure, ma possono essere sperimentali e non avere il supporto completo, ad esempio PowerShell e Python.

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Elaborazione dell'avviso e avvio di una sessione di acquisizione di pacchetti

A questo punto è necessario chiamare Network Watcher dalla funzione di Azure. L'implementazione di questa funzione è diversa a seconda dei requisiti. Il flusso generale del codice è tuttavia il seguente:

1. Elaborare i parametri di input
2. Effettuare una query delle acquisizioni di pacchetti esistenti, verificare i limiti e risolvere i conflitti di nomi
3. Creare un'acquisizione di pacchetti con i parametri appropriati
4. Eseguire regolarmente il polling dell'acquisizione di pacchetti fino al completamento
5. Notificare all'utente che la sessione di acquisizione di pacchetti è completa

L'esempio seguente è C# che può essere usato nella funzione di Azure. È necessario sostituire i valori per sottoscrizione, ID client, ID tenant e segreto client.

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

Dopo avere creato la funzione, è necessario configurare l'avviso per chiamare l'URL associato alla funzione. Per ottenere questo valore, copiare l'URL della funzione dall'app per le funzioni.

![Ricerca dell'URL della funzione][2]

Se sono necessarie proprietà personalizzate nel payload della richiesta POST del webhook, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>Configurare un avviso in una VM

Si possono configurare avvisi per notificare alle singole persone quando una metrica specifica supera una soglia assegnata. In questo esempio l'avviso è sui segmenti TCP inviati, ma può essere attivato per molte altre metriche. In questo esempio viene configurato un avviso per chiamare un webhook per chiamare la funzione.

### <a name="create-the-alert-rule"></a>Creare la regola di avviso

Passare a una macchina virtuale esistente e aggiungere una regola di avviso. Per informazioni più dettagliate sulla configurazione di avvisi, vedere [Usare il portale di Azure per creare avvisi per i servizi di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![Aggiungere una regola di avviso di macchina virtuale a una macchina virtuale][1]

> [!NOTE]
> Alcune metriche non sono abilitate per impostazione predefinita. Per altre informazioni su come abilitare metriche aggiuntive, vedere [Abilitare il monitoraggio e la diagnostica](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

Incollare infine l'URL del passaggio precedente nella casella di testo del webhook nell'avviso. Fare clic su **OK** per salvare la regola di avviso.

![Incollare l'URL nella regola di avviso][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Downloading e visualizzazione del file di acquisizione

Se si salva l'acquisizione in un account di archiviazione, il file di acquisizione può essere scaricato tramite il portale o a livello di codice. Se il file di acquisizione è archiviato in locale, viene recuperato accedendo alla macchina virtuale. 

Per istruzioni sul download di file dall'account di archiviazione di Azure, consultare [Introduzione all'archivio BLOB di Azure con .NET](../storage/storage-dotnet-how-to-use-blobs.md). Un altro strumento che può essere usato è Storage Explorer. Altre informazioni su Storage Explorer sono reperibili facendo clic sul collegamento seguente: [Storage Explorer](http://storageexplorer.com/).

Dopo il download dell'acquisizione, è possibile visualizzarla usando qualsiasi strumento per la lettura di un file **CAP**. I seguenti sono collegamenti a due di questi strumenti:

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare le acquisizioni di pacchetti, vedere [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md) (Analisi delle acquisizioni di pacchetti con Wireshark)

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

