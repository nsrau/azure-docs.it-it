<properties
   pageTitle="Memorizzazione nella cache dei token di accesso in un'applicazione multi-tenant | Microsoft Azure"
   description="Memorizzazione nella cache dei token di accesso usati per richiamare un'API Web back-end"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>


# Memorizzazione nella cache dei token di accesso in un'applicazione multi-tenant

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

Ottenere un token di accesso OAuth è un'attività relativamente complessa, in quanto è necessaria una richiesta HTTP all'endpoint del token. È consigliabile quindi memorizzare nella cache i token, laddove possibile. [Azure AD Authentication Library][ADAL] (ADAL) memorizza automaticamente nella cache i token ottenuti da Azure AD, inclusi i token di aggiornamento.

ADAL fornisce un'implementazione predefinita della cache del token. Tuttavia, questa cache è progettata per le app client native e _non_ è adatta alle app Web:

-	Si tratta di un'istanza statica e non thread-safe.
-	Non supporta la scalabilità a un numero elevato di utenti, in quanto i token di tutti gli utenti vengono collocati nello stesso dizionario.
-	Non può essere condivisa tra i server Web in una farm.

In alternativa, è necessario implementare una cache di token personalizzata che deriva dalla classe `TokenCache` di ADAL, ma è adatta a un ambiente server e fornisce il livello di isolamento tra i token desiderato per utenti diversi.

La classe `TokenCache` archivia un dizionario di token, indicizzati da autorità di certificazione, risorse, ID client e utente. Una cache di token personalizzata deve scrivere questo dizionario in un archivio di backup, ad esempio una cache Redis.

Nell'applicazione Tailspin Surveys la classe `DistributedTokenCache` implementa la cache di token. Questa implementazione usa l'astrazione [IDistributedCache][distributed-cache] da ASP.NET Core 1.0. In questo modo, qualsiasi implementazione di `IDistributedCache` può essere usata come archivio di backup.

-	Per impostazione predefinita, l'app Surveys usa una cache Redis.
-	Per un server Web a istanza singola, è possibile usare la [cache in memoria][in-memory-cache] di ASP.NET Core 1.0. Questa opzione risulta valida anche per l'esecuzione locale dell'app durante lo sviluppo.

> [AZURE.NOTE] La cache Redis non è attualmente supportata per .NET Core.

`DistributedTokenCache` archivia i dati della cache come coppie chiave/valore nell'archivio di backup. La chiave è costituita dall'ID utente e dall'ID client, pertanto l'archivio di backup contiene dati di cache separati per ogni combinazione univoca di utente/client.

![Cache del token](media/guidance-multitenant-identity/token-cache.png)

L'archivio di backup viene partizionato dall'utente. Per ogni richiesta HTTP, i token per tale utente vengono letti dall'archivio di backup e caricati nel dizionario `TokenCache`. Se Redis viene usato come archivio di backup, ogni istanza del server in una server farm legge o scrive nella cache stessa. Questo approccio è adatto a molti utenti.

## Crittografia dei token memorizzati nella cache

I token sono dati sensibili in quanto concedono l'accesso alle risorse dell'utente. Inoltre, a differenza di una password dell'utente, non è possibile semplicemente archiviare un hash del token è quindi fondamentale proteggere i token da eventuali eventi che possano danneggiarli. La cache Redis sottoposta a backup è protetta da password, ma se un utente la ottiene, può accedere a tutti i token di accesso memorizzati nella cache. Per questo motivo, `DistributedTokenCache` crittografa tutto ciò che viene scritto nell'archivio di backup. La crittografia viene eseguita usando le API di [protezione dei dati][data-protection] di ASP.NET Core 1.0.

> [AZURE.NOTE] Se si esegue la distribuzione a Siti Web di Azure, le chiavi di crittografia vengono sottoposte a backup nell'archiviazione di rete e sincronizzate tra tutti i computer. Vedere l'argomento relativo alla [gestione delle chiavi][key-management]. Per impostazione predefinita, le chiavi non vengono crittografate durante l'esecuzione di Siti Web di Azure, ma è possibile [abilitare la crittografia usando un certificato X.509][x509-cert-encryption].


## Implementazione di DistributedTokenCache

La classe [DistributedTokenCache][DistributedTokenCache] deriva dalla classe [TokenCache][tokencache-class] di ADAL.

Nel costruttore, la classe `DistributedTokenCache` crea una chiave per l'utente corrente e carica la cache dall'archivio di backup:

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

La chiave viene creata concatenando l'ID utente e l'ID client, entrambi ricavati dalle attestazioni presenti nell'oggetto `ClaimsPrincipal` dell'utente:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

Per caricare i dati della cache, leggere il BLOB serializzato dall'archivio di backup, e chiamare `TokenCache.Deserialize` per convertire il BLOB nei dati della cache.

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

Ogni volta che ADAL accede alla cache, genera un evento `AfterAccess`. Se i dati della cache vengono modificati, la proprietà `HasStateChanged` è true. In tal caso, aggiornare l'archivio di backup in modo che rifletta la modifica e quindi impostare `HasStateChanged` su false.

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache invia altri due eventi:

- `BeforeWrite`. Chiamato immediatamente prima che ADAL scriva nella cache. Ciò consente di implementare una strategia di concorrenza.
- `BeforeAccess`. Chiamato immediatamente prima che ADAL legga dalla cache. In questo caso, è possibile ricaricare la cache per ottenere la versione più recente.

In questo esempio specifico, è stato ritenuto opportuno non gestire questi due eventi.

- Per la concorrenza, prevale la scrittura più recente. Questa opzione è accettabile, perché i token vengono archiviati in modo indipendente per ogni utente e per ogni client, pertanto si verificherà un conflitto solo se lo stesso utente ha due sessioni di accesso simultanee.
- Per la lettura, la cache viene caricata per ogni richiesta. Le richieste sono di breve durata. Se la cache viene modificata in un determinato momento, la richiesta successiva rileverà il nuovo valore.

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[parte di una serie]: guidance-multitenant-identity.md
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->