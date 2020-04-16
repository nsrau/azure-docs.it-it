---
title: Guida alla limitazione delle richieste per Azure Key Vault
description: La limitazione delle richieste di Key Vault consente di limitare il numero di chiamate simultanee per evitare l'uso eccessivo delle risorse.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432086"
---
# <a name="azure-key-vault-throttling-guidance"></a>Guida alla limitazione delle richieste per Azure Key Vault

La limitazione delle richieste è un processo che consente di limitare il numero di chiamate simultanee al servizio di Azure per prevenire l'uso eccessivo delle risorse. Azure Key Vault è progettato per gestire un volume di richieste elevato. In caso di un numero eccessivamente elevato di richieste, la limitazione delle richieste del client aiuta a mantenere le prestazioni ottimali e l'affidabilità del servizio di Azure Key Vault.

I limiti delle richieste variano in base allo scenario. Ad esempio, se si esegue un volume elevato di operazioni di scrittura, la possibilità di limitare le richieste è maggiore rispetto a quando si eseguono solo operazioni di lettura.

## <a name="how-does-key-vault-handle-its-limits"></a>Gestione dei limiti da parte di Key Vault

I limiti del servizio in Key Vault impediscono l'uso improprio delle risorse e garantiscono la qualità del servizio per tutti i client di Key Vault. Quando viene superata una soglia di servizio, Key Vault limita eventuali ulteriori richieste da tale client per un periodo di tempo, restituisce il codice di stato HTTP 429 (troppe richieste) e la richiesta ha esito negativo. Richieste non riuscite che restituiscono un conteggio di 429 per i limiti di limitazione rilevati dall'insieme di credenziali delle chiavi. 

Key Vault è stato originariamente progettato per essere utilizzato per archiviare e recuperare i segreti in fase di distribuzione.  Il mondo si è evoluto e Key Vault viene utilizzato in fase di esecuzione per archiviare e recuperare segreti e spesso le app e i servizi desiderano usare Key Vault come un database.  I limiti di corrente non supportano velocità di velocità effettiva elevate.

