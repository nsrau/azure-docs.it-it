---
title: 'Service Fabric di Azure: usare identità gestite con Service Fabric applicazioni | Microsoft Docs'
description: Come usare le identità gestite dal codice dell'applicazione Service Fabric
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 528e1b0a353cdcd716f9bca63c423af7a6f12641
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958239"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Come sfruttare un'identità gestita dell'applicazione Service Fabric per accedere ai servizi di Azure

Service Fabric applicazioni possono sfruttare le identità gestite per accedere ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory. Un'applicazione può ottenere un [token di accesso](../active-directory/develop/developer-glossary.md#access-token) che rappresenta l'identità, che può essere assegnata dal sistema o assegnata dall'utente e usarla come token di connessione per autenticarsi a un altro servizio, noto anche come [server di risorse protetto](../active-directory/develop/developer-glossary.md#resource-server). Il token rappresenta l'identità assegnata all'applicazione Service Fabric e verrà emesso solo per le risorse di Azure (incluse le applicazioni SF) che condividono tale identità. Per una descrizione dettagliata delle identità gestite, vedere la documentazione relativa alla [Panoramica dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) , nonché la distinzione tra identità assegnate dal sistema e identità assegnate dall'utente. Si fa riferimento a un'applicazione Service Fabric abilitata per l'identità gestita come [applicazione client](../active-directory/develop/developer-glossary.md#client-application) in questo articolo.

> [!IMPORTANT]
> Un'identità gestita rappresenta l'associazione tra una risorsa di Azure e un'entità servizio nel tenant di Azure AD corrispondente associato alla sottoscrizione che contiene la risorsa. Di conseguenza, nel contesto di Service Fabric, le identità gestite sono supportate solo per le applicazioni distribuite come risorse di Azure. 

> [!IMPORTANT]
> Prima di usare l'identità gestita di un'applicazione Service Fabric, è necessario concedere all'applicazione client l'accesso alla risorsa protetta. Vedere l'elenco dei servizi di [Azure che supportano l'autenticazione Azure ad](/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) per verificare il supporto e quindi la documentazione del rispettivo servizio per i passaggi specifici per concedere a un'identità l'accesso alle risorse di interesse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Acquisizione di un token di accesso tramite l'API REST
Nei cluster abilitati per l'identità gestita, il runtime di Service Fabric espone un endpoint localhost che le applicazioni possono usare per ottenere i token di accesso. L'endpoint è disponibile in ogni nodo del cluster ed è accessibile a tutte le entità in tale nodo. I chiamanti autorizzati possono ottenere i token di accesso chiamando questo endpoint e presentando un codice di autenticazione; il codice viene generato dal Runtime Service Fabric per ogni attivazione del pacchetto di codice del servizio distinto ed è associato alla durata del processo che ospita il pacchetto di codice del servizio.

In particolare, l'ambiente di un servizio di Service Fabric abilitato per l'identità gestita viene sottoposta a seeding con le variabili seguenti:
- ' MSI_ENDPOINT ': endpoint localhost, completo con percorso, versione API e parametri corrispondenti all'identità gestita di tale servizio
- ' MSI_SECRET ': codice di autenticazione, ovvero una stringa opaca che rappresenta in modo univoco il servizio nel nodo corrente

> [!NOTE]
> I nomi ' MSI_ENDPOINT ' è MSI_SECRET ' fanno riferimento alla precedente designazione delle identità gestite ("identità del servizio gestita"), che ora è deprecata. I nomi sono anche coerenti con i nomi delle variabili di ambiente equivalenti usati da altri servizi di Azure che supportano le identità gestite.

> [!IMPORTANT]
> Il codice dell'applicazione deve considerare il valore della variabile di ambiente ' MSI_SECRET ' come dati sensibili. non deve essere registrato o distribuito in altro modo. Il codice di autenticazione non ha alcun valore all'esterno del nodo locale o dopo che il processo che ospita il servizio è stato terminato, ma rappresenta l'identità del servizio Service Fabric e pertanto deve essere trattato con le stesse precauzioni del token di accesso stesso.

