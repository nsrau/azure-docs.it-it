<properties
   pageTitle="Introduzione all'API di creazione report di Azure AD"
   description="Come iniziare a usare l'API di creazione report di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Introduzione all'API di creazione report di Azure AD

*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory offre un'ampia gamma di report di attività, sicurezza e controllo. I dati possono essere utilizzati tramite il portale di Azure ma possono risultare utili in molte altre applicazioni, ad esempio sistemi SIEM e strumenti di controllo o di business intelligence.

Le API di creazione report di Azure AD forniscono l'accesso ai dati a livello di codice tramite un set di API basate su REST che possono essere chiamate da vari linguaggi e strumenti di programmazione.

Questo articolo descrive come chiamare le API di creazione report di Azure AD tramite PowerShell. È possibile modificare lo script di PowerShell di esempio per accedere ai dati da qualsiasi report disponibile in formato JSON, XML o testo, a seconda delle esigenze dello scenario.

Per usare questo esempio, sarà necessario un tenant [Azure Active Directory](active-directory-whatis.md).

## Creazione di un'applicazione in Azure AD per accedere alle API

L'API di creazione report usa l'autenticazione [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso alle API Web. Per accedere alle informazioni disponibili nella directory, è necessario creare un'applicazione nella propria istanza di Active Directory e concederle le autorizzazioni appropriate per accedere ai dati di AAD.


### Creare un'applicazione
- Passare al [portale di gestione di Azure](https://manage.windowsazure.com/).
- Passare alla directory.
- Passare alle applicazioni.
- Nella barra inferiore fare clic su "Aggiungi".
	- Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
	- **Nome**: digitare un nome qualsiasi. È consigliabile usare un nome simile a "Applicazione API di creazione report".
	- **Tipo**: selezionare "Applicazione Web e/o API Web".
	- Fare clic sulla freccia per passare alla pagina successiva.
	- **URLaccesso**: ```http://localhost```.
	- **URI ID app**: ```http://localhost```.
	- Fare clic sul segno di spunta per completare l'aggiunta dell'applicazione.

### Concedere all'applicazione le autorizzazioni per l'uso dell'API
- Passare alla scheda Applicazioni.
- Passare all'applicazione appena creata.
- Fare clic sulla scheda **Configura**.
- Nella sezione "Autorizzazioni per altre applicazioni":
	- In Microsoft Azure Active Directory > Autorizzazioni applicazione selezionare **Leggi i dati della directory**.
- Fare clic su **Salva** nella barra inferiore.


### Ottenere l'ID directory, l'ID client e il segreto client

La procedura seguente illustra come ottenere l'ID client e il segreto client dell'applicazione. Sarà inoltre necessario conoscere il nome del tenant, che può essere il nome di dominio *.onmicrosoft.com o un nome di dominio personalizzato. Copiare i dati in una posizione separata. Saranno necessari per modificare lo script.

#### ID client dell'applicazione
- Passare alla scheda Applicazioni.
- Passare all'applicazione appena creata.
- Passare alla scheda **Configura**.
- L'ID client dell'applicazione è visualizzato nel campo **ID client**.

#### Segreto client dell'applicazione
- Passare alla scheda Applicazioni.
- Passare all'applicazione appena creata.
- Passare alla scheda Configura.
- Generare una nuova chiave privata per l'applicazione selezionando una durata nella sezione "Chiavi".
- La chiave verrà visualizzata al momento del salvataggio. Assicurarsi di copiarla e incollarla in un luogo sicuro, perché non è possibile recuperarla in un secondo momento.


## Modificare lo script
Modificare uno degli script seguenti in modo che funzioni con la directory, sostituendo $ClientID, $ClientSecret e $tenantdomain con i valori corretti in "Delega dell'accesso in Azure AD".

### Script di PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

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

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


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
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)
	
	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Eseguire lo script
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report degli eventi di controllo.

Lo script restituisce l'elenco di tutti i report disponibili e restituisce l'output del report AccountProvisioningEvents nella finestra di PowerShell in formato JSON. Crea anche i file con lo stesso output in formato JSON, testo e XML. È possibile sperimentare ulteriormente modificando lo script inserendo commenti affinché restituisca i dati da altri report e rimuovere i commenti per i formati di output non necessari.

## Note

- Non esiste alcun limite al numero di eventi restituiti da Azure AD Reporting API (utilizzando l’impaginazione OData).
	- Per i limiti di conservazione sui dati dei report, consultare [Criteri di conservazione dei report](active-directory-reporting-retention.md).


## Passaggi successivi
- Per informazioni sui report di sicurezza, controllo e attività disponibili, vedere [Report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md)
- Per informazioni dettagliate sul report di controllo, vedere [Eventi dei report di controllo di Azure AD](active-directory-reporting-audit-events.md)
- Per informazioni dettagliate sul servizio REST dell'API Graph, vedere [Report ed eventi di Azure AD (anteprima)](https://msdn.microsoft.com/library/azure/mt126081.aspx)

<!---HONumber=AcomDC_0211_2016-->