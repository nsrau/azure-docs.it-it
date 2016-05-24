<properties
   pageTitle="Anteprima di Microsoft Power BI Embedded: incorporare un report di Power BI con un oggetto IFrame"
   description="Anteprima di Microsoft Power BI Embedded: codice essenziale per integrare un report in un'app, come eseguire l'autenticazione con token dell'app Power BI Embedded, come ottenere i report"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Incorporare un report di Power BI con un oggetto IFrame
Questo articolo illustra il codice essenziale per usare l'API REST **Power BI Embedded**, i token dell'app, un IFrame e codice JavaScript per integrare o incorporare un report nell'app.

In [Introduzione a Microsoft Power BI Embedded: anteprima](power-bi-embedded-get-started.md), verrà illustrato come configurare un **Raccolta di aree di lavoro** perché contenga una o più **Aree di lavoro** per il contenuto del report. In [Introduzione all'esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) si importa quindi un report in un'**Area di lavoro**.

Questo articolo illustra i passaggi necessari per incorporare un report nell'app. Per seguire la procedura in questo articolo, è necessario scaricare l'esempio per [integrare un report con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) in GitHub. Questo esempio è una semplice app Web Form ASP.NET che ha lo scopo di illustrare il codice C# e JavaScript essenziale necessario per integrare un report. Per un esempio più avanzato che usa lo schema progettuale Model-View-Controller (MVC) per integrare un report, vedere l'[app Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493) in GitHub.

Per prima cosa viene descritto come integrare un report di **Power BI Embedded** nell'app.

Ecco i passaggi da eseguire per integrare un report.

- Passaggio 1: [Ottenere un report in un'area di lavoro](#GetReport). In questo passaggio si userà un flusso di token dell'app per ottenere un token di accesso per chiamare l'operazione REST [Ottieni report](https://msdn.microsoft.com/library/mt711510.aspx). Dopo aver ottenuto un report dall'elenco generato da **Ottieni report**, si incorporerà il report in un'app con un elemento **IFrame**.
- Passaggio 2: [Incorporare un report in un'app](#EmbedReport). In questo passaggio si userà un token di incorporamento per un report, codice JavaScript e un elemento IFrame per integrare, ovvero incorporare, un report in un'app Web.

Se si vuole eseguire l'esempio per vedere come integrare un report, scaricare l'esempio per [integrare un report con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) in GitHub e configurare tre impostazioni di Web.config:

- **AccessKey**: un elemento **AccessKey** viene usato per generare un Token Web JSON (JWT) usato per ottenere report e incorporare un report. Per altre informazioni su**AccessKey**, vedere [Introduzione a Microsoft Power BI Embedded: anteprima](power-bi-embedded-get-started.md).
- **WorkspaceName**: per informazioni su come ottenere un elemento **WorkspaceName**, vedere [Introduzione a Microsoft Power BI Embedded: anteprima](power-bi-embedded-get-started.md).
- **WorkspaceId**: per informazioni su come ottenere un elemento **WorkspaceId**, vedere [Introduzione a Microsoft Power BI Embedded: anteprima](power-bi-embedded-get-started.md).

Le sezioni successive illustrano il codice necessario per integrare un report.

<a name="GetReport"/>
## Ottenere un report in un'area di lavoro

Per integrare un report in un'app, è necessario un **ID** e un **embedUrl** del report. Per ottenere un **ID** e un **embedUrl** del report, chiamare l'operazione REST [Ottieni report](https://msdn.microsoft.com/library/mt711510.aspx) e scegliere un report dall'elenco JSON. In [Incorporare un report in un'app](#EmbedReport) si usa un **ID** e un **embedUrl** per incorporare il report in un'app.

### Ottenere report di risposta JSON
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Per chiamare l'operazione REST [Ottieni report](https://msdn.microsoft.com/library/mt711510.aspx), usare un token dell'app. Per altre informazioni sul flusso di token dell'app, vedere [Informazioni sul flusso dei token delle app in Power BI Embedded](power-bi-embedded-app-token-flow.md). Il codice seguente descrive come ottenere un elenco di report JSON. Per incorporare un report, vedere [Incorporare un report in un'app](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

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

Prima di incorporare un report nell'app, è necessario un token di incorporamento per un report. Questo token è simile a un token dell'app usato per chiamare operazioni REST **Power BI Embedded**, ma viene generato per una risorsa del report invece di una risorsa REST. Ecco il codice per ottenere un token dell'app per un report. Per usare il token dell'app per il report, vedere [Incorporare report nell'app](#EmbedReportJS).

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

## Conclusioni

Questo articolo ha illustrato il codice per l'integrazione di un report di **Power BI** in un'app. Per iniziare rapidamente a integrare un report in un'app, scaricare questi esempi in GitHub:

- [Esempio di integrazione di un report con IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [App Web dashboard di esempio](http://go.microsoft.com/fwlink/?LinkId=761493)

## Vedere anche
- [Introduzione alla versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
- [Esempio introduttivo](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Ottieni report](https://msdn.microsoft.com/library/mt711510.aspx)

<!---HONumber=AcomDC_0518_2016-->