Per ottenere un token, il client esegue i passaggi seguenti:
- forma un URI concatenando l'endpoint di identità gestita (valore MSI_ENDPOINT) con la versione dell'API e la risorsa (audience) richiesta per il token.
- Crea una richiesta HTTP GET per l'URI specificato
- aggiunge il codice di autenticazione (valore MSI_SECRET) come intestazione alla richiesta.
- Invia la richiesta

Una risposta con esito positivo conterrà un payload JSON che rappresenta il token di accesso risultante, oltre ai metadati che lo descrivono. Una risposta non riuscita includerà anche una spiegazione dell'errore. Per ulteriori informazioni sulla gestione degli errori, vedere di seguito.

I token di accesso verranno memorizzati nella cache da Service Fabric a diversi livelli (nodo, cluster, servizio del provider di risorse), quindi una risposta con esito positivo non implica necessariamente che il token sia stato emesso direttamente in risposta alla richiesta dell'applicazione utente. I token verranno memorizzati nella cache per un periodo di validità inferiore rispetto alla loro durata, quindi è garantita la ricezione di un token valido per un'applicazione. È consigliabile che il codice dell'applicazione memorizza nella cache i token di accesso acquisiti; la chiave di memorizzazione nella cache deve includere, ovvero derivare, il gruppo di destinatari. 


Richiesta di esempio:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
dove:

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Endpoint di identità gestita per Service Fabric applicazioni, fornito tramite la variabile di ambiente MSI_ENDPOINT. |
| `api-version` | Parametro della stringa di query che specifica la versione dell'API del servizio token di identità gestito. Attualmente l'unico valore accettato è `2019-07-01-preview`ed è soggetto a modifiche. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Questo verrà riflesso come `aud` attestazione (audience) del token emesso. Questo esempio richiede un token per accedere a Azure Key Vault, il cui URI ID app https://keyvault.azure.com/ è. |
| `Secret` | Campo di intestazione della richiesta HTTP, richiesto dal servizio token di identità gestito Service Fabric per Service Fabric Services per l'autenticazione del chiamante. Questo valore viene fornito dal runtime SF tramite la variabile di ambiente MSI_SECRET. |


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
| `token_type` | Tipo di token. in questo caso, un token di accesso "Bearer", che significa che il presentatore (' Bearer ') di questo token è l'oggetto previsto del token. |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `expires_on` | Timestamp della scadenza del token di accesso. rappresentata come numero di secondi da "1970-01-01T0:0: 0Z UTC" e corrisponde all' `exp` attestazione del token. In questo caso, il token scade il 2019-08-08T06:10:11 + 00:00 (in RFC 3339)|
| `resource` | Risorsa per cui è stato emesso il token di accesso, specificata tramite `resource` il parametro della stringa di query della richiesta; corrisponde all'attestazione ' AUD ' del token. |


