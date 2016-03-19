<properties 
	pageTitle="Introduzione all'autenticazione delle API con Azure Mobile Engagement"
	description="Questo articolo è una Guida alla migrazione rivolta ai clienti che usavano l'autenticazione con API Capptain e ora devono eseguire l'autenticazione con le nuove API Azure Mobile Enagagement." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement: Uso delle API per l'autenticazione

## Panoramica

Questo documento descrive in modo dettagliato come far funzionare il meccanismo di autenticazione per le nuove API.

Si presuppone che l'utente abbia una sottoscrizione di Azure valida e che abbia creato un'app Mobile Engagement usando una delle [Esercitazioni per sviluppatori](mobile-engagement-windows-store-dotnet-get-started.md).

## Autenticazione

È necessario usare per l'autenticazione un token OAuth basato su Microsoft Azure Active Directory.

Per autenticare richieste API, è necessario aggiungere a ciascuna richiesta un'intestazione di autorizzazione. Tale intestazione deve essere nel formato seguente:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] I token di Azure Active Directory scadono in un'ora.

È possibile ottenere un token in diversi modi: Dal momento che le API vengono in genere chiamate da un servizio cloud, è probabile che si voglia usare una chiave API. Nella terminologia di Azure una chiave API è una password dell'entità servizio. La procedura seguente descrive come impostarla in modo manuale.

> [AZURE.WARNING] La chiave visualizzata in Azure Active Directory NON è la chiave API di Mobile Engagement mostrata nel portale. Il concetto di chiave API di Mobile Engagement è deprecato ed è stato sostituito dall'autenticazione AAD descritta in questo documento.

#### Installazione singola (manuale)

Durante l'esecuzione della procedura annotare le informazioni seguenti perché saranno necessarie in seguito:
	
1. Creare un'applicazione Azure Active Directory usando [questa Guida](../resource-group-create-service-principal-portal.md).

	- Nel presente documento il nome scelto per l'applicazione avrà il formato `{AD_APP_NAME}`.
	- Nel presente documento il nome dell'identificatore client visualizzato nel menu di configurazione avrà il formato `{CLIENT_ID}`.
	- Nel presente documento la chiave visualizzata una sola volta dopo il salvataggio avrà il formato `{CLIENT_SECRET}`.
	- Fare clic sul pulsante **VISUALIZZA ENDPOINT** nella barra inferiore e copiare l'URL dell'**ENDPOINT TOKEN OAUTH 2.0**, denominato `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` nel presente documento. <br/>                                    
2. Assegnare un ruolo all'entità servizio, ad esempio Reader o Owner, usando l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

	Se si esegue un sistema operativo Windows, per usare i comandi di Azure modificare la variabile di ambiente `PATH` in modo che includa `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin`.

	Eseguire i comandi seguenti per impostare l'account con l'interfaccia della riga di comando di Azure:

		azure config mode arm 
		azure login

	Usare quindi il comando seguente per individuare l'ID oggetto dell'applicazione.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Notare `Object Id` nell'output.

	Assegnare quindi il ruolo `Owner` all'entità servizio usando il comando seguente:
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Installazione singola (mediante script)

Di seguito è descritto un modo alternativo per eseguire la procedura illustrata in precedenza usando uno script di PowerShell.

1. Usare la versione più recente di Azure PowerShell. Per le istruzioni di download vedere questo [collegamento](../powershell-install-configure.md). 

2. Aprire Windows PowerShell in modalità di amministrazione e assicurarsi di aver installato i [cmdlet di Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx).

		Install-Module AzureRM
		Install-AzureRM

3. Eseguire il comando seguente:

		Import-Module AzureRM.profile

4. Eseguire il comando seguente per avviare una finestra di dialogo di accesso. Dopo l'accesso, il comando visualizzerà la sottoscrizione "attiva", ovvero quella su cui avranno effetto i comandi eseguiti.

		Add-AzureRmAccount

5. Eseguire il comando seguente per ottenere un elenco di tutte le sottoscrizioni: Copiare il GUID di quella che si vuole usare.

		Get-AzureRmSubscription

6. Eseguire il comando seguente specificando il GUID per configurare la sottoscrizione da usare. Questo accorgimento è particolarmente utile quando si hanno più sottoscrizioni.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Copiare il testo per lo script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) nel computer locale ed eseguirlo.

	>[Azure.Note] I criteri di sicurezza predefiniti possono bloccare l'esecuzione degli script PowerShell. In questo caso, configurare temporaneamente i criteri di esecuzione in modo da consentire l'esecuzione degli script usando il comando seguente:

	>	Set-ExecutionPolicy RemoteSigned

	Lo script chiederà di specificare un nome da assegnare all'entità servizio. È possibile scegliere qualsiasi nome.

	Dopo il completamento dello script verranno visualizzati quattro valori che è necessario autenticare a livello di programmazione con AD: **TenantId**, **SubscriptionId**, **ApplicationId** e **Secret**.

	Copiare questi valori per riferimento. Per ottenere un token di accesso, specificare TenantId come `{TENANT_ID}`, ApplicationId come `{CLIENT_ID}` e Secret come `{CLIENT_SECRET}`.

8. Nel portale di gestione di Azure verificare se nell'area **Applicazioni di proprietà dell'azienda** viene visualizzata una nuova applicazione di Active Directory.

#### Procedura per ottenere un token valido

Per ottenere un nuovo token chiamare l'API seguente:

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

Di seguito è riportata una richiesta di esempio:

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Di seguito è riportata una risposta di esempio:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

Questo esempio include la codifica URL dei parametri POST. Il valore `resource` effettivo è `https://management.core.windows.net/`. Prestare attenzione anche alla codifica URL `{CLIENT_SECRET}` perché può contenere caratteri speciali.

Ora in ogni chiamata all'API includere l'intestazione della richiesta di autorizzazione:

	Authorization: Bearer {ACCESS_TOKEN}

Se viene restituito il codice di stato 401, rivedere il corpo della risposta e controllare che il token non sia scaduto. In caso affermativo, ottenere un nuovo token.

##Uso delle API

Ora che si dispone di un token valido, è possibile eseguire chiamate API.

1. È necessario passare in ciascuna richiesta API un token valido e non scaduto, ottenuto nella sezione precedente.

2. È necessario inserire alcuni parametri nella richiesta URI che identifica l'applicazione. L'URI di richiesta è simile al seguente:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Per ottenere i parametri, fare clic sul nome dell'applicazione e quindi su Dashboard. Verrà visualizzata una pagina simile a quella riportata di seguito, con tutti e tre i parametri.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/>
>1. Ignorare l'indirizzo radice dell'API perché riferito alle API precedenti.<br/> 2. È necessario usare il nome di risorsa dell'applicazione, che è diverso dal nome dell'applicazione stessa. 

<!---HONumber=AcomDC_0302_2016-->