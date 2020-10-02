---
title: Informazioni sulle chiavi - Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per le chiavi.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 29930a835297b0ddd3a91534dab9ccb6d74896e3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967551"
---
# <a name="about-keys"></a>Informazioni sulle chiavi

Azure Key Vault offre due tipi di risorse per l'archiviazione e la gestione delle chiavi crittografiche:

|Tipo di risorsa|Metodi di protezione della chiave|URL di base dell'endpoint del piano dati|
|--|--|--|
| **Insiemi di credenziali** | Protetta da software<br/><br/>e<br/><br/>Con protezione HSM (con SKU Premium)</li></ul> | https://{nome-insiemecredenziali}.vault.azure.net |
| **Pool di moduli di protezione hardware gestiti** | Con protezione HSM | https://{nome-hsm}.managedhsm.azure.net |
||||

- **Insiemi di credenziali**: gli insiemi di credenziali offrono una soluzione di gestione delle chiavi a basso costo, facile da distribuire, multi-tenant, con resilienza della zona (dove disponibile) e a disponibilità elevata adatta per gli scenari di applicazioni cloud più comuni.
- **Moduli di protezione hardware gestiti**: i moduli di protezione hardware gestiti sono moduli a singolo tenant, con resilienza della zona (se disponibile) e a disponibilità elevata per l'archiviazione e la gestione delle chiavi crittografiche. Sono particolarmente adatti per le applicazioni e gli scenari di utilizzo che gestiscono chiavi con valore elevato. Soddisfano inoltre i requisiti di sicurezza, conformità e normativi più rigorosi. 

> [!NOTE]
> Gli insiemi di credenziali consentono anche di archiviare e gestire diversi tipi di oggetti, come segreti, certificati e chiavi dell'account di archiviazione, oltre alle chiavi crittografiche.

Le chiavi crittografiche in Key Vault sono rappresentate come oggetti JSON Web Key [JWK]. Le specifiche per JSON (JavaScript Object Notation) e JOSE (JavaScript Object Signing and Encryption) sono:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Le specifiche JWK/JWA di base vengono inoltre estese per abilitare tipi di chiave univoci per le implementazioni di Azure Key Vault e dei moduli di protezione hardware gestiti. 

Le chiavi con protezione HSM vengono elaborate in un modulo di protezione hardware e rimangono sempre entro i limiti di questa protezione. 

- Gli insiemi di credenziali usano moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 2** per proteggere le chiavi con protezione HSM nell'infrastruttura back-end HSM. 
- I pool di moduli di protezione hardware gestiti usano moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 3** per proteggere le chiavi. Ogni pool di moduli di protezione hardware è un'istanza a tenant singolo isolata con il proprio [dominio di sicurezza](../managed-hsm/security-domain.md) che fornisce l'isolamento crittografico completo da tutti gli altri pool che condividono la stessa infrastruttura hardware.

Queste chiavi sono protette nei pool di moduli di protezione hardware a tenant singolo. È possibile importare una chiave RSA, EC o simmetrica protetta tramite software o mediante l'esportazione da un modulo di protezione hardware supportato. È possibile anche generare chiavi nei pool di moduli di protezione hardware. Quando si importano le chiavi con protezione HSM usando il metodo descritto nella [specifica BYOK (Bring Your Own Key)](../keys/byok-specification.md), viene abilitato il materiale della chiave di trasporto sicuro nei pool di moduli di protezione hardware gestiti. 

