---
title: Usare l'identità gestita con un'applicazioneUse managed identity with an application
description: Come usare le identità gestite nel codice dell'applicazione di Azure Service Fabric per accedere ai servizi di Azure.How to use managed identities in Azure Service Fabric application code to access Azure Services. Questa funzionalità è disponibile in anteprima pubblica.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 59680ec7911f55c3dc49d8834b410a039aa435dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610319"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Come sfruttare l'identità gestita di un'applicazione Service Fabric per accedere ai servizi di Azure (anteprima)How to leverage a Service Fabric application's managed identity to access Azure services (preview)

Le applicazioni Service Fabric possono sfruttare le identità gestite per accedere ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory.Service Fabric applications can leverage managed identities to access other Azure resources which support Azure Active Directory-based authentication. Un'applicazione può ottenere un token di [accesso](../active-directory/develop/developer-glossary.md#access-token) che rappresenta la propria identità, che può essere assegnato dal sistema o dall'utente, e utilizzarlo come token 'portatore' per autenticarsi in un altro servizio, noto anche come [server di risorse protette.](../active-directory/develop/developer-glossary.md#resource-server) Il token rappresenta l'identità assegnata all'applicazione Service Fabric e verrà rilasciato solo alle risorse di Azure (incluse le applicazioni SF) che condividono tale identità. Fare riferimento alla documentazione relativa alla panoramica delle [identità gestite](../active-directory/managed-identities-azure-resources/overview.md) per una descrizione dettagliata delle identità gestite, nonché alla distinzione tra identità assegnate dal sistema e identità assegnate dall'utente. Si farà riferimento a un'applicazione Service Fabric abilitata per l'identità gestita come [applicazione client](../active-directory/develop/developer-glossary.md#client-application) in questo articolo.

> [!IMPORTANT]
> Un'identità gestita rappresenta l'associazione tra una risorsa di Azure e un'entità servizio nel tenant di Azure AD corrispondente associato alla sottoscrizione. Di conseguenza, nel contesto di Service Fabric, le identità gestite sono supportate solo per le applicazioni distribuite come risorse di Azure.As such, in the context of Service Fabric, managed identities are only supported for applications deployed as Azure resources. 

> [!IMPORTANT]
> Prima di usare l'identità gestita di un'applicazione Service Fabric, all'applicazione client deve essere concesso l'accesso alla risorsa protetta. Fare riferimento all'elenco dei servizi di [Azure che supportano l'autenticazione](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) di Azure AD per verificare il supporto e quindi alla documentazione del rispettivo servizio per passaggi specifici per concedere a un'identità l'accesso alle risorse di interesse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Acquisizione di un token di accesso tramite l'API RESTAcquiring an access token using REST API
Nei cluster abilitati per l'identità gestita, il runtime di Service Fabric espone un endpoint localhost che le applicazioni possono utilizzare per ottenere i token di accesso. L'endpoint è disponibile in ogni nodo del cluster ed è accessibile a tutte le entità in tale nodo. I chiamanti autorizzati possono ottenere token di accesso chiamando questo endpoint e presentando un codice di autenticazione; il codice viene generato dal runtime di Service Fabric per ogni attivazione del pacchetto del codice del servizio distinto ed è associato alla durata del processo che ospita il pacchetto del codice del servizio.

In particolare, verrà eseguito il seeding dell'ambiente di un servizio di Service Fabric abilitato per l'identità gestita con le variabili seguenti:Specifically, the environment of a managed-identity-enabled Service Fabric service will be seeded with the following variables:
- 'MSI_ENDPOINT': l'endpoint localhost, completo di percorso, versione dell'API e parametri corrispondenti all'identità gestita del servizio'MSI_ENDPOINT': the localhost endpoint, complete with path, API version, and parameters corresponding to that service's managed identity
- 'MSI_SECRET': un codice di autenticazione, che è una stringa opaca e rappresenta in modo univoco il servizio nel nodo corrente'MSI_SECRET': an authentication code, which is an opaque string and uniquely represents the service on the current node

> [!NOTE]
> I nomi 'MSI_ENDPOINT' e 'MSI_SECRET' si riferiscono alla designazione precedente delle identità gestite ("Identità del servizio gestito"), e che ora è deprecata. I nomi sono inoltre coerenti con i nomi delle variabili di ambiente equivalenti usati da altri servizi di Azure che supportano le identità gestite.

