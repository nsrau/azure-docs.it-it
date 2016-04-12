<properties
	pageTitle="Configurare un'API per connettersi al sistema back-end in un dominio di Azure Active Directory in PowerApps | Microsoft Azure"
	description="Configurare un'API per connettersi al sistema back-end protetto di ADD in PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Configurare un'API per la connessione a una risorsa back-end in un dominio di Azure Active Directory
Man mano che gli utenti creano domini in Azure Active Directory (ADD), anche le risorse back-end vengono aggiunte ai domini di ADD. È possibile creare e configurare API per la connessione a queste risorse back-end.

#### Prerequisiti per iniziare

- Iscrizione a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Creare un [ambiente di servizio app](powerapps-get-started-azure-portal.md).
- Installare [Azure PowerShell][11] 1.0 Preview o versione successiva.
- Registrare un'API nell’[ambiente del servizio app](powerapps-register-api-hosted-in-app-service.md).

## Passaggio 1: creare un'applicazione di Active Directory e assegnare ad essa le autorizzazioni

Per accedere al sistema back-end in un dominio in ADD, creare un'applicazione di ADD e assegnare ad essa le autorizzazioni appropriate per il back-end esistente (che è anch’esso un'applicazione di ADD). Passaggi:

1. Nel [portale di Azure classico][13] passare ad Azure Active Directory, aprire il tenant (o la directory) e selezionare la scheda **Applicazioni**: ![][14]
2. Selezionare il pulsante **Aggiungi** nella parte inferiore. Quindi:  

	a) Scegliere **Aggiungi un'applicazione che l'organizzazione sta sviluppando**. b) Immettere un nome per l'applicazione e selezionare **Applicazione Web e/o API Web**. c) In **URL accesso** e **URI ID app**, immettere URL univoci all'interno di ADD e URL che abbiano senso per l'organizzazione. Ad esempio, è possibile immettere http://powerappssignon.contoso.com o http://powerappsappid.contoso.com. È consigliabile utilizzare un URL nel dominio di ADD dell'organizzazione. Gli URL vengono utilizzati come identificatori e non esiste alcun requisito per cui devono esistere. Nessun altro sfoglierà gli URL immessi. È possibile immettere HTTP o HTTPS.

3. Nella pagina dell'applicazione di ADD appena creata, andare alla scheda **Configura**: ![][15]
4. Nella sezione **chiavi**, utilizzare l'elenco di riepilogo a discesa per selezionare una durata. Si noti che la chiave viene visualizzata dopo aver selezionato **Salva**: ![][16]
5. In **single sign-on**, aggiungere ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` come **URL di risposta**.
6. In **Autorizzazioni per altre applicazioni**:  

	1. Selezionare **Aggiungi applicazione**. Nella finestra a comparsa, scegliere l'applicazione di ADD che protegge il back-end esistente: ![][17]  

	2. Usare l'elenco a discesa per aggiungere le autorizzazioni: ![][18]

7. Selezionare **Salva** nella parte inferiore.
8. Copiare l’**ID client** e la **chiave** e archiviarli. La chiave non viene visualizzata nuovamente dopo aver chiuso il portale di Azure. 

Vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/active-directory-integrating-applications.md) per ulteriori informazioni sulle applicazioni di ADD.

## Passaggio 2: Configurare l'API con Azure PowerShell

A questo punto, non vi è alcun supporto del portale di Azure per inizializzare la configurazione necessaria per l'API. Per configurare l'API nel portale di Azure, utilizzare il seguente script di Auzre PowerShell:

> [AZURE.TIP] Per informazioni su come installare, configurare ed eseguire Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][11]. Lo script seguente funziona con l’anteprima di Azure PowerShell 1.0 o versione successiva.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Si noti** che il nome del parametro di connessione del **token** è importante. È possibile selezionare il proprio nome, purché contenga maiuscole e minuscole. Si utilizzerà il nome specificato in un secondo momento nel codice del back-end o nel criterio API.

Successivamente, accedere al [portale di Azure][19] e passare al pannello delle impostazioni **Generali** dell'API. Vengono visualizzate le ulteriori opzioni di configurazione: ![][21]


## Provare il servizio

Aprire un'app in PowerApps. In **connessioni disponibili** è elencata la nuova API. Quando si seleziona **Connetti**, viene visualizzata una finestra di accesso ad ADD. Immettere i dettagli dell'account di Azure ADD dell'organizzazione e la connessione viene creata.

A questo punto quando una chiamata di runtime viene effettuata dall'app all’API tramite questa connessione, il back-end riceve il token di ADD dell'utente nell’intestazione HTTP **x-ms-apim-token** nel seguente formato [con codifica Base64][20]\:

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Si noti** che il nome della proprietà **token** corrisponde al nome del parametro di connessione utilizzato quando si configura l'impostazione.

Il codice di back-end può quindi ottenere il token di ADD dalla proprietà **AccessToken** e utilizzarlo, se necessario. L'ambiente del servizio app consente di aggiornare automaticamente il token.

## Configurare il criterio API

Facoltativamente, è possibile utilizzare il criterio API per impostare il token di ADD nell’intestazione standard HTTP **Autorizzazione**. In questo modo, se il codice di back-end deve usare il token di ADD, è possibile ottenerlo in modo standard, piuttosto che cercando in un'intestazione HTTP personalizzata ed eseguire la decodifica Base64. A tale scopo, passare al portale di Azure, andare al pannello **Criterio** dell'API e impostare il criterio seguente:

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Esaminando questo criterio, esso essenzialmente consente di fare riferimento ai valori nell’intestazione **x-ms-apim-token** come JObject decodificato mediante una variabile **token**. È quindi possibile utilizzare il criterio **set-header** per ottenere il token di ADD effettivo e impostarlo nell’intestazione **Autorizzazione**. Questo è lo stesso criterio utilizzato da [Gestione API di Azure](https://azure.microsoft.com/services/api-management/). Per ulteriori informazioni, vedere [Criteri in Gestione API di Azure](../api-management/api-management-howto-policies.md).

**Si noti** che il nome della proprietà **token** corrisponde al nome del parametro di connessione utilizzato quando si configura l'impostazione.

## Riepilogo e passaggi successivi

In questo argomento, è stato possibile esaminare come configurare un'API per la connessione (e l'autenticazione) a una risorsa back-end in un dominio di Azure Active Directory. Per altre informazioni sulle PowerApps, vedere gli articoli e le risorse correlati seguenti.

- [Sviluppare un'API per PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_0309_2016-->