L'insieme di credenziali delle chiavi è stato originariamente creato con i limiti specificati nei [limiti del servizio dell'insieme](service-limits.md)di chiavi di Azure.  Per ottimizzare l'insieme di credenziali delle chiavi attraverso le velocità di utilizzo, ecco alcune linee guida/procedure consigliate per ottimizzare la velocità effettiva:To maximize your Key Vault through put rate, here are some recommended guidelines/best practices for maximizing your throughput:
1. Assicurarsi di avere la limitazione delle richieste in atto.  Il client deve rispettare i criteri di back-off esponenziali per 429 e assicurarsi di eseguire nuovi tentativi in base alle indicazioni riportate di seguito.
1. Dividere il traffico dell'insieme di credenziali delle chiavi tra più insiemi di credenziali e aree diverse.   Utilizzare un insieme di credenziali separato per ogni dominio di sicurezza/disponibilità.   Se hai cinque app, ognuna in due aree, ti consigliamo 10 vault ciascuno contenente i segreti unici per app e area geografica.  Un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte il limite dell'insieme di credenziali delle singole chiavi. Ad esempio, le altre transazioni HSM-per sottoscrizione sono limitate a 5.000 transazioni in 10 secondi per sottoscrizione. Prendi in considerazione la memorizzazione nella cache del segreto all'interno del tuo servizio o app per ridurre anche le richieste al secondo direttamente all'insieme di credenziali delle chiavi e/o gestire il traffico basato su burst.  È inoltre possibile dividere il traffico tra aree diverse per ridurre al minimo la latenza e utilizzare una sottoscrizione/insieme di credenziali diverso.  Non inviare più del limite di sottoscrizione al servizio Key Vault in una singola area di Azure.Do not send more than the subscription limit to the Key Vault service in a single Azure region.
1. Memorizzare nella cache i segreti recuperati dall'insieme di credenziali delle chiavi di Azure in memoria e riutilizzarli dalla memoria quando possibile.  Rieseguire la rilettura dall'insieme di credenziali delle chiavi di Azure solo quando la copia memorizzata nella cache smette di funzionare (ad esempio perché è stata ruotata all'origine). 
1. Key Vault è progettato per i tuoi segreti di servizi.   Se si archiviano i segreti dei clienti (soprattutto per scenari di archiviazione delle chiavi con velocità effettiva elevata), è consigliabile inserire le chiavi in un database o in un account di archiviazione con crittografia e archiviare solo la chiave master in Archiviazione chiavi di Azure.If you are storing your customers' secrets (especially for high-throughput key storage scenarios), consider putting the keys in a database or storage account with encryption, and storing just the master key in Azure Key Vault.
1. È possibile eseguire operazioni di crittografia, wrapping e verifica della chiave pubblica senza accesso all'insieme di credenziali delle chiavi, che non solo riduce il rischio di limitazione delle richieste, ma migliora anche l'affidabilità (a condizione che si memorizzi nella cache correttamente il materiale della chiave pubblica).
1. Se si usa l'insieme di credenziali delle chiavi per archiviare le credenziali per un servizio, verificare se tale servizio supporta l'autenticazione di Azure AD per l'autenticazione diretta. Ciò riduce il carico sull'insieme di credenziali delle chiavi, migliora l'affidabilità e semplifica il codice poiché L'insieme di credenziali delle chiavi può ora usare il token di Azure AD.  Molti servizi sono stati spostati nell'utilizzo dell'autenticazione di Azure AD.  Vedere l'elenco corrente in [Servizi che supportano le identità gestite per le risorse](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)di Azure .See the current list at Services that support managed identities for Azure resources .
1. Valutare la possibilità di scaglionare il carico/distribuzione per un periodo di tempo più lungo per rimanere al di sotto dei limiti RPS correnti.
1. Se l'app comprende più nodi che devono leggere gli stessi segreti, prendi in considerazione l'uso di un modello di fan out, in cui un'entità legge il segreto dall'insieme di credenziali delle chiavi e i fan in tutti i nodi.   Memorizzare nella cache i segreti recuperati solo in memoria.
Se si scopre che quanto sopra ancora non soddisfa le vostre esigenze, si prega di compilare la tabella qui sotto e contattarci per determinare quale capacità aggiuntiva può essere aggiunto (ad esempio messo sotto solo per scopi illustrativi).

| Nome dell'insieme di credenziali | Regione Vault | Tipo di oggetto (Segreto, Chiave o Certificato) | Funzionamento(i) | Tipo chiave | Lunghezza chiave o curva | Tasto HSM?| RPS stato stabile necessario | RPS di picco necessari |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chiave | Sign | EC | P-256 | No | 200 | 1000 |

\*Per un elenco completo dei valori possibili, vedere [Operazioni dell'insieme di credenziali delle chiavi](/rest/api/keyvault/key-operations)di Azure.For a full list of possible values, see Azure Key Vault operations .

Se la capacità aggiuntiva viene approvata, tenere presente quanto segue a seguito degli aumenti di capacità:
1. Modifiche al modello di coerenza dei dati. Quando un insieme di credenziali è consentito elencato con ulteriore capacità di velocità effettiva, le modifiche di garanzia della coerenza dei dati del servizio key Vault (necessarie per soddisfare richieste al secondo di volume più elevate poiché il servizio di archiviazione di Azure sottostante non è in grado di tenere il passo).  In pratica
  1. **Senza l'elenco consenti:** il servizio Key Vault rifletterà i risultati di un'operazione di scrittura (ad es. SecretSet, CreateKey) immediatamente nelle chiamate successive (ad es. SecretGet, KeySign).
  1. **Con consenti elenco**: Il servizio Key Vault rifletterà i risultati di un'operazione di scrittura (ad es. SecretSet, CreateKey) entro 60 secondi nelle chiamate successive (ad es. SecretGet, KeySign).
1. Il codice client deve rispettare i criteri di back-off per 429 tentativi. Il codice client che chiama il servizio Key Vault non deve ripetere immediatamente le richieste dell'insieme di credenziali delle chiavi quando riceve un codice di risposta 429.The client code calling the Key Vault service must not immediately retry Key Vault requests when it receives a 429 response code.  Le linee guida per la limitazione delle richieste di Azure Key Vault pubblicate qui consigliano di applicare il backoff esponenziale quando si riceve un codice di risposta Http 429.The Azure Key Vault throttling guidance published here recommends applying exponential backoff when receiving a 429 Http response code.

Nel caso l'utente abbia delle esigenze aziendali per cui sono necessarie delle limitazioni maggiori, contattare Microsoft.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Come limitare le richieste per l'app in risposta ai limiti del servizio

Di seguito sono **riportate le procedure consigliate** da implementare quando il servizio viene limitato:The following are best practices you should implement when your service is throttled:
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


L'utilizzo di questo codice in un'applicazione client in Cè è semplice. 

### <a name="recommended-client-side-throttling-method"></a>Metodo consigliato per la limitazione delle richieste lato client

Quando si genera il codice di errore HTTP 429, iniziare a limitare le richieste del client con un approccio di backoff esponenziale:

1. Attendere 1 secondo e ripetere la richiesta
2. Se viene ancora limitata, attendere 2 secondi e ripetere la richiesta
3. Se viene ancora limitata, attendere 4 secondi e ripetere la richiesta
4. Se viene ancora limitata, attendere 8 secondi e ripetere la richiesta
5. Se viene ancora limitata, attendere 16 secondi e ripetere la richiesta

A questo punto, il codice di risposta HTTP 429 dovrebbe non essere più visualizzato.

## <a name="see-also"></a>Vedere anche

Per un approfondimento sulla limitazione delle richieste nel cloud di Microsoft, vedere [Throttling Pattern](https://docs.microsoft.com/azure/architecture/patterns/throttling) (Modello di limitazione delle richieste).