> [!IMPORTANT]
> Il codice dell'applicazione deve considerare il valore della variabile di ambiente 'MSI_SECRET' come dati sensibili, che non devono essere registrati o altrimenti diffusi. Il codice di autenticazione non ha alcun valore all'esterno del nodo locale o dopo che il processo che ospita il servizio è terminato, ma rappresenta l'identità del servizio Service Fabric e pertanto deve essere trattato con le stesse precauzioni del token di accesso stesso.

Per ottenere un token, il client esegue i passaggi seguenti:To obtain a token, the client performs the following steps:
- forma un URI concatenando l'endpoint di identità gestita (valore di MSI_ENDPOINT) con la versione dell'API e la risorsa (gruppo di destinatari) necessaria per il token
- crea una richiesta HTTP GET per l'URI specificato
- aggiunge il codice di autenticazione (valore MSI_SECRET) come intestazione alla richiesta
- invia la richiesta

Una risposta corretta conterrà un payload JSON che rappresenta il token di accesso risultante, nonché i metadati che lo descrivono. Una risposta non riuscita includerà anche una spiegazione dell'errore. Per ulteriori informazioni sulla gestione degli errori, vedere di seguito.

I token di accesso verranno memorizzati nella cache da Service Fabric a vari livelli (nodo, cluster, servizio del provider di risorse), pertanto una risposta corretta non implica necessariamente che il token sia stato emesso direttamente in risposta alla richiesta dell'applicazione utente. I token verranno memorizzati nella cache per meno della durata e pertanto è garantito che un'applicazione riceva un token valido. È consigliabile che il codice dell'applicazione memorizzi nella cache tutti i token di accesso acquisiti; la chiave di memorizzazione nella cache deve includere (una derivazione) il pubblico. 


Richiesta di esempio:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
dove:

| Elemento | Descrizione |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Endpoint di identità gestita per le applicazioni di Service Fabric, fornito tramite la variabile di ambiente MSI_ENDPOINT. |
| `api-version` | Un parametro della stringa di query che specifica la versione API del servizio token di identità gestita. attualmente l'unico `2019-07-01-preview`valore accettato è , ed è soggetto a modifiche. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Questo si rifletterà `aud` come l'attestazione (pubblico) del token emesso. Questo esempio richiede un token per accedere a Azure Key\/Vault, il cui URI ID app è https: /keyvault.azure.com/. |
| `Secret` | Un campo di intestazione della richiesta HTTP, richiesto dal servizio token di identità gestita di Service Fabric per i servizi di Service Fabric per autenticare il chiamante. Questo valore viene fornito dal runtime SF tramite MSI_SECRET variabile di ambiente. |


Risposta di esempio:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
dove:

| Elemento | Descrizione |
| ------- | ----------- |
| `token_type` | Il tipo di token; in questo caso, un token di accesso "Bearer", il che significa che il presentatore ('portatore') di questo token è l'oggetto previsto del token. |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `expires_on` | Timestamp della scadenza del token di accesso. rappresentato come il numero di secondi da "1970-01-01T0:0:0 UTC" e corrisponde all'attestazione del `exp` token. In questo caso, il token scade il 2019-08-08T06:10:11-00:00 (in RFC 3339)|
| `resource` | Risorsa per la quale è stato emesso il token di accesso, specificato tramite il `resource` parametro della stringa di query della richiesta. corrisponde all'affermazione "aud" del token. |


## <a name="acquiring-an-access-token-using-c"></a>Acquisizione di un token di accesso tramite CAcquiring an access token using C #
Quanto sopra diventa, in C:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Accesso all'insieme di credenziali delle chiavi da un'applicazione Di Service Fabric tramite l'identità gestitaAccessing Key Vault from a Service Fabric application using Managed Identity
Questo esempio si basa su quanto sopra per dimostrare l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi usando l'identità gestita.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Gestione degli errori
Il campo 'codice di stato' dell'intestazione della risposta HTTP indica lo stato della richiesta. uno stato '200 OK' indica l'esito positivo e la risposta includerà il token di accesso come descritto in precedenza. Di seguito è riportata una breve enumerazione delle possibili risposte di errore.

| Codice di stato | Motivo dell'errore | Come gestirlo |
| ----------- | ------------ | ------------- |
| 404 - Non trovato. | Codice di autenticazione sconosciuto o all'applicazione non è stata assegnata un'identità gestita. | Rettificare l'impostazione dell'applicazione o il codice di acquisizione di token. |
| 429 - Numero eccessivo di richieste. |  Raggiunto limite di limitazione, imposto da AAD o SF. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |
| Errore 4xx nella richiesta. | Uno o più dei parametri della richiesta non sono corretti. | Non riprovare.  Esaminare i dettagli dell'errore per maggiori informazioni.  Errori 4xx in fase di progettazione.|
| 5xx Errore dal servizio. | Il sottosistema di identità gestita o Azure Active Directory ha restituito un errore temporaneo. | È sicuro riprovare dopo un breve periodo di tempo. È possibile che venga raggiunta una condizione di limitazione delle richieste (429) al nuovo tentativo.|

