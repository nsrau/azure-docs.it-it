<properties
   pageTitle="Microsoft Power BI Embedded: incorporare un report di Power BI con un oggetto IFrame"
   description="Microsoft Power BI Embedded: codice essenziale per integrare un report in un'app, come eseguire l'autenticazione con token dell'app Power BI Embedded, come ottenere i report"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/19/2016"
   ms.author="owend"/>

# Incorporare un report di Power BI con un oggetto IFrame
Questo articolo illustra il codice essenziale per usare l'API REST **Power BI Embedded**, i token dell'app, un IFrame e codice JavaScript per integrare o incorporare un report nell'app.

[Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md) illustra come configurare una **Raccolta di aree di lavoro** perché contenga una o più **aree di lavoro** per il contenuto del report. In [Introduzione all'esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) si importa quindi un report in un'**Area di lavoro**.

Questo articolo illustra i passaggi necessari per incorporare un report nell'app. Per seguire la procedura in questo articolo, è necessario scaricare l'esempio per [integrare un report con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) in GitHub. Questo esempio è una semplice app Web Form ASP.NET che ha lo scopo di illustrare il codice C# e JavaScript essenziale necessario per integrare un report. Per un esempio più avanzato che usa lo schema progettuale Model-View-Controller (MVC) per integrare un report, vedere l'[app Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493) in GitHub.

Di seguito sono descritti i passaggi da eseguire per integrare un report:

- Passaggio 1: [Ottenere un report in un'area di lavoro](#GetReport). In questo passaggio si userà un flusso di token dell'app per ottenere un token di accesso per chiamare l'operazione REST [Ottieni report](https://msdn.microsoft.com/library/mt711510.aspx). Dopo aver ottenuto un report dall'elenco generato da **Ottieni report**, si incorporerà il report in un'app con un elemento **IFrame**.
- Passaggio 2: [Incorporare un report in un'app](#EmbedReport). In questo passaggio si userà un token di incorporamento per un report, codice JavaScript e un elemento IFrame per integrare, ovvero incorporare, un report in un'app Web.

Se si vuole eseguire l'esempio, scaricare l'esempio per [integrare un report con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) in GitHub e configurare tre impostazioni di Web.config:

- **AccessKey**: un elemento **AccessKey** viene usato per generare un token JSON Web (JWT) usato per ottenere e incorporare report.
- **Workspace Collection Name**: identifica l'area di lavoro.
- **Workspace Id**: ID univoco dell'area di lavoro

Per informazioni su come ottenere i valori di AccessKey, Workspace Collection Name e Workspace Id dal portale di Azure, vedere [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

<a name="GetReport"/>
## Ottenere un report in un'area di lavoro

Per integrare un report in un'app sono necessari gli elementi **ID** e **embedUrl** del report. Per ottenere questi elementi, chiamare l'operazione REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) e scegliere un report dall'elenco JSON.

### Ottenere report di risposta JSON
```
{
  "@odata.context":"https://api.powerbi.com/v1.0/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Per chiamare l'operazione REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) usare un token dell'app. Per altre informazioni sul flusso del token dell'app, vedere [Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md). Il codice seguente descrive come ottenere un elenco di report JSON.

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/v1.0/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppKey {0}", accessKey));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}

```

<a name="EmbedReport"/>
## Incorporare un report in un'app

Prima di incorporare un report nell'app, è necessario un token di incorporamento per un report. Questo token è simile a un token dell'app usato per chiamare operazioni REST di Power BI Embedded, ma viene generato per una risorsa del report invece di una risorsa REST. Ecco il codice per ottenere un token dell'app per un report.

<a name="EmbedReportToken"/>
### Ottenere un token dell'app per un report

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Incorporare report nell'app

Per incorporare un report di **Power BI** nell'app, usare un elemento IFrame e codice JavaScript. Di seguito è riportato un elemento IFrame di esempio e codice JavaScript per incorporare un report. Per visualizzare tutto il codice di esempio per incorporare un report, vedere l'esempio per [integrare un report con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) in GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Il report appena incorporato nell'applicazione può essere filtrato. La sezione seguente illustra come filtrare un report usando una sintassi dell'URL.

## Filtrare un report

È possibile filtrare un report incorporato tramite una sintassi dell'URL. A tale scopo, aggiungere un parametro della stringa di query all'URL iFrame src specificando il filtro. È possibile **filtrare in base a un valore** e **nascondere il riquadro filtro**.


**Filtrare in base a un valore**

Per filtrare in base a un valore è possibile usare una sintassi di query **$filter** con un operatore **eq**, come indicato di seguito:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Ad esempio, è possibile applicare un filtro in base alla catena di negozi "Lindseys". La parte del filtro dell'URL avrebbe un aspetto simile al seguente:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} non può includere spazi o caratteri speciali. {fieldValue} accetta un singolo valore categorico.

**Nascondere il riquadro filtro**

Per nascondere il **riquadro filtro** aggiungere **filterPaneEnabled** alla stringa di query del report, come indicato di seguito:

```
&filterPaneEnabled=false
```

## Risorse aggiuntive

Questo articolo ha illustrato il codice per l'integrazione di un report di **Power BI** in un'app. Vedere questi esempi aggiuntivi in GitHub:

- [Esempio di integrazione di un report con IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [App Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493)

## Vedere anche
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0720_2016-->