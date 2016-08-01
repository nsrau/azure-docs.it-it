<properties
   pageTitle="Introduzione all'API di creazione report di Azure AD | Microsoft Azure"
   description="Come iniziare a usare l'API di creazione report di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/18/2016"
   ms.author="dhanyahk"/>

# Introduzione all'API di creazione report di Azure Active Directory

*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory (AD) offre un'ampia gamma di report di attività, sicurezza e controllo. I dati possono essere usati tramite il portale di Azure ma possono risultare utili in molte altre applicazioni, ad esempio sistemi SIEM e strumenti di controllo o di business intelligence.

Le [API di creazione report di Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) offrono l'accesso ai dati a livello di codice tramite un set di API basate su REST che possono essere chiamate da diversi linguaggi e strumenti di programmazione.

Questo articolo descrive come chiamare le API di creazione report di Azure AD tramite PowerShell. È possibile modificare lo script di PowerShell di esempio per accedere ai dati da qualsiasi report disponibile in formato JSON, XML o testo, a seconda delle esigenze dello scenario.

Per usare questo esempio, sarà necessario un tenant di [Azure Active Directory](active-directory-whatis.md).

## Creazione di un'applicazione in Azure AD per accedere alle API

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web. Per accedere alle informazioni della directory, è necessario creare un'applicazione nel tenant di Azure AD e assegnare all'applicazione le autorizzazioni appropriate per accedere ai dati di Azure AD.


### Registrare un'applicazione Azure AD
Per completare la registrazione dell'applicazione Azure AD è necessario accedere al portale di Azure classico con un account di amministratore di sottoscrizione di Azure che sia anche membro del ruolo Amministratore globale nel tenant di Azure AD. Questo perché l'applicazione Azure AD verrà registrata con autorizzazioni che richiedono la registrazione e il consenso con un account con privilegi di Amministratore globale.

> [AZURE.IMPORTANT] Le applicazioni eseguite con credenziali con privilegi "admin" come questa possono essere molto potenti, quindi assicurarsi di proteggere l'ID e il segreto dell'applicazione.


1. Passare al [portale di Azure classico](https://manage.windowsazure.com/).
2. Passare al tenant di Azure AD, nell'estensione **Active Directory** nel riquadro sinistro.
3. Passare alla scheda **Applicazioni**.
4. Nella barra inferiore fare clic su **Aggiungi**.
	- Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
	- **Nome**: digitare un nome qualsiasi. È consigliabile usare un nome simile a "Applicazione API di creazione report".
	- **Tipo**: selezionare "Applicazione Web e/o API Web".
	- Fare clic sulla freccia per passare alla pagina successiva.
	- **URLaccesso**: ```https://localhost```.
	- **URI ID app**: ```https://localhost/ReportingApiApp```.
	- Fare clic sul segno di spunta per completare l'aggiunta dell'applicazione.

### Concedere all'applicazione le autorizzazioni per l'uso dell'API
1. Passare alla scheda **Applicazioni**.
2. Passare all'applicazione appena creata.
3. Fare clic sulla scheda **Configura**.
4. Nella sezione "Autorizzazioni per altre applicazioni":
	- Per la risorsa "Microsoft Azure Active Directory" (autorizzazioni per l'API Graph di Azure AD), fare clic sull'elenco a discesa "Autorizzazioni applicazione" e selezionare **Lettura dati directory**.

        > [AZURE.IMPORTANT] Specificare le autorizzazioni corrette. Applicare "Autorizzazioni applicazione" e non "Autorizzazioni delegate", altrimenti l'applicazione non riceverà il livello di autorizzazione necessario per accedere all'API di creazione report e lo script riceverà un errore *"Unable to check Directory Read access for appId"* (Impossibile verificare l'accesso in lettura alla directory per appId).


5. Fare clic su **Salva** nella barra inferiore.

### Ottenere l'ID directory, l'ID client e il segreto client

La procedura seguente illustra come ottenere l'ID client e il segreto client dell'applicazione. Sarà inoltre necessario conoscere il nome del tenant, che può essere il nome di dominio *.onmicrosoft.com o un nome di dominio personalizzato. Copiare i valori in una posizione separata. Saranno necessari per modificare lo script in un secondo momento.

#### Ottenere il nome dominio del tenant di Azure AD
1. Passare al tenant di Azure AD, nell'estensione **Active Directory** nel riquadro sinistro.
2. Passare alla scheda **Domini**.
4. Verrà visualizzato il nome dominio "<tenant-name>.onmicrosoft.com" del tenant, insieme a eventuali nomi dominio personalizzati configurati.

#### Ottenere l'ID client dell'applicazione
1. Passare alla scheda **Applicazioni**.
2. Passare all'applicazione appena creata.
3. Passare alla scheda **Configura**.
4. L'ID client dell'applicazione è visualizzato nel campo **ID client**.

#### Ottenere il segreto client dell'applicazione
1. Passare alla scheda **Applicazioni**.
2. Passare all'applicazione appena creata.
3. Passare alla scheda **Configura**.
4. Generare una nuova chiave privata per l'applicazione selezionando una durata nella sezione "Chiavi".
5. La chiave verrà visualizzata al momento del salvataggio. Assicurarsi di copiarla e incollarla in un luogo sicuro, perché non è possibile recuperarla in un secondo momento.

## Modificare lo script
Modificare uno degli script seguenti in modo che funzioni con la directory, sostituendo $ClientID, $ClientSecret e $tenantdomain con i valori corretti indicati nelle sezioni precedenti.

### Script di PowerShell
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/reports/auditEvents?api-version=beta&`$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Script di Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Eseguire lo script
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report degli eventi di controllo.

Lo script restituisce l'output del report auditEvents in formato JSON. Crea anche un file `auditEvents.json` con lo stesso output. È possibile provare a modificare lo script per restituire i dati di altri report e rimuovere i commenti per i formati di output non necessari.

## Note

- Non esiste alcun limite al numero di eventi restituiti da Azure AD Reporting API (utilizzando l’impaginazione OData).
- Per i limiti di conservazione sui dati dei report, consultare [Criteri di conservazione dei report](active-directory-reporting-retention.md).


## Passaggi successivi
- Per informazioni sui report di sicurezza, controllo e attività disponibili, vedere i [report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md). È anche possibile visualizzare tutti gli endpoint disponibili dell'API Graph di Azure AD passando a [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta) e a [Reports and events (preview) | Graph API concepts](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) (Report ed eventi - anteprima | Concetti dell'API Graph).
- Per informazioni dettagliate sul report di controllo, vedere [Eventi dei report di controllo di Azure AD](active-directory-reporting-audit-events.md)
- Per informazioni dettagliate sul servizio REST dell'API Graph di Azure AD, vedere [Reports and events (preview) | Graph API concepts](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) (Report ed eventi - anteprima | Concetti dell'API Graph).

<!---HONumber=AcomDC_0720_2016-->