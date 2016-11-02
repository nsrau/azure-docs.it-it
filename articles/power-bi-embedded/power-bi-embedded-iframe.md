<properties
   pageTitle="Come usare Power BI Embedded con REST | Microsoft Azure"
   description="Informazioni su come usare Power BI Embedded con REST  "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>


# <a name="how-to-use-power-bi-embedded-with-rest"></a>Come usare Power BI Embedded con REST


## <a name="power-bi-embedded:-what-it-is-and-what-it's-for"></a>Power BI Embedded: che cos'è e a cosa serve
Sebbene una panoramica di Power BI Embedded sia disponibile nel [sito di Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/)ufficiale, di seguito viene illustrata una breve introduzione prima di analizzare più dettagliatamente il relativo uso con REST.

È davvero semplice. Spesso i fornitori di software indipendenti desiderano usare le visualizzazioni dei dati dinamici di [Power BI](https://powerbi.microsoft.com) nelle proprie applicazioni come blocchi predefiniti dell'interfaccia utente.

Tuttavia, è noto che l'incorporamento dei report o dei riquadri di Power BI nelle pagine Web è già possibile senza il servizio Power BI Embedded di Azure, ovvero usando l' **API Power BI**. Per condividere i report nella stessa organizzazione, è possibile incorporarli con l'autenticazione di Azure AD. L'utente che visualizza i report deve eseguire l'accesso usando il proprio account Azure AD. Per condividere i report per tutti gli utenti (tra cui gli utenti esterni), è possibile incorporarli semplicemente con l'accesso anonimo.

Tuttavia, questa soluzione di incorporamento semplice non soddisfa del tutto le esigenze delle applicazioni dei fornitori di software indipendenti.
La maggior parte delle applicazioni dei fornitori di software indipendenti deve trasmettere i dati per i propri clienti, che non sono necessariamente utenti della propria organizzazione. Ad esempio, se si offrono alcuni servizi all'azienda A e all'azienda B, gli utenti dell'azienda A dovrebbero accedere solo ai dati per l'azienda A. Pertanto, è necessaria una distribuzione multi-tenant.

Le applicazioni dei fornitori di software indipendenti potrebbero anche offrire i propri metodi di autenticazione, ad esempio l'autenticazione basata su moduli, l'autenticazione di base e così via. In tal caso, la soluzione di incorporamento deve collaborare in modo sicuro con questi metodi di autenticazione esistenti. È inoltre necessario che gli utenti siano in grado di usare tali applicazioni dei fornitori di software indipendenti senza acquisti aggiuntivi o licenze di una sottoscrizione di Power BI.

 **Power BI Embedded** è progettato esattamente per questi tipi di scenari relativi ai fornitori di software indipendenti. Dopo questa rapida introduzione, Power BI Embedded verrà ora analizzato più dettagliatamente.

Per creare applicazioni con Power BI Embedded in modo semplice, è possibile usare l'SDK per Node.js o .NET \(C#). Tuttavia, in questo articolo verrà illustrato il flusso HTTP \(tra cui AuthN) di Power BI senza SDK. Dopo aver compreso tale flusso, è possibile creare applicazioni **con qualsiasi linguaggio di programmazione** e capire la profonda essenza di Power BI Embedded.

## <a name="create-power-bi-workspace-collection,-and-get-access-key-\(provisioning)"></a>Creare una raccolta di aree di lavoro di Power BI e ottenere la chiave di accesso \(provisioning)
Power BI Embedded è uno dei servizi di Azure. Le spese di utilizzo vengono addebitate solo ai fornitori di software indipendenti che usano il portale di Azure \(per sessione utente oraria), mentre l'utente che visualizza il report non riceve addebiti e non necessita di una sottoscrizione di Azure.
Prima di iniziare a sviluppare applicazioni, è necessario creare la **raccolta di aree di lavoro di Power BI** tramite il portale di Azure.

Ogni area di lavoro di Power BI Embedded è destinata a un cliente (tenant) ed è possibile aggiungere molte aree di lavoro in ogni raccolta di aree di lavoro. In ogni raccolta di aree di lavoro viene usata la stessa chiave di accesso. In effetti, la raccolta di aree di lavoro è il limite di sicurezza per Power BI Embedded.

![](media\power-bi-embedded-iframe\create-workspace.png)

Al termine della creazione della raccolta di aree di lavoro, copiare la chiave di accesso dal portale di Azure.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] È inoltre possibile eseguire il provisioning della raccolta di aree di lavoro e ottenere la chiave di accesso tramite l'API REST. Per altre informazioni, vedere [Power BI Resource Provider APIs](https://msdn.microsoft.com/library/azure/mt712306.aspx)(API del provider di risorse di Power BI).

