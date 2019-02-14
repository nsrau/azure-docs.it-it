---
title: Informazioni su chiavi, segreti e certificati di Azure Key Vault - Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per le chiavi, i segreti e i certificati.
services: key-vault
documentationcenter: ''
author: BryanLa
manager: barbkess
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 49879d36937a0f0d7ccf1a82cf8b6ca09453894d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106974"
---
# <a name="about-keys-secrets-and-certificates"></a>Informazioni su chiavi, segreti e certificati

Azure Key Vault consente agli utenti e alle applicazioni di Microsoft Azure di archiviare e usare diversi tipi di segreti e chiavi:

- Chiavi di crittografia. Supportano più tipi di chiavi e algoritmi e consentono di usare moduli di protezione hardware per le chiavi di valore elevato. 
- Segreti. Offre l'archiviazione sicura di segreti, ad esempio password e stringhe di connessione di database.
- Certificati. Supporta i certificati, basati su chiavi e segreti, e aggiunge una funzionalità di rinnovo automatico.
- Archiviazione di Azure. Può gestire le chiavi di un account di archiviazione di Azure per l'utente. Internamente, Key Vault può elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) tali chiavi con cadenza periodica. 

Per altre informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

Le sezioni seguenti forniscono informazioni generali applicabili all'intera implementazione del servizio Key Vault.

### <a name="supporting-standards"></a>Standard di supporto

Le specifiche di JavaScript Object Notation (JSON) e JavaScript Object Signing and Encryption (JOSE) costituiscono informazioni generiche importanti.  