## <a name="acquiring-an-access-token-using-c"></a>Acquisizione di un token di accesso tramiteC#
Il precedente diventa C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Accesso a Key Vault da un'applicazione Service Fabric mediante identità gestita
Questo esempio si basa su quello precedente per illustrare l'accesso a un segreto archiviato in un Key Vault usando l'identità gestita.

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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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
```

## <a name="error-handling"></a>Gestione degli errori
Il campo ' status code ' dell'intestazione della risposta HTTP indica lo stato di esito positivo della richiesta. uno stato "200 OK" indica l'esito positivo e la risposta includerà il token di accesso come descritto in precedenza. Di seguito è riportata una breve enumerazione delle possibili risposte di errore.

| Codice di stato | Motivo dell'errore | Come gestirlo |
| ----------- | ------------ | ------------- |
| 404 - Non trovato. | Codice di autenticazione sconosciuto oppure all'applicazione non è stata assegnata un'identità gestita. | Correggere l'installazione dell'applicazione o il codice di acquisizione dei token. |
| 429 - Numero eccessivo di richieste. |  Limite di limitazione raggiunto, imposto da AAD o SF. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |
| Errore 4xx nella richiesta. | Uno o più dei parametri della richiesta non sono corretti. | Non riprovare.  Esaminare i dettagli dell'errore per maggiori informazioni.  Errori 4xx in fase di progettazione.|
| Errore 5xx dal servizio. | Il sottosistema di identità gestito o Azure Active Directory ha restituito un errore temporaneo. | È possibile riprovare dopo un breve periodo di tempo. È possibile che si verifichi una condizione di limitazione delle richieste (429) quando si esegue un nuovo tentativo.|

Se si verifica un errore, il corpo della risposta HTTP corrispondente contiene un oggetto JSON con i dettagli dell'errore:

| Elemento | Descrizione |
| ------- | ----------- |
| code | Codice di errore. |
| correlationId | ID di correlazione utilizzabile per il debug. |
| message | Descrizione dettagliata dell'errore. **Le descrizioni degli errori possono cambiare in qualsiasi momento: Non dipendono dal messaggio di errore stesso.**|

Errore di esempio:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Di seguito è riportato un elenco di errori di Service Fabric tipici specifici per le identità gestite:

| Codice | Messaggio | DESCRIZIONE | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Il segreto non è stato trovato nelle intestazioni della richiesta. | Il codice di autenticazione non è stato fornito con la richiesta. | 
| ManagedIdentityNotFound | Identità gestita non trovata per l'host applicazioni specificato. | L'applicazione non ha identità oppure il codice di autenticazione è sconosciuto. |
| ArgumentNullOrEmpty | Il parametro ' Resource ' non deve essere null o una stringa vuota. | La risorsa (audience) non è stata specificata nella richiesta. |
| InvalidApiVersion | La versione API '' non è supportata. La versione supportata è "2019-07-01-Preview". | Versione API mancante o non supportata specificata nell'URI della richiesta. |
| InternalServerError | Si è verificato un errore. | Si è verificato un errore nel sottosistema di identità gestito, possibilmente all'esterno dello stack Service Fabric. La causa più probabile è un valore non corretto specificato per la risorsa (verificare la presenza di '/' finali?) | 

## <a name="retry-guidance"></a>Materiale sussidiario sulla ripetizione di tentativi 

In genere, l'unico codice di errore irreversibile è 429 (troppe richieste); gli errori interni del server o i codici di errore 5xx possono essere ritentabili, anche se la causa potrebbe essere permanente. 

I limiti di limitazione si applicano al numero di chiamate effettuate al sottosistema di identità gestite, in particolare le dipendenze "upstream" (il servizio di identità gestito di Azure o il servizio token di sicurezza). Service Fabric memorizza nella cache i token a diversi livelli della pipeline, ma data la natura distribuita dei componenti implicati, il chiamante può riscontrare risposte di limitazione incoerenti (ad esempio, la limitazione di un nodo o un'istanza di un'applicazione, ma non di una nodo diverso durante la richiesta di un token per la stessa identità. Quando viene impostata la condizione di limitazione delle richieste, le richieste successive provenienti dalla stessa applicazione potrebbero avere esito negativo con il codice di stato HTTP 429 (troppe richieste) finché la condizione non viene cancellata.  

È consigliabile che le richieste non siano riuscite a causa della limitazione delle richieste con un backoff esponenziale, come indicato di seguito: 

| Indice delle chiamate | Azione di ricezione 429 | 
| --- | --- | 
| 1 | Attendere 1 secondo e riprovare |
| 2 | Attendere 2 secondi e riprovare |
| 3 | Attendere 4 secondi e riprovare |
| 4 | Attendere 8 secondi e riprovare |
| 4 | Attendere 8 secondi e riprovare |
| 5 | Attendere 16 secondi e riprovare |

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure
Vedere i [servizi di Azure che supportano l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-msi.md) per un elenco di risorse che supportano Azure ad e i rispettivi ID di risorsa.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)

## <a name="see-also"></a>Vedere anche

* Esaminare il [supporto di identità gestite](./concepts-managed-identity.md) in Azure Service Fabric

* [Distribuire un nuovo](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric cluster con supporto di identità gestite 

* [Abilitare l'identità gestita](./configure-existing-cluster-enable-managed-identity-token-service.md) in un cluster di Service Fabric Azure esistente