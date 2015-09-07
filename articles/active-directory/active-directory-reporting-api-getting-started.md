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
	ms.date="07/17/2015"
	ms.author="kenhoff;yossib"/>


# Introduzione all'API di creazione report di Azure AD

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
    $ClientID      = "<<YOUR CLIENT ID HERE>>"                # Should be a ~35 character string insert your info here
    $ClientSecret  = "<<YOUR CLIENT SECRET HERE>>"          # Should be a ~44 character string insert your info here
    $loginURL      = "https://login.windows.net"
    $tenantdomain  = "<<YOUR TENANT NAME HERE>>"            # For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
        $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        # Returns a list of all the available reports
        Write-host List of available reports
        Write-host =========================
        $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
        Write-host $allReports.Content

        Write-host
        Write-host Data from the AccountProvisioningEvents report
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "accountProvisioningEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/accountProvisioningEvents?api-version=beta")
        Write-host $myReport.Content

        Write-host
        Write-host Data from the AuditEvents report with datetime filter
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "auditEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt 2015-05-20")
        Write-host $myReport.Content

        # Options for other output formats

        # to output the JSON use following line
        $myReport.Content | Out-File -FilePath accountProvisioningEvents.json -Force

        # to output the content to a name value list
        ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath accountProvisioningEvents.txt -Force

        # to output the content in XML use the following line
        (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath accountProvisioningEvents.xml -Force

    } else {
        Write-Host "ERROR: No Access Token"
        }

### Script di Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="<<YOUR CLIENT ID HERE>>"			# Should be a ~35 character string insert your info here
    CLIENT_SECRET="<<YOUR CLIENT SECRET HERE>>"	# Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="<<YOUR TENANT NAME HERE>>"	 # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | jq -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | jq -r '.access_token')

    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta)

    echo $REPORT | jq -r '.value' | jq -r ".[]"




## Eseguire lo script
Una volta modificato lo script, eseguirlo e verificare che vengano restituiti i dati corretti dal report degli eventi di controllo.

Lo script restituisce l'elenco di tutti i report disponibili e restituisce l'output del report AccountProvisioningEvents nella finestra di PowerShell in formato JSON. Crea anche i file con lo stesso output in formato JSON, testo e XML. È possibile sperimentare ulteriormente modificando lo script inserendo commenti affinché restituisca i dati da altri report e rimuovere i commenti per i formati di output non necessari.


## Passaggi successivi
- Per informazioni sui report di sicurezza, controllo e attività disponibili, vedere [Report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md)
- Per informazioni dettagliate sul report di controllo, vedere [Eventi dei report di controllo di Azure AD](active-directory-reporting-audit-events.md)
- Per informazioni dettagliate sul servizio REST dell'API Graph, vedere [Report ed eventi di Azure AD (anteprima)](https://msdn.microsoft.com/library/azure/mt126081.aspx)

<!---HONumber=August15_HO9-->