Se si verifica un errore, il corpo della risposta HTTP corrispondente contiene un oggetto JSON con i dettagli dell'errore:If an error occurs, the corresponding HTTP response body contains a JSON object with the error details:

| Elemento | Descrizione |
| ------- | ----------- |
| codice | Codice di errore. |
| correlationId | ID di correlazione che può essere utilizzato per il debug. |
| message | Descrizione dettagliata dell'errore. **Le descrizioni degli errori possono cambiare in qualsiasi momento. Non dipendere dal messaggio di errore stesso.**|

Errore di esempio:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Di seguito è riportato un elenco di errori tipici di Service Fabric specifici per le identità gestite:Following is a list of typical Service Fabric errors specific to managed identities:

| Codice | Message | Descrizione | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Il segreto non viene trovato nelle intestazioni della richiesta. | Il codice di autenticazione non è stato fornito con la richiesta. | 
| ManagedIdentityNotFound | Identità gestita non trovata per l'host applicazioni specificato. | L'applicazione non ha identità o il codice di autenticazione è sconosciuto. |
| ArgomentoNullOrVuotoArgumentNullOrEmpty | Il parametro 'resource' non deve essere null o una stringa vuota. | La risorsa (gruppo di destinatari) non è stata fornita nella richiesta. |
| InvalidApiVersion | La versione api '' non è supportata. La versione supportata è '2019-07-01-preview'. | Versione API mancante o non supportata specificata nell'URI della richiesta. |
| InternalServerError | Si è verificato un errore. | Si è verificato un errore nel sottosistema di identità gestita, possibilmente all'esterno dello stack di Service Fabric. Molto probabilmente causa è un valore non corretto specificato per la risorsa (verificare la presenza di '/')più probabile'?) | 

## <a name="retry-guidance"></a>Materiale sussidiario sulla ripetizione di tentativi 

In genere l'unico codice di errore ritentabile è 429 (Troppe richieste); errori interni del server/5xx codici di errore possono essere ritentabili, anche se la causa può essere permanente. 

I limiti di limitazione delle richieste si applicano al numero di chiamate effettuate al sottosistema di identità gestite, in particolare alle dipendenze "upstream" (servizio Azure dell'identità gestita o al servizio token sicuro). Service Fabric memorizza nella cache i token a vari livelli della pipeline, ma data la natura distribuita dei componenti coinvolti, il chiamante potrebbe riscontrare risposte di limitazione incoerenti (ad esempio, ottenere limitato in un nodo/istanza di un'applicazione, ma non in un nodo diverso mentre si richiede un token per la stessa identità). Quando viene impostata la condizione di limitazione, le richieste successive dalla stessa applicazione potrebbero non riuscire con il codice di stato HTTP 429 (troppe richieste) fino a quando la condizione non viene cancellata.  

È consigliabile che le richieste non riuscite a causa della limitazione delle richieste vengano ritentate con un backoff esponenziale, come indicato di seguito:It is recommended that requests failed due to throttling are retried with an exponential backoff, as follows: 

| Indice chiamate | Azione alla ricezione 429 | 
| --- | --- | 
| 1 | Attendere 1 secondo e riprovare |
| 2 | Attendere 2 secondi e riprovare |
| 3 | Attendi 4 secondi e riprova |
| 4 | Attendere 8 secondi e riprovare |
| 4 | Attendere 8 secondi e riprovare |
| 5 | Attendi 16 secondi e riprova |

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure
Vedere Servizi di [Azure che supportano l'autenticazione](../active-directory/managed-identities-azure-resources/services-support-msi.md) di Azure AD per un elenco di risorse che supportano Azure AD e i rispettivi ID di risorsa.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dal sistemaDeploy an Azure Service Fabric application with a system-assigned managed identity](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Azure Service Fabric con un'identità gestita assegnata dall'utenteDeploy an Azure Service Fabric application with a user-assigned managed identity](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Concedere a un'applicazione di Azure Service Fabric l'accesso ad altre risorse di AzureGrant an Azure Service Fabric application access to other Azure resources](./how-to-grant-access-other-resources.md)