-   [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Tipi di dati

Fare riferimento alle specifiche JOSE per i tipi di dati pertinenti per chiavi, crittografia e firma.  

-   **algoritmo**: un algoritmo supportato per un'operazione della chiave, ad esempio, RSA1_5  
-   **valore di testo crittografato**: ottetti di testo cifrati, codificati tramite Base64URL  
-   **valore di digest**: l'output di un algoritmo hash, codificato tramite Base64URL  
-   **key-type**: uno dei tipi di chiave supportati, ad esempio RSA (Rivest-Shamir-Adleman)  
-   **valore di testo non crittografato**: ottetti di testo non crittografato, codificati tramite Base64URL  
-   **valore di firma**: l'output di un algoritmo di firma, codificato tramite Base64URL  
-   **base64URL**: un valore binario Base64URL [RFC4648] codificato  
-   **booleano**: vero o falso  
-   **Identità**: un identità di Azure Active Directory (AAD).  
-   **IntDate** : un valore decimale JSON che rappresenta il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla data/ora UTC specificata. Per informazioni dettagliate sui valori data/ora in generale e UTC in particolare, vedere RFC3339.  

### <a name="objects-identifiers-and-versioning"></a>Oggetti, identificatori e controllo delle versioni

Agli oggetti archiviati in Key Vault viene applicato il controllo delle versioni ogni volta che si crea una nuova istanza di un oggetto. A ogni versione vengono assegnati un identificatore univoco e un URL. Un oggetto creato per la prima volta viene etichettato con un identificatore univoco della versione e contrassegnato come versione corrente dell'oggetto. La creazione di una nuova istanza con lo stesso nome assegna al nuovo oggetto un identificatore univoco della versione, trasformando tale oggetto nella versione corrente.  

È possibile fare riferimento agli oggetti di Key Vault usando l'identificatore corrente o un identificatore specifico della versione. Considerando ad esempio una chiave con nome `MasterKey`, l'esecuzione di operazioni con l'identificatore corrente fa sì che venga usata automaticamente la versione disponibile più recente. L'esecuzione di operazioni con l'identificatore specifico della versione fa sì che il sistema userà quella versione specifica dell'oggetto.  

All'interno di Key Vault gli oggetti sono identificati in modo univoco con un URL. Nel sistema non esistono coppie di oggetti con lo stesso URL, indipendentemente dalla posizione geografica. L'URL completo di un oggetto viene chiamato identificatore di oggetto. L'URL è costituito da un prefisso che identifica l'insieme di credenziali delle chiavi, seguito dal tipo di oggetto, dal nome dell'oggetto specificato dall'utente e dalla versione dell'oggetto. Il nome dell'oggetto non è modificabile e non fa distinzione tra maiuscole e minuscole. Gli identificatori che non includono la versione dell'oggetto vengono definiti identificatori di base.  

Per ulteriori informazioni, vedere[ Autenticazione, richieste e risposte](authentication-requests-and-responses.md)

Un identificatore di oggetto ha il seguente formato generale:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Dove:  

|||  
|-|-|  
|`keyvault-name`|Il nome per un insieme di credenziali delle chiavi nel servizio Microsoft Azure Key Vault.<br /><br /> I nomi di Key Vault vengono selezionati dall'utente e sono univoci.<br /><br /> Il nome dell'insieme di credenziali delle chiavi deve essere costituito da una stringa di lunghezza compresa tra 3 e 24 caratteri, contenente solo i numeri 0-9, i caratteri a-z e A-Z e il trattino -.|  
|`object-type`|Tipo di oggetto, "chiave" o "segreto".|  
|`object-name`|Un `object-name` è un nome utente fornito per un Key Vault e deve essere univoco all'interno di esso. Il nome deve essere costituito da una stringa di lunghezza compresa tra 1 e 127 caratteri, contenente solo i numeri 0-9, i caratteri a-z e A-Z e il trattino -.|  
|`object-version`|`object-version` è un identificatore di stringa di 32 caratteri generato dal sistema che viene usato facoltativamente per fare riferimento a una versione univoca di un oggetto.|  

## <a name="key-vault-keys"></a>Chiavi Key Vault

### <a name="keys-and-key-types"></a>Chiavi e i tipi di chiave

Le chiavi crittografiche in Key Vault sono rappresentate come oggetti JSON Web Key [JWK]. Le specifiche JWK/JWA di base vengono anche estese per abilitare tipi di chiave univoci per l'implementazione di Key Vault. Ad esempio, l'importazione di chiavi tramite la creazione di pacchetti HSM specifici del fornitore consente il trasporto sicuro delle chiavi, che possono essere usate solo nei moduli di protezione hardware di Key Vault.  

- **Chiavi "soft"**: chiave elaborata da Key Vault nel software, ma archiviata come crittografata quando inattiva tramite l'uso di una chiave di sistema che si trovi in un modulo di protezione hardware. I client possono importare una chiave RSA o EC (Elliptic Curve) esistente oppure richiedere a Key Vault di generarne una.
- **Chiavi "hard"**: chiave elaborata in un modulo di protezione hardware. Queste chiavi sono protette in uno degli scenari di sicurezza di un modulo di protezione hardware di Key Vault (è disponibile uno scenario di sicurezza per ogni area geografica per garantire l'isolamento). I client possono importare una chiave RSA o EC, in forma soft o tramite l'esportazione da un modulo di protezione hardware compatibile. I client possono anche richiedere a Key Vault di generare una chiave. Questo tipo di chiave aggiunge l'attributo T a JWK per riportare il materiale della chiave HSM.

     Per ulteriori informazioni sui limiti geografici, vedere [Centro di protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault supporta solo chiavi RSA ed EC (Elliptic Curve). 

-   **EC**: chiave "soft" a curva ellittica.
-   **EC-HSM**: chiave "hard" a curva ellittica.
-   **RSA**: chiave "soft" RSA.
-   **RSA-HSM**: chiave "hard" RSA.

Key Vault supporta chiavi RSA di dimensioni pari a 2048, 3072 e 4096 e chiavi EC (Elliptic Curve) di tipo P-256, P-384, P-521 e P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Protezione crittografica

I moduli crittografici usati da Key Vault, moduli di protezione hardware o software, dispongono di convalida FIPS (Federal Information Processing Standards). Non è necessario attuare misure preventive particolari per l'esecuzione in modalità FIPS. Le chiavi **create** o **importate** come protette tramite il modulo di protezione hardware vengono elaborate all'interno di un modulo di questo tipo e convalidate per FIPS 140-2 livello 2. Le chiavi **create** o **importate** come protette tramite software vengono elaborate all'interno di moduli crittografici e convalidate per FIPS 140-2 livello 1. Per ulteriori informazioni, vedere [Chiavi e i tipi di chiave](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmi EC
 Gli identificatori di algoritmo seguenti sono supportati con le chiavi EC ed EC-HSM in Key Vault. 

#### <a name="curve-types"></a>Tipi di curva

-   **P-256**: curva NIST P-256, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: curva SEC SECP256K1, definita in [SEC 2: Recommended Elliptic Curve Domain Parameters](http://www.secg.org/sec2-v2.pdf) (SEC 2: parametri di dominio EC consigliati).
-   **P-384**: curva NIST P-384, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: curva NIST P-521, definita in [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN//VERIFY

-   **ES256** - ECDSA per digest e chiavi SHA-256 creati con la curva P-256. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA per digest e chiavi SHA-256 creati con la curva P-256K. Questo algoritmo è in attesa di standardizzazione.
-   **ES384** - ECDSA per digest e chiavi SHA-384 creati con la curva P-384. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA per digest e chiavi SHA-512 creati con la curva P-521. Questo algoritmo è descritto in [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmi RSA  
 Gli identificatori di algoritmo seguenti sono supportati con le chiavi RSA e RSA-HSM in Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>ESEGUI/NON ESEGUIRE IL WRAPPING DELLA CHIAVE, CRITTOGRAFA/DECRITTOGRAFA

-   **RSA1_5**: crittografia della chiave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP**: RSAES con OAEP (Optimal Asymmetric Encryption Padding) [RFC3447] con i parametri predefiniti specificati da RFC 3447 nella sezione A.2.1. Tali parametri predefiniti usano una funzione hash di SHA-1 e una funzione di generazione della maschera MGF1 con SHA-1.  

#### <a name="signverify"></a>SIGN//VERIFY

-   **RS256**: RSASSA-PKCS-v1_5 tramite SHA-256. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-256 e deve avere una lunghezza di 32 byte.  
-   **RS384**: RSASSA-PKCS-v1_5 tramite SHA-384. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-384 e deve avere una lunghezza di 48 byte.  
-   **RS512**: RSASSA-PKCS-v1_5 tramite SHA-512. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-512 e deve avere una lunghezza di 64 byte.  
-   **RSNULL**: vedere [RFC2437], un caso di utilizzo specializzato per abilitare determinati scenari TLS.  

###  <a name="key-operations"></a>Operazioni relative alle chiavi

Key Vault supporta le operazioni seguenti sugli oggetti chiave:  

-   **Creazione**: consente a un client di creare una chiave in Key Vault. Il valore della chiave viene generato da Key Vault e archiviato e non viene rilasciato al cliente. In Key Vault è possibile creare chiavi asimmetriche.  
-   **Importazione**: consente a un client di importare una chiave esistente in Key Vault. Le chiavi asimmetriche possono essere importate in Key Vault usando una serie di metodi di creazione di pacchetti diversi all'interno di un costrutto JWK. 
-   **Aggiornamento**: consente a un client con autorizzazioni sufficienti di modificare i metadati (attributi chiave) associati a una chiave archiviata in precedenza in Key Vault.  
-   **Eliminazione**: consente a un client con autorizzazioni sufficienti di eliminare una chiave da Key Vault.  
-   **Elenco**: consente a un client di elencare tutte le chiavi di un determinato Key Vault.  
-   **Elenco delle versioni**: consente a un client di elencare tutte le versioni di una chiave specifica in un determinato Key Vault.  
-   **Recupero**: consente a un client di recuperare le parti pubbliche di una determinata chiave in un Key Vault.  
-   **Backup**: esporta una chiave in un formato protetto.  
-   **Ripristino**: importa una chiave precedentemente sottoposta a backup.  

Per altre informazioni, vedere le [operazioni relative alle chiavi nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault).  

Dopo aver creato una chiave in Key Vault, sarà possibile eseguire le operazioni crittografiche seguenti usando tale chiave:  

-   **Firma e verifica**: in senso stretto, si tratta di una firma hash o di una verifica hash", poiché Key Vault non supporta l'hashing del contenuto durante la creazione della firma. Le applicazioni devono eseguire l'hashing dei dati per la firma locale e quindi richiedere a Key Vault la firma dell'hash. La verifica degli hash firmati è supportata come operazione utile per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per ottenere prestazioni ottimali dalle applicazioni, verificare che le operazioni vengano eseguite in locale.  
-   **Crittografia tramite chiave/Wrapping**: una chiave archiviata in Key Vault può essere usata per proteggere un'altra chiave, in genere una chiave CEK (Content Encryption Key) simmetrica. Quando la chiave in Key Vault è asimmetrica, viene usata la crittografia della chiave. Ad esempio, RSA-OAEP e le operazioni WRAPKEY/UNWRAPKEY equivalgono a ENCRYPT/DECRYPT. Quando la chiave in Key Vault è simmetrica, viene usato il wrapping della chiave, ad esempio AES-KW. L'operazione WRAPKEY è supportata come strumento utile per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per ottenere prestazioni ottimali dalle applicazioni, le operazioni WRAPKEY devono essere eseguite in locale.  
-   **Crittografia e decrittografia**: una chiave archiviata in Key Vault può essere usata per crittografare o decrittografare un singolo blocco di dati. Le dimensioni del blocco sono determinate dal tipo di chiave e dall'algoritmo di crittografia selezionato. L'operazione Encrypt viene fornita per comodità per le applicazioni che non possono accedere al materiale delle chiavi [pubblico]. Per ottenere prestazioni ottimali dalle applicazioni, le operazioni Encrypt devono essere eseguite in locale.  

Anche se WRAPKEY/UNWRAPKEY con chiavi asimmetriche possono sembrare superflue (in quanto equivalenti a ENCRYPT/DECRYPT), l'uso di operazioni distinte è importante. La distinzione assicura la separazione delle operazioni a livello di semantica e autorizzazioni e garantisce coerenza quando sono supportati altri tipi di chiave da parte del servizio.  

Key Vault non supporta le operazioni EXPORT. Una volta effettuato il provisioning di una chiave nel sistema, la chiave non può essere estratta e il relativo materiale non può essere modificato. Tuttavia, è possibile che gli utenti di Key Vault richiedano la chiave per altri casi d'uso, ad esempio dopo che è stata eliminata. In casi di questo tipo, è possibile usare le operazioni BACKUP e RESTORE per esportare o importare la chiave in un formato protetto. Le chiavi create dall'operazione BACKUP non possono essere usate all'esterno di Key Vault. In alternativa, l'operazione IMPORT può essere usata a fronte di più istanze di Key Vault.  

Gli utenti possono limitare le operazioni crittografiche supportate da Key Vault per ciascuna chiave usando la proprietà key_ops dell'oggetto JWK.  

Per ulteriori informazioni sugli oggetti JWK, vedere [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Attributi della chiave

Oltre al materiale della chiave,è possibile specificare gli attributi seguenti. In una richiesta JSON, la parola chiave di attributi e le parentesi graffe, ' {' '}', sono necessari anche se non sono stati specificati gli attributi.  

- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Specifica se la chiave è abilitata e utilizzabile per le operazioni di crittografia. L’attributo *abilitato* viene utilizzato in combinazione con *nbf* ed *exp*. Quando si verifica un'operazione tra *nbf* ed *exp*, questa verrà consentita solo se *abilitato* è impostato su **vero**. Le operazioni di fuori delle finestre *nbf* / *exp* non sono impostate automaticamente, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations).
- *nbf*: il valore predefinito IntDate facoltativo è "now". L’attributo *nbf* (non precedente) identifica l'ora prima della quale la chiave NON DEVE essere usata per le operazioni di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations). L'elaborazione dell’attributo *nbf* richiede che la data/ora corrente SIA successiva o uguale a data/ora non precedente elencata nell’attributo *nbf*. Key Vault PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  
- *exp*: il valore predefinito IntDate facoltativo è "forever". L’attributo *exp* (data di scadenza) identifica la data di scadenza in cui o precedentemente alla quale la chiave NON DEVE essere usata per l’operazione di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](#date-time-controlled-operations). L'elaborazione dell’attributo *exp* richiede che la data/ora corrente SIA precedente a data/ora di scadenza elencata nell’attributo *exp*. Key Vault PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi chiave:  

- *created*: IntDate, facoltativo. L’attributo *creato* indica quando è stata creata questa versione della chiave. Il valore è null per le chiavi create prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  
- *updated*: IntDate, facoltativo. L’attributo *aggiornato* indica quando è stata aggiornata questa versione della chiave. Il valore è null per le chiavi che sono state aggiornate per l'ultima volta prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  

Per ulteriori informazioni su IntDate e altri tipi di dati, vedere [Tipi di dati](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operazioni controllate in base a data e ora

Le chiavi non ancora valide e scadute, che non rientrano nella finestra *nbf* / *exp*, possono essere usate per le operazioni di **decrittografia**, **annullamento del wrapping** e **verifica** (non verrà restituito l'errore 403 Accesso negato). La logica per l'utilizzo di stato non ancora valido consiste nel consentire a una chiave di essere sottoposta a test prima dell'uso di produzione. La logica per l'utilizzo di stato scaduto consiste nel consentire operazioni di ripristino sui dati creati quando la chiave risultava valida. Inoltre, è possibile disabilitare l'accesso a una chiave usando i criteri di Key Vault, o aggiornando l’attributo chiave *abilitato* per **falso**.

Per altre informazioni sui tipi di dati, vedere [Tipi di dati](#data-types).

Per altre informazioni su altri possibili attributi, vedere [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Tag della chiave

È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

>[!Note]
>I tag possono essere letti da un chiamante che ha l'autorizzazione *list* o *get* per accedere a un determinato tipo di oggetto (chiave, segreto o certificato).

###  <a name="key-access-control"></a>Controllo di accesso per le chiavi

Controllo di accesso per le chiavi gestite da Key Vault fornito a livello di un Key Vault che funge da contenitore delle chiavi. I criteri di controllo di accesso per le chiavi sono distinti dai criteri di controllo di accesso per i segreti presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per le chiavi e sono tenuti a mantenere una segmentazione e una gestione delle chiavi appropriate in base allo scenario. Il controllo degli accessi per le chiavi è indipendente dal controllo di accesso per i segreti.  

Le autorizzazioni seguenti possono essere concesse, su base principale utente/servizio, nella voce di controllo di accesso chiavi in un insieme di credenziali. Queste autorizzazioni rispecchiano fedelmente le operazioni consentite su un oggetto chiave:  

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

## <a name="key-vault-secrets"></a>Segreti Key Vault 

### <a name="working-with-secrets"></a>Uso dei segreti

Dal punto di vista dello sviluppo, le API di Key Vault accettano e restituiscono i valori dei segreti sotto forma di stringhe. Internamente, Key Vault archivia e gestisce i segreti come sequenze di ottetti (byte a 8 bit), con dimensioni massime di 25 kbyte ciascuno. Il servizio Key Vault non fornisce la semantica per i segreti. Accetta semplicemente i dati, li crittografa, li archivia e restituisce un identificatore di segreto ("id"). L'identificatore può essere usato per recuperare il segreto in un secondo momento.  

Per i dati altamente sensibili è opportuno prendere in considerazione livelli di protezione aggiuntivi, ad esempio la crittografia dei dati con una chiave di protezione separata prima dell'archiviazione in Key Vault.  

Key Vault supporta anche un campo contentType per i segreti. I client possono specificare il tipo di contenuto di un segreto per facilitare l'interpretazione dei dati del segreto quando vengono recuperati. Il campo può contenere al massimo 255 caratteri. Non sono presenti valori predefiniti. L'utilizzo consigliato è come un hint per l'interpretazione dei dati segreti. Ad esempio, in un'implementazione possono essere archiviati password e certificati come segreti. Questo campo consente quindi di distinguerli. Non sono presenti valori predefiniti.  

### <a name="secret-attributes"></a>Attributi dei segreti

Oltre ai dati dei segreti,è possibile specificare gli attributi seguenti:  

- *exp*: il valore predefinito IntDate facoltativo è **forever**. L’attributo *exp* (ora di scadenza) identifica l'ora di scadenza in cui o successiva alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](#date-time-controlled-operations). Il campo è a solo scopo **informativo** e informa gli utenti del servizio di insieme di credenziali delle chiavi che non è possibile usare un segreto specifico. Il valore DEVE essere un numero contenente un valore IntDate.   
- *nbf*: il valore predefinito IntDate facoltativo è **now**. L’attributo *nbf* (non precedente) identifica l'ora precedente alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](#date-time-controlled-operations). Questo documento è esclusivamente a scopo **informativo**. Il valore DEVE essere un numero contenente un valore IntDate. 
- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo specifica se i dati dei segreti possono essere recuperati. L'attributo abilitato viene usato in combinazione con *nbf* ed *exp* quando si verifica un'operazione tra *nbf* ed *exp*, sarà consentito solo se Abilitato è impostato su **true**. Le operazioni all'esterno della finestra *nbf* ed *exp* non sono impostate automaticamente non consentiti, ad eccezione di [situazioni particolari](#date-time-controlled-operations).  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi dei segreti:  

- *created*: IntDate, facoltativo. L’attributo creato indica quando è stata creata questa versione del segreto. Questo valore è null per i segreti creati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.  
- *updated*: IntDate, facoltativo. L’attributo aggiornato indica quando è stata aggiornata questa versione del segreto. Questo valore è null peri segreti aggiornati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.

#### <a name="date-time-controlled-operations"></a>Operazioni controllate in base a data e ora

L’operazione **ottieni** di un segreto funzionerà per i segreti non ancora validi e scaduti, all'esterno della finestra *nbf* / *exp*. La chiamata dell’operazione **ottieni** di un segreto, per una chiave privata non ancora valida, può essere utilizzata per scopi di test. Il recupero (**get**) di un segreto scaduto può essere usato per le operazioni di ripristino.

Per altre informazioni sui tipi di dati, vedere [Tipi di dati](#data-types).  

### <a name="secret-access-control"></a>Controllo di accesso per i segreti

Il controllo di accesso per i segreti gestiti in Key Vault viene fornito al livello dell'insieme di credenziali delle chiavi che contiene tali segreti. I criteri di controllo di accesso per i segreti sono distinti dai criteri di controllo di accesso per le chiavi presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per i segreti e sono tenuti a mantenere una segmentazione e una gestione dei segreti appropriate in base allo scenario.   

Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

- Autorizzazioni per le operazioni di gestione dei segreti
  - *get*: legge un segreto  
  - *list*: consente di elencare i segreti o le versioni di un segreto archiviati in un Key Vault  
  - *set*: Creare un segreto  
  - *delete*: consente di eliminare un segreto  
  - *recover*: consente di recuperare un server eliminato
  - *backup*: consente di eseguire il backup di un segreto in un insieme di credenziali delle chiavi
  - *restore*: consente di ripristinare un segreto sottoposto a backup in un insieme di credenziali delle chiavi

- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un segreto eliminato

Per altre informazioni sull'uso dei segreti, vedere le [operazioni relative ai segreti nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy). 

### <a name="secret-tags"></a>Tag dei segreti  
È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

>[!Note]
>I tag possono essere letti da un chiamante che ha l'autorizzazione *list* o *get* per accedere a un determinato tipo di oggetto (chiave, segreto o certificato).

## <a name="key-vault-certificates"></a>Certificati Key Vault

La funzionalità Certificati dell'insieme di credenziali delle chiavi supporta la gestione di certificati X509 e consente di eseguire le operazioni seguenti:  

-   Il proprietario di un certificato può creare un certificato tramite un processo di creazione dell'insieme di credenziali delle chiavi o tramite l'importazione di un certificato esistente. Sono inclusi i certificati autofirmati e quelli generati dall'autorità di certificazione.
-   Il proprietario di un certificato dell'insieme di credenziali delle chiavi può implementare l'archiviazione sicura e la gestione di certificati X509 senza interagire con materiale della chiave privata.  
-   Il proprietario di un certificato può creare criteri che guidano l'insieme di credenziali delle chiavi nella gestione del ciclo di vita di un certificato.  
-   I proprietari di un certificato possono specificare informazioni sul contatto per notificare eventi sul ciclo di vita di un certificato, come la scadenza e il rinnovo.  
-   Viene supportato il rinnovo automatico con autorità di certificazione selezionate, ad esempio provider di certificati X509 / Autorità di certificazione partner dell'insieme di credenziali delle chiavi.

>[!Note]
>Sono ammessi anche i provider e le autorità di certificazione senza partnership, ma non supportano la funzionalità di rinnovo automatico.

### <a name="composition-of-a-certificate"></a>Componenti di un certificato

Quando viene creato un certificato Key Vault, vengono creati anche una chiave e un segreto indirizzabili con lo stesso nome. La chiave Key Vault consente operazioni di chiave e il segreto Key Vault consente il recupero del valore del certificato come segreto. Un certificato Key Vault contiene inoltre metadati del certificato x509 pubblico.  

L'identificatore e la versione dei certificati sono simili a quelli delle chiavi e segreti. Una versione specifica di una chiave e del segreto indirizzabili creati con la versione di certificato Key Vault è disponibile nella risposta di certificato Key Vault.
 
![Certificati come oggetti complessi](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Chiave esportabile o non esportabile

Un certificato di Key Vault creato può essere recuperato dal segreto indirizzabile con la chiave privata in formato PFX o PEM. I criteri usati per creare il certificato devono indicare che la chiave è esportabile. Se i criteri indicano che non è esportabile, la chiave privata non farà parte del valore recuperato come segreto.  

La chiave indirizzabile diventa maggiormente pertinente con certificati KV non esportabili. Le operazioni della chiave KV indirizzabile sono mappate dal campo *keyusage* dei criteri del certificato KV utilizzato per creare il certificato KV.  

Sono supportati due tipi di chiave *RSA* oppure *RSA HSM* con certificati. La possibilità di essere esportabile è consentita solo con RSA, non è supportata da RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Tag e attributi dei certificati

Oltre ai metadati del certificato, a una chiave e a un segreto indirizzabili, un certificato di Key Vault contiene anche attributi e tag.  

#### <a name="attributes"></a>Attributi

Gli attributi di certificato si riflettono negli attributi della chiave e del segreto indirizzabile creati quando viene creato KV certificato.  

Un certificato Key Vault ha gli attributi seguenti:  

-   *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo può essere specificato per indicare se i dati del certificato possono essere recuperati come segreti o sono eseguibili come chiave. Viene anche usato in combinazione con *nbf* ed *exp* quando si verifica un'operazione nella finestra tra *nbf* ed *exp*. Ne sarà consentito l'uso solo se enabled è impostato su true. Le operazioni all'esterno della finestra di *nbf* ed *exp* non sono consentite automaticamente.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta:

-   *created*: valore IntDate che indica quando è stata creata questa versione del certificato.  
-   *updated*: valore IntDate che indica quando è stata aggiornata questa versione del certificato.  
-   *exp*: valore IntDate che contiene il valore della data di scadenza del certificato x509.  
-   *nbf*: valore IntDate che contiene il valore della data del certificato x509.  

> [!Note] 
> Se un certificato in Key Vault scade, la chiave e il segreto indirizzabili sono inutilizzabili.  

#### <a name="tags"></a>Tag

 Dizionario specificato dal client di coppie chiave-valore, simile ai tag delle chiavi e dei segreti.  

 > [!Note]
> I tag possono essere letti da un chiamante che ha l'autorizzazione *list* o *get* per accedere a un determinato tipo di oggetto (chiave, segreto o certificato).

### <a name="certificate-policy"></a>Criteri dei certificati

I criteri dei certificati contengono informazioni su come creare e gestire la durata di un certificato di Key Vault. Quando un certificato con chiave privata viene importato nell'insieme di credenziali delle chiavi, viene creato un criterio predefinito leggendo il certificato x509.  

Quando viene creato un certificato di Key Vault completamente nuovo, è necessario specificare i criteri. I criteri specificano come creare questa versione o la versione successiva del certificato di Key Vault. Dopo aver definito i criteri, non sarà necessario ripetere la procedura per le successive operazioni di creazione delle versioni future. È presente una sola istanza dei criteri per tutte le versioni di un certificato di Key Vault.  

In generale, i criteri dei certificati contengono le informazioni seguenti:  

-   Proprietà del certificato X509: nome del soggetto, nomi alternativi del soggetto e altre proprietà usate per creare una richiesta di certificato x509.  
-   Proprietà della chiave: contiene il tipo di chiave, la lunghezza della chiave e i campi relativi alla chiave esportabile e di riutilizzo. Questi campi indicare all’insieme di credenziali delle chiavi come generare una chiave.  
-   Proprietà del segreto: contiene le proprietà del segreto, ad esempio tipo di contenuto del segreto indirizzabile per generare il valore del segreto, al fine di recuperare il certificato come segreto.  
-   Azioni di durata: contiene le azioni di durata per il certificato KV. Ogni azione di durata contiene:  

     - Trigger: specificato tramite i giorni precedenti alla scadenza o alla percentuale dell’intervallo di vita  

     - Azione: specificare il tipo di azione *emailContacts* o *autoRenew*  

-   Autorità di certificazione: parametri relativi all’autorità di certificazione del certificato da usare per l'emissione dei certificati x509.  
-   Attributi dei criteri: contiene gli attributi associati al criterio  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mappatura di X509 per l’utilizzo in Key Vault

La tabella seguente rappresenta il mapping dei criteri di utilizzo della chiave per le operazioni di chiave efficaci di una chiave creata come parte della creazione di un certificato Key Vault x509.

|**Flag utilizzo chiave X509**|**Operazioni chiave Key Vault**|**Comportamento predefinito**|
|----------|--------|--------|
|DataEncipherment|crittografa, decrittografa| N/D |
|DecipherOnly|decrypt| N/D  |
|DigitalSignature|firma, verifica| Valore predefinito Key Vault senza una specifica di utilizzo al momento della creazione di certificati | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|firma, verifica|N/D|
|KeyEncipherment|esegui/non eseguire il wrapping della chiave| Valore predefinito Key Vault senza una specifica di utilizzo al momento della creazione di certificati | 
|NonRepudiation|firma, verifica| N/D |
|crlsign|firma, verifica| N/D |

### <a name="certificate-issuer"></a>Autorità di certificazione

Un oggetto del certificato Key Vault contiene una configurazione utilizzata per comunicare con un provider dell’autorità di certificazione selezionato per ordinare certificati x509.  

-   Key Vault agisce in collaborazione con i provider dell’autorità di certificazione seguente per certificati SSL

|**Nome provider**|**Località**|
|----------|--------|
|DigiCert|Supportata in tutte le posizioni del servizio dell'insieme di credenziali delle chiavi nel cloud pubblico e Azure per enti pubblici|
|GlobalSign|Supportata in tutte le posizioni del servizio dell'insieme di credenziali delle chiavi nel cloud pubblico e Azure per enti pubblici|

Prima della creazione di un'autorità di certificazione in Key Vault, è necessario eseguire i seguenti passaggi prerequisiti 1 e 2.  

1. Eseguire l’onboarding ai provider dell’autorità di certificazione (CA)  

    -   Un amministratore dell'organizzazione deve eseguire l’onboardig della propria azienda (es. Contoso) con almeno un provider di autorità di certificazione.  

2. L'amministratore crea le credenziali del richiedente per Key Vault al fine di registrare (e rinnovare) i certificati SSL  

    -   Fornisce la configurazione da utilizzare per creare un oggetto dell'autorità di certificazione del provider nell'insieme di credenziali delle chiavi  

Per ulteriori informazioni sulla creazione di oggetti di autorità di certificazione dal portale di certificati, vedere il [blog dei certificati Key Vault](https://aka.ms/kvcertsblog)  

Key Vault consente di creare più oggetti di autorità di certificazione con una differente configurazione del provider dell'autorità di certificazione. Dopo che è stato creato un oggetto di autorità di certificazione, è possibile fare riferimento al suo nome in uno o più criteri del certificato KV. Fare riferimento all'oggetto dell'autorità di certificazione indica a Key Vault di usare la configurazione come specificato nell'oggetto dell'autorità di certificazione quando si richiede il certificato x509 dal provider di autorità di certificazione durante la creazione e il rinnovo di certificati.  

Gli oggetti di autorità di certificazione vengono creati nell'insieme di credenziali e possono essere utilizzati solo con certificati KV nello stesso insieme di credenziali.  

### <a name="certificate-contacts"></a>Contatti relativi al certificato

I contatti relativi al certificato contengono le informazioni di contatto per inviare notifiche attivate da eventi di durata dei certificati. Le informazioni dei contatti vengono condivise da tutti i certificati nell'insieme di credenziali delle chiavi. Viene inviata una notifica a tutti i contatti specificati per un evento per qualsiasi certificato presente nell'insieme di credenziali delle chiavi.  

Se i criteri del certificato sono impostati per il rinnovo automatico, viene inviata una notifica sugli eventi seguenti.  

-   Prima del rinnovo del certificato
-   Dopo il rinnovo del certificato, che indica se il certificato è stato rinnovato, o se si è verificato un errore, richiede il rinnovo manuale del certificato.  

 Se i criteri del certificato sono impostati per il rinnovo manuale (solo posta elettronica), viene inviata una notifica al momento del rinnovo.  

### <a name="certificate-access-control"></a>Controllo di accesso per i certificati

 Il controllo di accesso per i certificati è gestito da Key Vault e viene fornito dall'insieme di credenziali delle chiavi che contiene tali certificati. I criteri di controllo di accesso per i certificati sono distinti dai criteri di controllo di accesso per le chiavi e i segreti presenti nello stesso insieme di credenziali delle chiavi. Gli utenti possono creare uno o più insiemi di credenziali per i certificati e per mantenere una segmentazione e una gestione dei certificati appropriate in base allo scenario.  

 Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali delle chiavi e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

- Autorizzazioni per le operazioni di gestione dei certificati
  - *get*: consente di ottenere la versione corrente del certificato o qualsiasi versione di un certificato 
  - *list*: consente di elencare i certificati correnti o le versioni di un certificato  
  - *update*: consente di aggiornare un certificato
  - *create*: consente di creare un certificato di Key Vault
  - *import*: consente di importare il materiale apposito in un certificato di Key Vault
  - *delete*: consente di eliminare un certificato, nonché i relativi criteri e versioni  
  - *recover*: consente di recuperare un certificato eliminato
  - *backup*: consente di eseguire il backup di un certificato in un insieme di credenziali delle chiavi
  - *restore*: consente di ripristinare un certificato sottoposto a backup in un insieme di credenziali delle chiavi
  - *managecontacts*: consente di gestire i contatti del certificato in un Key Vault  
  - *manageissuers*: consente di gestire autorità di certificazione di Key Vault
  - *getissuers*: consente di ottenere le autorità emittenti del certificato
  - *listissuers*: consente di elencare le autorità emittenti del certificato  
  - *setissuers*: consente di creare o aggiornare le autorità di certificazione di un certificato di Key Vault  
  - *deleteissuers*: consente di eliminare le autorità di certificazione del certificato di Key Vault  
 
- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un certificato

Per altre informazioni, vedere le [operazioni relative ai certificati nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="azure-storage-account-key-management"></a>Gestione delle chiavi dell'account di archiviazione di Azure

Key Vault può gestire le chiavi dell'account di archiviazione Azure:

- Internamente, Key Vault può elencare (sincronizzare) le chiavi con un account di archiviazione di Azure. 
- Key Vault rigenera (ruota) le chiavi con cadenza periodica.
- I valori di chiave non vengono mai restituiti in risposta al chiamante.
- Key Vault gestisce le chiavi sia degli account di archiviazione sia degli account di archiviazione classici.

Per altre informazioni, vedere [Chiavi dell'account di archiviazione Key Vault](key-vault-ovw-storage-keys.md).

### <a name="storage-account-access-control"></a>Controllo di accesso dell'account di archiviazione

Le autorizzazioni seguenti consentono di autorizzare un utente o un'entità di sicurezza dell'applicazione a eseguire operazioni su un account di archiviazione gestito:  

- Autorizzazioni per le operazioni relative all'account di archiviazione gestito e alla definizione SAS
  - *get*: consente di ottenere informazioni su un account di archiviazione 
  - *list*: consente di elencare gli account di archiviazione gestiti da Key Vault
  - *update*: consente di creare un account di archiviazione
  - *delete*: Eliminare un account di archiviazione  
  - *recover*: consente di ripristinare un account di archiviazione eliminato
  - *backup*: consente di eseguire il backup di un account di archiviazione
  - *restore*: consente di ripristinare un account di archiviazione sottoposto a backup in un Key Vault
  - *set*: consente di creare o aggiornare un account di archiviazione
  - *regeneratekey*: consente di rigenerare un valore di chiave specificato per un account di archiviazione
  - *getsas*: consente di ottenere informazioni su una definizione SAS per un account di archiviazione
  - *listsas*: consente di elencare le definizioni SAS di archiviazione per un account di archiviazione
  - *deletesas*: consente di eliminare una definizione SAS da un account di archiviazione
  - *setsas*: consente di creare o aggiornare una nuova definizione SAS/nuovi attributi per un account di archiviazione

- Autorizzazioni per le operazioni privilegiate
  - *purge*: consente di eliminare definitivamente un account di archiviazione gestito

Per altre informazioni, vedere le operazioni relative ai certificati in [Key Vault REST API reference](/rest/api/keyvault) (Riferimenti sull'API REST di Key Vault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="see-also"></a>Vedere anche

- [Autenticazione, richieste e risposte](authentication-requests-and-responses.md)
- [Versioni di Key Vault](key-vault-versions.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](/azure/key-vault/key-vault-developers-guide)
