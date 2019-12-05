---
title: Guida alla limitazione delle richieste per Azure Key Vault
description: La limitazione delle richieste di Key Vault consente di limitare il numero di chiamate simultanee per evitare l'uso eccessivo delle risorse.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806678"
---
# <a name="azure-key-vault-throttling-guidance"></a>Guida alla limitazione delle richieste per Azure Key Vault

La limitazione delle richieste è un processo che consente di limitare il numero di chiamate simultanee al servizio di Azure per prevenire l'uso eccessivo delle risorse. Azure Key Vault è progettato per gestire un volume di richieste elevato. In caso di un numero eccessivamente elevato di richieste, la limitazione delle richieste del client aiuta a mantenere le prestazioni ottimali e l'affidabilità del servizio di Azure Key Vault.

I limiti delle richieste variano in base allo scenario. Ad esempio, se si esegue un volume elevato di operazioni di scrittura, la possibilità di limitare le richieste è maggiore rispetto a quando si eseguono solo operazioni di lettura.

## <a name="how-does-key-vault-handle-its-limits"></a>Gestione dei limiti da parte di Key Vault

I limiti del servizio in Key Vault impedire l'utilizzo improprio delle risorse e garantire la qualità del servizio per tutti i client di Key Vault. Quando viene superata una soglia di servizio, Key Vault limita le altre richieste da tale client per un determinato periodo di tempo, restituisce il codice di stato HTTP 429 (troppe richieste) e la richiesta ha esito negativo. Richieste non riuscite che restituiscono un conteggio di 429 per i limiti di limitazione rilevati da Key Vault. 

Key Vault è stato originariamente progettato per essere usato per archiviare e recuperare i segreti in fase di distribuzione.  Il mondo si è evoluto e Key Vault viene usato in fase di esecuzione per archiviare e recuperare i segreti e spesso le app e i servizi vogliono usare Key Vault come un database.  I limiti correnti non supportano velocità effettiva elevata.