Per ulteriori informazioni sui limiti geografici, vedere [Centro di protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-protection-methods-and-algorithms"></a>Tipi di chiave, metodi di protezione e algoritmi

Key Vault supporta le chiavi RSA, EC e simmetriche. 

### <a name="hsm-protected-keys"></a>Chiavi protette dal modulo di protezione hardware

|Tipo di chiave|Insiemi di credenziali (solo SKU Premium)|Pool di moduli di protezione hardware gestiti|
|--|--|--|--|
**EC-HSM**: chiave a curva ellittica|Modulo di protezione hardware FIPS 140-2 livello 2|Modulo di protezione hardware FIPS 140-2 livello 3
**RSA-HSM**: chiave RSA|Modulo di protezione hardware FIPS 140-2 livello 2|Modulo di protezione hardware FIPS 140-2 livello 3
**oct-HSM**: Simmetrica|Non supportato|Modulo di protezione hardware FIPS 140-2 livello 3
||||

### <a name="software-protected-keys"></a>Chiavi protette tramite software

|Tipo di chiave|Insiemi di credenziali|Pool di moduli di protezione hardware gestiti|
|--|--|--|--|
**RSA**: chiave RSA protetta tramite software|FIPS 140-2 livello 1|Non supportato
**EC**: chiave a curva ellittica protetta tramite software|FIPS 140-2 livello 1|Non supportato
||||

### <a name="supported-algorithms"></a>Algoritmi supportati

|Tipi di chiave/dimensioni/curve| Crittografa/Decrittografa<br>(Wrapping/Annullamento del wrapping) | Firma/Verifica | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|N/D|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES a 128 bit, 256 bit| AES-KW<br>AES-GCM<br>AES-CBC| N/D| 
|||

###  <a name="ec-algorithms"></a>Algoritmi EC
 Gli identificatori di algoritmo seguenti sono supportati con le chiavi EC-HSM

#### <a name="curve-types"></a>Tipi di curva

-   **P-256**: curva NIST P-256, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: curva SEC SECP256K1, definita in [SEC 2: Recommended Elliptic Curve Domain Parameters](https://www.secg.org/sec2-v2.pdf) (SEC 2: parametri di dominio EC consigliati).
-   **P-384**: curva NIST P-384, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: curva NIST P-521, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN//VERIFY

-   **ES256** - ECDSA per digest e chiavi SHA-256 creati con la curva P-256. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA per digest e chiavi SHA-256 creati con la curva P-256K. Questo algoritmo è in attesa di standardizzazione.
-   **ES384** - ECDSA per digest e chiavi SHA-384 creati con la curva P-384. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA per digest e chiavi SHA-512 creati con la curva P-521. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmi RSA  
 Gli identificatori di algoritmo seguenti sono supportati con le chiavi RSA e RSA-HSM  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>ESEGUI/NON ESEGUIRE IL WRAPPING DELLA CHIAVE, CRITTOGRAFA/DECRITTOGRAFA

-   **RSA1_5**: crittografia della chiave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP**: RSAES con OAEP (Optimal Asymmetric Encryption Padding) [RFC3447] con i parametri predefiniti specificati da RFC 3447 nella sezione A.2.1. Tali parametri predefiniti usano una funzione hash di SHA-1 e una funzione di generazione della maschera MGF1 con SHA-1.  
-  **RSA-OAEP-256**: RSAES con OAEP (Optimal Asymmetric Encryption Padding) con una funzione hash SHA-256 e una funzione di generazione della maschera di MGF1 con SHA-256

#### <a name="signverify"></a>SIGN//VERIFY

-   **PS256** - RSASSA-PSS tramite SHA-256 e MGF1 con SHA-256, come descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS tramite SHA-384 e MGF1 con SHA-384, come descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS tramite SHA-512 e MGF1 con SHA-512, come descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256**: RSASSA-PKCS-v1_5 tramite SHA-256. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-256 e deve avere una lunghezza di 32 byte.  
-   **RS384**: RSASSA-PKCS-v1_5 tramite SHA-384. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-384 e deve avere una lunghezza di 48 byte.  
-   **RS512**: RSASSA-PKCS-v1_5 tramite SHA-512. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-512 e deve avere una lunghezza di 64 byte.  
-   **RSNULL**: vedere [RFC2437](https://tools.ietf.org/html/rfc2437), un caso di utilizzo specializzato per abilitare determinati scenari TLS.  

###  <a name="symmetric-key-algorithms"></a>Algoritmi per chiavi simmetriche
- **AES-KW**: wrapping delle chiavi AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM**: crittografia AES in modalità contatore di Galois ([NIST SP800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC**: crittografia AES in modalità Cipher Block Chaining ([NIST SP800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> L'attuale implementazione di AES-GCM e le API corrispondenti sono in fase sperimentale. L'implementazione e le API potrebbero subire cambiamenti sostanziali nelle iterazioni future. 

##  <a name="key-operations"></a>Operazioni relative alle chiavi

Il modulo di protezione hardware gestito supporta le operazioni seguenti sugli oggetti chiave:  

-   **Creazione**: consente a un client di creare una chiave in Key Vault. Il valore della chiave viene generato da Key Vault e archiviato e non viene rilasciato al cliente. In Key Vault è possibile creare chiavi asimmetriche.  
-   **Import**: consente a un client di importare una chiave esistente in Key Vault. Le chiavi asimmetriche possono essere importate in Key Vault usando una serie di metodi di creazione di pacchetti diversi all'interno di un costrutto JWK. 
-   **Aggiornamento**: consente a un client con autorizzazioni sufficienti di modificare i metadati (attributi chiave) associati a una chiave archiviata in precedenza in Key Vault.  
-   **Elimina**: consente a un client con autorizzazioni sufficienti di eliminare una chiave da Key Vault.  
-   **Elenco**: consente a un client di elencare tutte le chiavi di un determinato Key Vault.  
-   **Elenco delle versioni**: consente a un client di elencare tutte le versioni di una chiave specifica in un determinato Key Vault.  
-   **Recupero**: consente a un client di recuperare le parti pubbliche di una determinata chiave in un Key Vault.  
-   **Backup**: esporta una chiave in un formato protetto.  
-   **Ripristino**: importa una chiave precedentemente sottoposta a backup.  

Per altre informazioni, vedere le [operazioni relative alle chiavi nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault).  

Dopo aver creato una chiave in Key Vault, sarà possibile eseguire le operazioni crittografiche seguenti usando tale chiave:  

-   **Firma e verifica**: in senso stretto, si tratta di una firma hash o di una verifica hash", poiché Key Vault non supporta l'hashing del contenuto durante la creazione della firma. Le applicazioni devono eseguire l'hashing dei dati per la firma locale e quindi richiedere a Key Vault la firma dell'hash. La verifica degli hash firmati è supportata come operazione utile per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per ottenere prestazioni ottimali delle applicazioni, è consigliabile eseguire le operazioni VERIFY in locale.  
-   **Crittografia tramite chiave/Wrapping**: una chiave archiviata in Key Vault può essere usata per proteggere un'altra chiave, in genere una chiave CEK (Content Encryption Key) simmetrica. Quando la chiave in Key Vault è asimmetrica, viene usata la crittografia della chiave. Ad esempio, RSA-OAEP e le operazioni WRAPKEY/UNWRAPKEY equivalgono a ENCRYPT/DECRYPT. Quando la chiave in Key Vault è simmetrica, viene usato il wrapping della chiave, ad esempio AES-KW. L'operazione WRAPKEY è supportata come strumento utile per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per ottenere prestazioni ottimali dalle applicazioni, le operazioni WRAPKEY devono essere eseguite in locale.  
-   **Crittografia e decrittografia**: una chiave archiviata in Key Vault può essere usata per crittografare o decrittografare un singolo blocco di dati. Le dimensioni del blocco sono determinate dal tipo di chiave e dall'algoritmo di crittografia selezionato. L'operazione Encrypt viene fornita per comodità per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per prestazioni ottimali delle applicazioni, è consigliabile eseguire le operazioni ENCRYPT in locale.  

Anche se WRAPKEY/UNWRAPKEY con chiavi asimmetriche possono sembrare superflue (in quanto equivalenti a ENCRYPT/DECRYPT), l'uso di operazioni distinte è importante. La distinzione assicura la separazione delle operazioni a livello di semantica e autorizzazioni e garantisce coerenza quando sono supportati altri tipi di chiave da parte del servizio.  

Key Vault non supporta le operazioni EXPORT. Una volta effettuato il provisioning di una chiave nel sistema, la chiave non può essere estratta e il relativo materiale non può essere modificato. Tuttavia, è possibile che gli utenti di Key Vault richiedano la chiave per altri casi d'uso, ad esempio dopo che è stata eliminata. In casi di questo tipo, è possibile usare le operazioni BACKUP e RESTORE per esportare o importare la chiave in un formato protetto. Le chiavi create dall'operazione BACKUP non possono essere usate all'esterno di Key Vault. In alternativa, l'operazione IMPORT può essere usata a fronte di più istanze di Key Vault.  

Gli utenti possono limitare le operazioni crittografiche supportate da Key Vault per ciascuna chiave usando la proprietà key_ops dell'oggetto JWK.  

Per ulteriori informazioni sugli oggetti JWK, vedere [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Attributi della chiave

Oltre al materiale della chiave,è possibile specificare gli attributi seguenti. In una richiesta JSON la parola chiave attributes e le parentesi graffe, '{' '}', sono obbligatorie anche se non sono stati specificati attributi.  

- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Specifica se la chiave è abilitata e utilizzabile per le operazioni di crittografia. L’attributo *abilitato* viene utilizzato in combinazione con *nbf* ed *exp*. Quando si verifica un'operazione tra *nbf* ed *exp*, questa verrà consentita solo se *abilitato* è impostato su **vero**. Le operazioni di fuori delle finestre *nbf* / *exp* non sono impostate automaticamente, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations).
- *nbf*: il valore predefinito IntDate facoltativo è "now". L’attributo *nbf* (non precedente) identifica l'ora prima della quale la chiave NON DEVE essere usata per le operazioni di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations). L'elaborazione dell’attributo *nbf* richiede che la data/ora corrente SIA successiva o uguale a data/ora non precedente elencata nell’attributo *nbf*. Key Vault PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  
- *exp*: il valore predefinito IntDate facoltativo è "forever". L’attributo *exp* (data di scadenza) identifica la data di scadenza in cui o precedentemente alla quale la chiave NON DEVE essere usata per l’operazione di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations). L'elaborazione dell’attributo *exp* richiede che la data/ora corrente SIA precedente a data/ora di scadenza elencata nell’attributo *exp*. Key Vault PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi chiave:  

- *created*: IntDate, facoltativo. L’attributo *creato* indica quando è stata creata questa versione della chiave. Il valore è null per le chiavi create prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  
- *updated*: IntDate, facoltativo. L’attributo *aggiornato* indica quando è stata aggiornata questa versione della chiave. Il valore è null per le chiavi che sono state aggiornate per l'ultima volta prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  

Per altre informazioni su IntDate e altri tipi di dati, vedere [Informazioni su chiavi, segreti e certificati: [Tipi di dati](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operazioni controllate in base a data e ora

Le chiavi non ancora valide e scadute, che non rientrano nella finestra *nbf* / *exp*, possono essere usate per le operazioni di **decrittografia**, **annullamento del wrapping** e **verifica** (non verrà restituito l'errore 403 Accesso negato). La logica per l'utilizzo di stato non ancora valido consiste nel consentire a una chiave di essere sottoposta a test prima dell'uso di produzione. La logica per l'utilizzo di stato scaduto consiste nel consentire operazioni di ripristino sui dati creati quando la chiave risultava valida. Inoltre, è possibile disabilitare l'accesso a una chiave usando i criteri di Key Vault, o aggiornando l’attributo chiave *abilitato* per **falso**.

Per altre informazioni sui tipi di dati, vedere [Tipi di dati](../general/about-keys-secrets-certificates.md#data-types).

Per altre informazioni su altri possibili attributi, vedere [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Tag della chiave

È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

> [!NOTE] 
> Un chiamante può leggere i tag se ha l'autorizzazione *list* o *get* per la chiave.

##  <a name="key-access-control"></a>Controllo di accesso per le chiavi

Controllo di accesso per le chiavi gestite da Key Vault fornito a livello di un Key Vault che funge da contenitore delle chiavi. I criteri di controllo di accesso per le chiavi sono distinti dai criteri di controllo di accesso per i segreti presenti nello stesso Key Vault. Gli utenti possono creare uno o più insiemi di credenziali per le chiavi e sono tenuti a mantenere una segmentazione e una gestione delle chiavi appropriate in base allo scenario. Il controllo degli accessi per le chiavi è indipendente dal controllo di accesso per i segreti.  

Le autorizzazioni seguenti possono essere concesse, su base principale utente/servizio, nella voce di controllo di accesso chiavi in un insieme di credenziali. Queste autorizzazioni rispecchiano fedelmente le operazioni consentite su un oggetto chiave:  La concessione dell'accesso a un'entità servizio nell'insieme di credenziali delle chiavi è un'operazione che viene eseguita una sola volta e l'entità servizio rimarrà la stessa per tutte le sottoscrizioni di Azure. È possibile usarla per distribuire tutti i certificati desiderati. 

- Autorizzazioni per le operazioni di gestione delle chiavi
  - *get*: consente di leggere la parte pubblica di una chiave e i relativi attributi
  - *list*: consente di elencare le chiavi o le versioni di una chiave archiviata in un insieme di credenziali delle chiavi
  - *update*: consente di aggiornare gli attributi per una chiave
  - *create*: consente di creare nuove chiavi
  - *import*: consente di importare una chiave in un insieme di credenziali delle chiavi
  - *delete*: consente di eliminare l'oggetto chiave
  - *recover*: consente di recuperare una chiave eliminata
  - *backup*: consente di eseguire il backup di una chiave in un insieme di credenziali delle chiavi
  - *restore*: consente di ripristinare una chiave precedentemente sottoposta a backup in un insieme di credenziali delle chiavi

- Autorizzazioni per le operazioni di crittografia
  - *decrypt*: consente di usare la chiave per annullare la protezione di una sequenza arbitraria di byte
  - *encrypt*: consente di usare la chiave per proteggere una sequenza arbitraria di byte
  - *unwrapKey*: consente di usare la chiave per annullare la protezione delle chiavi simmetriche di cui è stato eseguito il wrapping
  - *wrapKey*: consente di usare la chiave per proteggere una chiave simmetrica
  - *verify*: consente di usare la chiave per verificare digest del messaggio  
  - *sign*: consente di usare la chiave per firmare digest del messaggio
    
- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente una chiave

Per altre informazioni sull'uso delle chiavi, vedere le operazioni relative alle chiavi in [Key Vault REST API reference](/rest/api/keyvault) (Riferimenti sull'API REST di Key Vault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy). 

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni sul modulo di protezione hardware gestito](../managed-hsm/overview.md)
- [Informazioni sui segreti](../secrets/about-secrets.md)
- [Informazioni sui certificati](../certificates/about-certificates.md)
- [Panoramica dell'API REST di Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)