## <a name="create-.pbix-file-with-power-bi-desktop"></a>Creare file con estensione pbix con Power BI Desktop
Successivamente, è necessario creare la connessione dati e i report da incorporare.
Per questa attività, non è disponibile alcun codice o programmazione. È sufficiente usare Power BI Desktop.
In questo articolo non verrà analizzato l'uso di Power BI Desktop nel dettaglio. Per assistenza, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Per questo esempio, sarà sufficiente usare l' [esempio di analisi delle vendite al dettaglio](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

Ora che il provisioning è stato eseguito, è possibile iniziare a creare l'area di lavoro di un cliente nella raccolta di aree di lavoro tramite le API REST. La richiesta POST HTTP (REST) crea la nuova area di lavoro nella raccolta di aree di lavoro esistenti. In questo esempio il nome della raccolta di aree di lavoro è **mypbiapp**.
È sufficiente impostare la chiave di accesso, copiata in precedenza, come **AppKey**. L'autenticazione è molto semplice.

**Richiesta HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Il **workspaceId** restituito viene usato per le chiamate API seguenti. L'applicazione deve mantenere questo valore.

## <a name="import-.pbix-file-into-the-workspace"></a>Importare file con estensione pbix nell'area di lavoro
Ogni area di lavoro può ospitare un unico file di Power BI Desktop con un set di dati \(tra cui le impostazioni dell'origine dati) e i report. È possibile importare il file con estensione pbix nell'area di lavoro come mostrato nel codice riportato di seguito. Come si può notare, è possibile caricare il binario del file con estensione pbix usando MIME multipart in http.

Il frammento URI **32960a09-6366-4208-a8bb-9e0678cdbb9d** è il workspaceId e il parametro di query **datasetDisplayName** è il nome del set di dati da creare. Il set di dati creato contiene tutti gli elementi del file con estensione pbix relativi ai dati, ad esempio i dati importati, il puntatore all'origine dati e così via.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Questa attività di importazione potrebbe richiedere qualche minuto. Al termine, l'applicazione può richiedere lo stato dell'attività usando l'id di importazione. In questo esempio l'id di importazione è **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Il codice seguente consente di richiedere lo stato usando l'id di importazione:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Se l'attività non viene completata, la risposta HTTP potrebbe essere simile alla seguente:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Se l'attività viene completata, la risposta HTTP potrebbe essere simile alla seguente:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-\(and-multi-tenancy-of-data)"></a>Connettività dell'origine dati \(e multi-tenancy dei dati)
Sebbene quasi tutti gli elementi del file con estensione pbix vengano importati nell'area di lavoro, le credenziali per le origini dati restano escluse. Di conseguenza, quando si usa la **modalità DirectQuery**, il report incorporato non può essere mostrato correttamente. Tuttavia, quando si usa la **modalità di importazione**, è possibile visualizzare il report usando i dati importati esistenti. In tal caso, è necessario impostare le credenziali attenendosi alla procedura seguente tramite chiamate REST.

Innanzitutto, è necessario ottenere l'origine dati del gateway. L' **id** del set di dati corrisponde all'id restituito in precedenza.

**Richiesta HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Grazie all'ID del gateway e all'ID dell'origine dati restituiti \(vedere il **gatewayId** e l'**id** precedenti nel risultato restituito), è possibile modificare le credenziali dell'origine dati come segue:

**Richiesta HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Risposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Nell'ambiente di produzione è inoltre possibile impostare una stringa di connessione diversa per ogni area di lavoro usando l'API REST \(ad esempio, è possibile separare il database per ogni cliente).

Il codice seguente consente di modificare la stringa di connessione dell'origine dati tramite REST.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

In alternativa, è possibile usare la sicurezza a livello di riga di Power BI Embedded e separare i dati per ogni utente in un unico report. Di conseguenza, è possibile eseguire il provisioning di ogni report cliente con lo stesso file con estensione pbix \(interfaccia utente e così via) e origini dati diverse.

> [AZURE.NOTE] Se si usa la **modalità di importazione** anziché la **modalità DirectQuery**, non è possibile aggiornare i modelli tramite l'API. Inoltre, le origini dati locali mediante gateway di Power BI non sono ancora supportate in Power BI Embedded. Tuttavia, è consigliabile consultare il [blog di Power BI](https://powerbi.microsoft.com/blog/) per scoprire le novità e le versioni future.

## <a name="authentication-and-hosting-(embedding)-reports-in-our-web-page"></a>Autenticazione e hosting (incorporamento) dei report nella pagina Web

Nell'API REST precedente è possibile usare la chiave di accesso **AppKey** come intestazione di autorizzazione. Poiché queste chiamate possono essere gestite sul lato server back-end, questa operazione è sicura.

Tuttavia, quando si incorpora il report nella nostra pagina Web, questa tipologia di informazioni di sicurezza verrà gestita usando JavaScript \(front-end). Quindi il valore dell'intestazione di autorizzazione deve essere protetto. Se un utente malintenzionato o un codice dannoso individua la chiave di accesso, la può usare per chiamare qualsiasi operazione.

Quando si incorpora il report nella pagina Web, è necessario usare il token calcolato anziché la chiave di accesso **AppKey**. L'applicazione deve creare il token JSON Web \(JTW) OAuth, che prevede le attestazioni e la firma digitale calcolata. Come illustrato di seguito, questo token JTW OAuth è il token di stringa con codifica delimitato da punti.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Innanzitutto, è necessario preparare il valore di input, che viene firmato in un secondo momento. Questo valore è la stringa (rfc4648) con codifica URL base64 del JSON seguente ed è delimitato dal carattere punto \(.). Successivamente, verrà illustrato come ottenere l'id del report.

> [AZURE.NOTE] Se si vuole usare la sicurezza a livello di riga con Power BI Embedded, è necessario specificare anche **nome utente** e **ruoli** nelle attestazioni.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Subito dopo è necessario creare la stringa con codifica base64 del codice HMAC \(la firma) con l'algoritmo SHA256. Questo valore di input firmato è la stringa precedente.

Infine è necessario unire il valore di input e la stringa della firma usando il carattere punto \(.). La stringa completa è il token dell'applicazione per l'incorporamento del report. Anche se il token dell'applicazione viene individuato da un utente malintenzionato, quest'ultimo non riuscirà a ottenere la chiave di accesso originale. Questo token dell'applicazione scadrà rapidamente.

Ecco un esempio di codice PHP per questi passaggi:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally,-embed-the-report-into-the-web-page"></a>Incorporare il report nella pagina Web

Per incorporare il report, è necessario ottenere l'URL di incorporamento e l' **id** del report usando l'API REST seguente.

**Richiesta HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

È possibile incorporare il report nell'applicazione Web usando il token dell'applicazione precedente.
Se si esamina il codice di esempio successivo, la prima parte corrisponde all'esempio precedente. In quest'ultima parte **embedUrl** \(vedere il risultato precedente) si trova nell'iframe e il token dell'applicazione viene pubblicato nell'iframe.

> [AZURE.NOTE] Sarà necessario modificare il valore dell'id del report con uno personalizzato. Inoltre, a causa di un bug nel sistema di gestione dei contenuti, il tag iframe nell'esempio di codice viene letto in modo letterale. Rimuovere il testo racchiuso dal tag se si copia e incolla questo codice di esempio.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Ed ecco il risultato:

![](media\power-bi-embedded-iframe\view-report.png)

Al momento, Power BI Embedded mostra solo il report nell'iframe. Tuttavia, è consigliabile consultare il [blog di Power BI](). I miglioramenti futuri potrebbero usare le API lato client che consentiranno di inviare informazioni nell'iframe, nonché di ottenere informazioni. Ci sono tanti aspetti interessanti.


## <a name="see-also"></a>Vedere anche
- [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)



<!--HONumber=Oct16_HO2-->