Key Vault è stato creato in origine con i limiti specificati nei [limiti di Azure Key Vault servizio](key-vault-service-limits.md).  Per ottimizzare la Key Vault tramite le tariffe put, di seguito sono riportate alcune linee guida consigliate e procedure consigliate per massimizzare la velocità effettiva:
1. Assicurarsi di aver installato la limitazione delle richieste.  Il client deve rispettare i criteri di disattivazione esponenziale per 429 e assicurarsi di eseguire nuovi tentativi in base alle indicazioni riportate di seguito.
1. Dividere il traffico Key Vault tra più insiemi di credenziali e aree diverse.   Usare un insieme di credenziali separato per ogni dominio di sicurezza/disponibilità.   Se si dispone di cinque app, ciascuna in due aree, si consiglia di utilizzare 10 insiemi di credenziali, ognuno dei quali contiene i segreti esclusivi dell'app e dell'area.  Un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte il limite del singolo insieme di credenziali delle chiavi. Ad esempio, le transazioni HSM-other per ogni sottoscrizione sono limitate a 5.000 transazioni in 10 secondi per sottoscrizione. Si consiglia di memorizzare nella cache il segreto all'interno del servizio o dell'app per ridurre anche il RPS direttamente all'insieme di credenziali delle chiavi e/o gestire il traffico basato su picchi.  È anche possibile dividere il traffico tra aree diverse per ridurre al minimo la latenza e usare una sottoscrizione o un insieme di credenziali diverso.  Non inviare più del limite di sottoscrizione al servizio Key Vault in una singola area di Azure.
1. Memorizzare nella cache i segreti recuperati dal Azure Key Vault in memoria e riutilizzare la memoria quando possibile.  Ripetere la lettura da Azure Key Vault solo quando la copia memorizzata nella cache smette di funzionare, ad esempio perché è stata ruotata nell'origine. 
1. Key Vault è progettato per i segreti dei propri servizi.   Se si archiviano i segreti dei clienti (soprattutto per gli scenari di archiviazione chiavi a velocità effettiva elevata), è consigliabile inserire le chiavi in un account di archiviazione o database con crittografia e archiviare solo la chiave master in Azure Key Vault.
1. Le operazioni di crittografia, wrapping e verifica della chiave pubblica possono essere eseguite senza l'accesso alle Key Vault, che non solo riduce il rischio di limitazione, ma migliora anche l'affidabilità (purché il materiale della chiave pubblica venga memorizzato correttamente nella cache).
1. Se si usa Key Vault per archiviare le credenziali per un servizio, controllare se il servizio supporta l'autenticazione AAD per l'autenticazione diretta. In questo modo si riduce il carico su Key Vault, aumenta l'affidabilità e si semplifica il codice perché Key Vault ora può usare il token di AAD.  Molti servizi sono passati all'uso dell'autenticazione di AAD.  Vedere l'elenco corrente in [servizi che supportano identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Valutare la possibilità di scaglionare il carico o la distribuzione in un periodo di tempo più lungo per restare sotto i limiti correnti di RPS.
1. Se l'app è costituita da più nodi che devono leggere gli stessi segreti, provare a usare un modello di fan out, in cui un'entità legge il segreto da Key Vault e i fan a tutti i nodi.   Memorizza nella cache i segreti recuperati solo in memoria.
Se si ritiene che il precedente non soddisfi le proprie esigenze, compilare la tabella seguente e contattarci per determinare quale capacità aggiuntiva è possibile aggiungere (ad esempio, inserire di seguito solo a scopo illustrativo).

| Nome dell'insieme di credenziali | Area dell'insieme di credenziali | Tipo di oggetto (segreto, chiave o certificato) | Operazioni * | Tipo chiave | Lunghezza o curva della chiave | Chiave HSM?| RPS stato stabile necessario | Richieste RPS di picco |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chiave | Sign | EC | P-256 | No | 200 | 1000 |

\* per un elenco completo dei valori possibili, vedere [operazioni Azure Key Vault](/rest/api/keyvault/key-operations).

Se è stata approvata una capacità aggiuntiva, tenere presente quanto segue in seguito all'aumento della capacità:
1. Il modello di coerenza dei dati cambia. Quando un insieme di credenziali è consentito nell'elenco con capacità di velocità effettiva aggiuntiva, la coerenza dei dati del servizio Key Vault garantisce modifiche (necessario per soddisfare un volume RPS superiore perché il servizio di archiviazione di Azure sottostante non può rimanere attivo).  In pratica
  1. **Senza Allow Listing**: il servizio Key Vault rifletterà i risultati di un'operazione di scrittura (ad esempio, Secrett, CreateKey) immediatamente nelle chiamate successive, ad esempio SecretGet, segno di firma.
  1. **Con Allow Listing**: il servizio Key Vault rifletterà i risultati di un'operazione di scrittura (ad esempio, Secrett, CreateKey) entro 60 secondi nelle chiamate successive, ad esempio SecretGet, segno di firma.
1. Il codice client deve rispettare i criteri di backup per 429 tentativi. Il codice client che chiama il servizio Key Vault non deve ritentare immediatamente Key Vault richieste quando riceve un codice di risposta 429.  Il Azure Key Vault linee guida per la limitazione delle richieste pubblicate qui consiglia di applicare backoff esponenziali quando si riceve un codice di risposta http 429.

Nel caso l'utente abbia delle esigenze aziendali per cui sono necessarie delle limitazioni maggiori, contattare Microsoft.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Come limitare le richieste per l'app in risposta ai limiti del servizio

Di seguito sono riportate le **procedure** consigliate da implementare quando il servizio è limitato:
- Ridurre il numero di operazioni per ogni richiesta.
- Ridurre la frequenza delle richieste.
- Evitare tentativi immediati. 
    - Tutte le richieste si accumulano per i limiti di consumo.

Quando si implementa la gestione degli errori dell'app, usare il codice di errore HTTP 429 per rilevare la necessità di limitazione delle richieste lato client. Se la richiesta ha di nuovo esito negativo con un codice di errore HTTP 429, vuol dire che ci sono ancora limiti nel servizio di Azure. Continuare a usare il metodo di limitazione delle richieste lato client consigliato, provando a inviare nuovamente la richiesta fino a quando non ha esito positivo.

Di seguito è riportato il codice che implementa il backoff esponenziale. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


L'uso di questo codice in C# un'applicazione client è semplice. 

### <a name="recommended-client-side-throttling-method"></a>Metodo consigliato per la limitazione delle richieste lato client

Quando si genera il codice di errore HTTP 429, iniziare a limitare le richieste del client con un approccio di backoff esponenziale:

1. Attendere 1 secondo e ripetere la richiesta
2. Se viene ancora limitata, attendere 2 secondi e ripetere la richiesta
3. Se viene ancora limitata, attendere 4 secondi e ripetere la richiesta
4. Se viene ancora limitata, attendere 8 secondi e ripetere la richiesta
5. Se viene ancora limitata, attendere 16 secondi e ripetere la richiesta

A questo punto, il codice di risposta HTTP 429 dovrebbe non essere più visualizzato.

## <a name="see-also"></a>Vedi anche

Per un approfondimento sulla limitazione delle richieste nel cloud di Microsoft, vedere [Throttling Pattern](https://docs.microsoft.com/azure/architecture/patterns/throttling) (Modello di limitazione delle richieste).

