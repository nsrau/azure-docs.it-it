---
title: Informazioni su chiavi, segreti e certificati
description: Panoramica dell'interfaccia REST e dettagli per sviluppatori KV
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 77675b3c0b2ed9fcdb923c92638384d215bddc40
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972401"
---
# <a name="about-keys-secrets-and-certificates"></a>Informazioni su chiavi, segreti e certificati
Azure Key Vault consente agli utenti di archiviare e utilizzare le chiavi di crittografia all'interno dell'ambiente Microsoft Azure. Key Vault supporta più tipi di chiavi e algoritmi e consente l'utilizzo di moduli di protezione hardware (HSM) per le chiavi di valore elevato. Inoltre, Key Vault consente agli utenti di archiviare in modo sicuro i segreti. I segreti sono oggetti ottetto di dimensioni limitate senza alcuna semantica specifica. Key Vault supporta inoltre certificati, che si avvalgono di chiavi e segreti, e aggiunge una funzionalità di rinnovo automatico.

Per altre informazioni su Azure Key Vault, vedere [Cos'è Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Dettagli generali di Key Vault**

-   [Standard di supporto](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Tipi di dati](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Oggetti, identificatori e controllo delle versioni](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Informazioni sulle chiavi**

-   [Chiavi e i tipi di chiave](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algoritmi RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algoritmi RSA-HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Protezione crittografica](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Operazioni chiave](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Attributi chiave](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Tag della chiave](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Informazioni sui segreti** 

-   [Utilizzo dei segreti](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Attributi dei segreti](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Tag dei segreti](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Controllo di accesso per i segreti](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Informazioni sui certificati**

-   [Componenti di un certificato](#BKMK_CompositionOfCertificate)  
-   [Tag e attributi del certificato](#BKMK_CertificateAttributesAndTags)  
-   [Criteri dei certificati](#BKMK_CertificatePolicy)  
-   [Autorità di certificazione](#BKMK_CertificateIssuer)  
-   [Contatti relativi al certificato](#BKMK_CertificateContacts)  
-   [Controllo di accesso di certificato](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Dettagli generali di Key Vault

Nelle sezioni seguenti vengono presentate informazioni generali applicabili internamente alle implementazioni del servizio insieme Azure Key Vault.

###  <a name="BKMK_Standards"></a> Standard di supporto

Le specifiche di JavaScript Object Notation (JSON) e JavaScript Object Signing and Encryption (JOSE) costituiscono informazioni generiche importanti.  

-   [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Tipi di dati

Consultare le [specifiche JOSE](#BKMK_Standards) per i tipi di dati pertinenti per chiavi, crittografia e firma.  

-   **algoritmo**: un algoritmo supportato per un'operazione della chiave, ad esempio, RSA1_5  
-   **valore di testo crittografato**: ottetti di testo cifrati, codificati tramite Base64URL  
-   **valore di digest**: l'output di un algoritmo hash, codificato tramite Base64URL  
-   **tipo di chiave**: uno dei tipi di chiave supportati, ad esempio RSA.  
-   **valore di testo non crittografato**: ottetti di testo non crittografato, codificati tramite Base64URL  
-   **valore di firma**: l'output di un algoritmo di firma, codificato tramite Base64URL  
-   **base64URL**: un valore binario Base64URL [RFC4648] codificato  
-   **booleano**: vero o falso  
-   **Identità**: un identità di Azure Active Directory (AAD).  
-   **IntDate** : un valore decimale JSON che rappresenta il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla data/ora UTC specificata. Vedere [RFC3339] per dettagli relativi alla data/ora a livello generale e UTC in particolare.  

###  <a name="BKMK_ObjId"></a> Oggetti, identificatori e controllo delle versioni

Gli oggetti archiviati Azure Key Vault conservano le versioni ogni volta che viene creata una nuova istanza di un oggetto e ogni versione ha un identificatore univoco e un URL. Quando viene creato per la prima volta un oggetto, viene assegnato a un identificatore univoco della versione e viene contrassegnato come la versione corrente dell'oggetto. La creazione di una nuova istanza con lo stesso nome di oggetto fornisce al nuovo oggetto un identificatore univoco della versione e lo rende la versione corrente.  

Gli oggetti Azure Key Vault possono essere trattati usando l'identificatore attuale o un identificatore specifico della versione. Ad esempio, data una chiave con nome "MasterKey", l'esecuzione di operazioni con l'identificatore attuale fa sì che il sistema userà la versione più recente disponibile. L'esecuzione di operazioni con l'identificatore specifico della versione fa sì che il sistema userà quella versione specifica dell'oggetto.  

Gli oggetti vengono identificati in modo univoco all'interno di Azure Key Vault tramite l’utilizzo di un URL in modo che nessuno tra i due oggetti nel sistema, indipendentemente dalla posizione geografica, abbia lo stesso URL. L'URL completo per un oggetto viene chiamato l'identificatore di oggetto ed è costituito da un prefisso che identifica l'insieme di credenziali chiave, il tipo di oggetto, il nome dell'oggetto fornito dall'utente e una versione dell'oggetto. Il nome dell'oggetto non è modificabile e non fa distinzione tra maiuscole e minuscole. Gli identificatori che non includono la versione dell'oggetto vengono definiti come identificatori di base.  

Per ulteriori informazioni, vedere[ Autenticazione, richieste e risposte](authentication-requests-and-responses.md)

Un identificatore di oggetto ha il seguente formato generale:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Dove:  

|||  
|-|-|  
|`keyvault-name`|Il nome per un insieme di credenziali delle chiavi nel servizio Microsoft Azure Key Vault.<br /><br /> I nomi di Key Vault vengono selezionati dall'utente e sono univoci.<br /><br /> Il nome di Key Vault deve essere una stringa composta da un minimo di 3 a un massimo di 24 caratteri e contenente solo (0-9, a-z, A-Z e -).|  
|`object-type`|Il tipo di oggetto, "chiavi" o "segreti".|  
|`object-name`|Un `object-name` è un nome utente fornito per un Key Vault e deve essere univoco all'interno di esso. Il nome deve essere una stringa composta da un minimo di uno a un massimo di 127 caratteri e contenente solo i caratteri 0-9, a-z, A-Z e -.|  
|`object-version`|Un `object-version` è un identificatore di stringa di 32 caratteri generato dal sistema che consente eventualmente di far fronte a una versione univoca di un oggetto.|  

## <a name="key-vault-keys"></a>Chiavi Key Vault

###  <a name="BKMK_KeyTypes"></a> Chiavi e i tipi di chiave

Le chiavi crittografiche in Azure Key Vault sono rappresentate come oggetti JSON Web Key [JWK]. Le specifiche di base JWK/JWA vengono inoltre estese per consentire l’implementazione in Azure Key Vault dei tipi di chiave univoci, ad esempio importando le chiavi Azure Key Vault tramite il pacchetto specifico del fornitore (Thales) del modulo di protezione hardware per abilitare il trasporto sicuro di chiavi utilizzabili solo nei moduli di protezione hardware di Azure Key Vault.  

La versione iniziale di Azure Key Vault supporta solo le chiavi RS; le versioni future potrebbero supportare altri tipi di chiave, ad esempio simmetriche e a curva ellittica.  

-   **RSA**: una chiave RSA a 2048 bit. Si tratta di una chiave "soft", che viene elaborata da Key Vault nel software ma viene archiviata come crittografata quando inattiva tramite l’utilizzo di una chiave di sistema che si trovi in un modulo di protezione hardware. I client possono importare una chiave RSA esistente o richiedere a Azure Key Vault di generarne una.  
-   **RSA-HSM**: una chiave RSA elaborata in un modulo di protezione hardware. Le chiavi RSA-HSM sono protette in uno degli scenari di sicurezza di un modulo di protezione hardware di Azure Key Vault (è disponibile uno scenario di sicurezza per ogni area geografica per garantire l'isolamento). I client possono importare una chiave RSA, in forma soft o tramite l'esportazione da un dispositivo del modulo di protezione hardware, o richiedere a Azure Key Vault di generarne una. Questo tipo di chiave aggiunge l'attributo T a JWK per riportare il materiale della chiave HSM.  

     Per ulteriori informazioni sui limiti geografici, vedere [Centro di protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algoritmi RSA  
 Gli identificatori di algoritmi seguenti sono supportati con le chiavi RSA in Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>ESEGUI/NON ESEGUIRE IL WRAPPING DELLA CHIAVE, CRITTOGRAFA/DECRITTOGRAFA

-   **RSA1_5**: crittografia della chiave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP**: RSAES con OAEP (Optimal Asymmetric Encryption Padding) [RFC3447] con i parametri predefiniti specificati da RFC 3447 nella sezione A.2.1. Tali parametri predefiniti usano una funzione hash di SHA-1 e una funzione di generazione della maschera MGF1 con SHA-1.  

#### <a name="signverify"></a>SIGN//VERIFY

-   **RS256**: RSASSA-PKCS-v1_5 tramite SHA-256. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-256 e deve avere una lunghezza di 32 byte.  
-   **RS384**: RSASSA-PKCS-v1_5 tramite SHA-384. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-384 e deve avere una lunghezza di 48 byte.  
-   **RS512**: RSASSA-PKCS-v1_5 tramite SHA-512. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-512 e deve avere una lunghezza di 64 byte.  
-   **RSNULL**: vedere [RFC2437], un caso di utilizzo specializzato per abilitare determinati scenari TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algoritmi RSA-HSM  
Gli identificatori di algoritmi seguenti sono supportati con le chiavi RSA-HSM in Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Protezione crittografica

I moduli di crittografia utilizzati da Azure Key Vault Azure, moduli di protezione hardware o software, sono dispongono di convalida FIPS. Non è necessario attuare misure preventive particolari per l'esecuzione in modalità FIPS. Se si **creano** oppure si **importano** chiavi come protette del modulo di protezione hardware, queste verranno elaborate all'interno dei moduli di protezione hardware convalidati per il livello 2 o una versione successiva di FIPS 140-2. Se si **creano** oppure si **importano** chiavi come protette dal software, queste verranno elaborate all'interno di moduli di crittografia convalidati per il livello 1 o la versione successivi di FIPS 140-2. Per ulteriori informazioni, vedere [Chiavi e i tipi di chiave](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>ESEGUI/NON ESEGUIRE IL WRAPPING, CRITTOGRAF/DECRITTOGRAFA

-   **RSA1_5**: crittografia della chiave RSAES-PKCS1-V1_5 [RFC3447].  
-   **RSA-OAEP**: RSAES con OAEP (Optimal Asymmetric Encryption Padding) [RFC3447] con i parametri predefiniti specificati da RFC 3447 nella sezione A.2.1. Tali parametri predefiniti usano una funzione hash di SHA-1 e una funzione di generazione della maschera MGF1 con SHA-1.  

 #### <a name="signverify"></a>SIGN//VERIFY  

-   **RS256**: RSASSA-PKCS-v1_5 tramite SHA-256. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-256 e deve avere una lunghezza di 32 byte.  
-   **RS384**: RSASSA-PKCS-v1_5 tramite SHA-384. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-384 e deve avere una lunghezza di 48 byte.  
-   **RS512**: RSASSA-PKCS-v1_5 tramite SHA-512. Il valore di digest dell'applicazione fornito deve essere calcolato tramite SHA-512 e deve avere una lunghezza di 64 byte.  
-   RSNULL: vedere [RFC2437], un caso di utilizzo specializzato per abilitare determinati scenari TLS.  

###  <a name="BKMK_KeyOperations"></a> Operazioni chiave

Azure Key Vault supporta le seguenti operazioni sugli oggetti chiave:  

-   **Crea**: consente al client di creare una chiave in Azure Key Vault. Il valore della chiave generato da Azure Key Vault e archiviato e non rilasciato al client. Le chiavi asimmetriche (e in futuro, chiavi a curva ellittica e simmetriche) possono essere create in Azure Key Vault.  
-   **Importa**: consente al client di importare una chiave esistente in Azure Key Vault. Le chiavi asimmetriche (e in futuro, chiavi a curva ellittica e simmetriche) possono essere importate in Azure Key Vault usando una serie di metodi di creazione di pacchetti diversi all'interno di un costrutto JWK.  
-   **Aggiorna**: consente al client con autorizzazioni sufficienti di modificare i metadati (attributi chiave) associati a una chiave archiviata in precedenza in Azure Key Vault.  
-   **Elimina**: consente al client con autorizzazioni sufficienti di eliminare una chiave dall'insieme da Azure Key Vault.  
-   **Elenca**: consente al client di elencare tutte le chiavi in un determinato Azure Key Vault.  
-   **Elenca versioni**: consente al client di elencare tutte le versioni di una data chiave in un dato Azure Key Vault.  
-   **Ottieni**: consente al client di recuperare le parti pubbliche di una data chiave in un Azure Key Vault.  
-   **Backup**: esporta una chiave in un formato protetto.  
-   **Ripristina**: importa una chiave precedentemente sottoposta a backup.  

Per altre informazioni, vedere [Operazioni chiave](/rest/api/keyvault/key-operations).  

Dopo avere creato una chiave in Azure Key Vault, le operazioni crittografiche seguenti potranno essere eseguite utilizzando la chiave:  

-   **Firma e verifica**: in via del tutto eccezionale, questa operazione è "firma hash" o "verifica hash" poiché Azure Key Vault non supporta l'hashing del contenuto in quanto parte della creazione della firma. Le applicazioni devono eseguire l'hashing dei dati per la firma locale, quindi richiedere la firma dell'hash a Azure Key Vault. La verifica degli hash firmati è supportata come operazione utile per le applicazioni che potrebbero non avere accesso al materiale della chiave [pubblica]; per garantire le prestazioni ottimali delle applicazioni, è consigliabile verificare che le operazioni siano eseguite localmente.  
-   **Chiave di crittografia/Esecuzione del wrapping**: una chiave archiviata in Azure Key Vault può essere utilizzata per proteggere un'altra chiave, in genere una chiave di crittografia simmetrica dei contenuti (CEK). Quando la chiave in Azure Key Vault è asimmetrica, viene utilizzata la crittografia con chiave, ad esempio RSA-OAEP e le operazioni ESEGUI/NON ESEGUIRE IL WRAPPING DELLA CHIAVE equivalgono a CRITTOGRAFA/DECRITTOGRAFA. Quando la chiave in Azure Key Vault è simmetrica,il wrapping della chiave viene utilizzato; ad esempio AES-KW. L’operazione ESEGUI IL WRAPPING DELLA CHIAVE è supportata come operazione utile per le applicazioni che potrebbero non avere accesso al materiale della chiave [pubblica]; per garantire le prestazioni ottimali delle applicazioni, è consigliabile verificare che le operazioni ESEGUI IL WRAPPING DELLA CHIAVE siano eseguite localmente.  
-   **Crittografa e decrittografa**: una chiave archiviata in Azure Key Vault può essere utilizzata per crittografare o decrittografare un singolo blocco di dati, la cui dimensione è determinata dal tipo di chiave e dall'algoritmo di crittografia selezionato. L’operazione Crittografa è fornita come operazione utile per le applicazioni che potrebbero non avere accesso al materiale della chiave [pubblica]; per garantire le prestazioni ottimali delle applicazioni, è consigliabile verificare che le operazioni di crittografia siano eseguite localmente.  

Mentre l’operazione ESEGUI/NON ESEGUIRE IL WRAPPING DELLA CHIAVE con chiavi asimmetriche potrebbe sembrare superflua poiché l'operazione è equivalente a CRITTOGRAFA/DECRITTOGRAFA, l'utilizzo di operazioni distinte è considerato importante per offrire la separazione dalla semantica e dall’autorizzazione di queste operazioni e coerenza quando altri tipi di chiave sono supportati dal servizio.  

Azure Key Vault non supporta le operazioni di esportazione: una volta che viene eseguito il provisioning di una chiave nel sistema, il materiale non può essere estratto o modificato.  Tuttavia, gli utenti di Azure Key Vault che potrebbero necessitare della chiave per altri casi d'uso o dopo che è stata eliminata da Azure Key Vault, possono utilizzare le operazioni BACKUP e RIPRISTINA per esportare/importare la chiave in un formato protetto. Le chiavi create dall'operazione di BACKUP non sono utilizzabili all'esterno di Azure Key Vault. In alternativa, l'operazione di importazione può essere utilizzata in più istanze di Azure Key Vault.  

Gli utenti possono limitare le operazioni di crittografia che supporta Azure Key Vault su base per chiave utilizzando la proprietà key_ops dell'oggetto JWK.  

Per ulteriori informazioni sugli oggetti JWK, vedere [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Attributi chiave

Oltre al materiale della chiave,è possibile specificare gli attributi seguenti. In una richiesta JSON, la parola chiave di attributi e le parentesi graffe, ' {' '}', sono necessari anche se non sono stati specificati gli attributi.  

- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Specifica se la chiave è abilitata e utilizzabile per le operazioni di crittografia. L’attributo *abilitato* viene utilizzato in combinazione con *nbf* ed *exp*. Quando si verifica un'operazione tra *nbf* ed *exp*, questa verrà consentita solo se *abilitato* è impostato su **vero**. Le operazioni di fuori delle finestre *nbf* / *exp* non sono impostate automaticamente, ad eccezione di alcuni tipi di operazione in [particolari condizioni](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *nbf*: il valore predefinito IntDate facoltativo è adesso. L’attributo *nbf* (non precedente) identifica l'ora prima della quale la chiave NON DEVE essere usata per le operazioni di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). L'elaborazione dell’attributo *nbf* richiede che la data/ora corrente SIA successiva o uguale a data/ora non precedente elencata nell’attributo *nbf*. Azure Key Vault di Azure PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  
- *nbf*: il valore predefinito IntDate facoltativo è "per sempre". L’attributo *exp* (data di scadenza) identifica la data di scadenza in cui o precedentemente alla quale la chiave NON DEVE essere usata per l’operazione di crittografia, ad eccezione di alcuni tipi di operazione in [particolari condizioni](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). L'elaborazione dell’attributo *exp* richiede che la data/ora corrente SIA precedente a data/ora di scadenza elencata nell’attributo *exp*. Azure Key Vault di Azure PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi chiave:  

- *creato*: IntDate facoltativo. L’attributo *creato* indica quando è stata creata questa versione della chiave. Questo valore è null per le chiavi create prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  
- *aggiornato*: IntDate facoltativo. L’attributo *aggiornato* indica quando è stata aggiornata questa versione della chiave. Questo valore è null per le chiavi aggiornate prima dell'aggiunta di questo attributo. Il valore DEVE essere un numero contenente un valore IntDate.  

Per ulteriori informazioni su IntDate e altri tipi di dati, vedere [Tipi di dati](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operazioni controllate in base a data e ora

Chiavi non ancora valide e scadute, al di fuori della finestra *nbf* / *exp*, possono essere utilizzate per **decrittografare**, **non eseguire il wrapping** e **verificare** operazioni (non verrà restituito, 403 accesso negato). La logica per l'utilizzo di stato non ancora valido consiste nel consentire a una chiave di essere sottoposta a test prima dell'uso di produzione. La logica per l'utilizzo di stato scaduto consiste nel consentire operazioni di ripristino sui dati creati quando la chiave risultava valida. Inoltre, è possibile disabilitare l'accesso a una chiave usando i criteri di Key Vault, o aggiornando l’attributo chiave *abilitato* per **falso**.

Per ulteriori informazioni sui tipi di dati, vedere [Tipi di dati](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Per ulteriori informazioni su altri attributi possibili, vedere la [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Tag della chiave

È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Azure Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

>[!Note]
>I tag sono leggibili da un chiamante se hanno l’autorizzazione *elenca* o *ottieni* a tale tipo di oggetto, chiave, segreto o certificato.

###  <a name="BKMK_KeyAccessControl"></a> Controllo di accesso per le chiavi

Controllo di accesso per le chiavi gestite da Key Vault fornito a livello di un Key Vault che funge da contenitore delle chiavi. Vi è un criterio di controllo di accesso per le chiavi distinto dal criterio di controllo di accesso per i segreti nello stesso Key Vault. Gli utenti possono creare uno o più insiemi di credenziali per le chiavi e sono necessari per mantenere una segmentazione di scenario e una gestione delle chiavi appropriate. Il controllo degli accessi per le chiavi è indipendente dal controllo di accesso per i segreti.  

Le autorizzazioni seguenti possono essere concesse, su base principale utente/servizio, nella voce di controllo di accesso chiavi in un insieme di credenziali. Queste autorizzazioni rispecchiano fedelmente le operazioni consentite su un oggetto chiave:  

-   *crea*: creare nuove chiavi
-   *ottieni*: leggere la parte pubblica di una chiave e i relativi attributi
-   *elenca*: elencare le chiavi o le versioni di una chiave archiviata in un insieme di credenziali delle chiavi
-   *aggiorna*: aggiornare gli attributi per una chiave
-   *elimina*: eliminare l'oggetto chiave
-   *firma*: usare la chiave per firmare digest
-   *verifica*: usare la chiave per firmare digest
-   *esegui il wrapping della chiave*: usare la chiave per custodire una chiave simmetrica
-   *non eseguire il wrapping della chiave*: usare la chiave per custodire una chiave simmetrica
-   *crittografa*: usare la chiave per proteggere una sequenza arbitraria di byte
-   *decrittografa*: usare la chiave per non custodire una sequenza arbitraria di byte
-   *importa*: importare una chiave in un insieme di credenziali delle chiavi
-   *backup*: backup di una chiave in un insieme di credenziali delle chiavi
-   *ripristina*: ripristinare una chiave precedentemente sottoposta a backup a un insieme di credenziali delle chiavi
-   *tutte*: tutte le autorizzazioni

## <a name="key-vault-secrets"></a>Segreti Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Utilizzo dei segreti

I segreti in Azure Key Vault sono sequenze di ottetti con una dimensione massima di 25 KB ciascuna. Il servizio Azure Key Vault non fornisce alcuna semantica per i segreti; accetta semplicemente i dati, esegue la crittografia e li archivia, restituendo un identificatore di segreto, "id", che può essere usato per recuperare il segreto in un secondo momento.  

Per i dati altamente sensibili, i client devono prendere in considerazione livelli aggiuntivi di protezione per i dati archiviati in Azure Key Vault; ad esempio per i dati pre-crittografati utilizzando una chiave di protezione separata.  

Azure Key Vault supporta anche un campo contentType per i segreti. I client possono specificare il tipo di contenuto, "contentType", di un segreto per facilitare l'interpretazione dei dati dei segreti quando vengono recuperati. Il campo può contenere al massimo 255 caratteri. Non sono presenti valori predefiniti. L'utilizzo consigliato è come un hint per l'interpretazione dei dati segreti. Ad esempio, un'implementazione può archiviare password e certificati come segreti quindi usare questo campo per indicare quali fra essi. Non sono presenti valori predefiniti.  

###  <a name="BKMK_SecretAttrs"></a> Attributi dei segreti

Oltre ai dati dei segreti,è possibile specificare gli attributi seguenti:  

- *nbf*: il valore predefinito IntDate facoltativo è **per sempre**. L’attributo *exp* (ora di scadenza) identifica l'ora di scadenza in cui o successiva alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). L'elaborazione dell’attributo *exp* richiede che la data/ora corrente SIA precedente a data/ora di scadenza elencata nell’attributo *exp*. Azure Key Vault di Azure PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  
- *nbf*: il valore predefinito IntDate facoltativo è **adesso**. L’attributo *nbf* (non precedente) identifica l'ora precedente alla quale i dati dei segreti NON DEVONO essere recuperati, tranne che in [situazioni particolari](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). L'elaborazione dell’attributo *nbf* richiede che la data/ora corrente SIA successiva o uguale a data/ora non precedente elencata nell’attributo *nbf*. Azure Key Vault di Azure PUÒ concedere un minimo margine temporale, in genere non più di qualche minuto, per tenere conto dello sfasamento di orario. Il valore DEVE essere un numero contenente un valore IntDate.  
- *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo specifica se i dati dei segreti possono essere recuperati. L’attributo abilitato viene usato in combinazione con ed *exp* quando si verifica un'operazione tra ed exp, sarà consentito solo se abilitato è impostato su **vero**. Le operazioni all'esterno della finestra *nbf* ed *exp* non sono impostate automaticamente non consentiti, ad eccezione di [situazioni particolari](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta che include gli attributi dei segreti:  

- *creato*: IntDate facoltativo. L’attributo creato indica quando è stata creata questa versione del segreto. Questo valore è null per i segreti creati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.  
- *aggiornato*: IntDate facoltativo. L’attributo aggiornato indica quando è stata aggiornata questa versione del segreto. Questo valore è null peri segreti aggiornati prima dell'aggiunta di questo attributo. Il valore deve essere un numero contenente un valore IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operazioni controllate in base a data e ora

L’operazione **ottieni** di un segreto funzionerà per i segreti non ancora validi e scaduti, all'esterno della finestra *nbf* / *exp*. La chiamata dell’operazione **ottieni** di un segreto, per una chiave privata non ancora valida, può essere utilizzata per scopi di test. Il recupero (l’**ottenimento**) di un segreto scaduto può essere utilizzato per le operazioni di ripristino.

Per ulteriori informazioni sui tipi di dati, vedere [Tipi di dati](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Controllo di accesso per i segreti

Controllo di accesso per i segreti gestiti da Key Vault Azure fornito a livello di un Key Vault che funge da contenitore di tali segreti. Vi è un criterio di controllo di accesso per i segreti distinti dal criterio di controllo di accesso per i segreti nello stesso Key Vault. Gli utenti possono creare uno o più insiemi di credenziali per i segreti e sono necessari per mantenere una segmentazione di scenario e una gestione dei segreti appropriati. Il controllo degli accessi per i segreti è indipendente dal controllo di accesso per le chiavi.  

Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

-   *imposta*: creare nuovi segreti  
-   *ottieni*: legge un segreto  
-   *elenca*: elencare i segreti o le versioni di un segreto archiviato in un Key Vault  
-   *elimina*: eliminare il segreto  
-   *tutte*: tutte le autorizzazioni  

Per ulteriori informazioni sull'uso dei segreti, vedere [operazioni relative ai segreti](/rest/api/keyvault/secret-operations).  

###  <a name="BKMK_SecretTags"></a> Tag dei segreti  
È possibile specificare metadati aggiuntivi specifici dell'applicazione sotto forma di tag. Azure Key Vault supporta fino a 15 tag, ognuno dei quali può avere un nome di 256 caratteri e un valore di 256 caratteri.  

>[!Note]
>I tag sono leggibili da un chiamante se hanno l’autorizzazione *elenca* o *ottieni* a tale tipo di oggetto, chiave, segreto o certificato.

## <a name="key-vault-certificates"></a>Certificati Key Vault

La funzionalità Certificati dell'insieme di credenziali delle chiavi supporta la gestione di certificati X509 e consente di eseguire le operazioni seguenti:  

-   Il proprietario di un certificato può creare un certificato tramite un processo di creazione dell'insieme di credenziali delle chiavi o tramite l'importazione di un certificato esistente. Sono inclusi i certificati autofirmati e i certificati generati dall'autorità di certificazione.
-   Il proprietario di un certificato dell'insieme di credenziali delle chiavi può implementare l'archiviazione sicura e la gestione di certificati X509 senza interagire con materiale della chiave privata.  
-   Il proprietario di un certificato può creare criteri che guidano l'insieme di credenziali delle chiavi nella gestione del ciclo di vita di un certificato.  
-   I proprietari di un certificato possono specificare informazioni sul contatto per notificare eventi sul ciclo di vita di un certificato, come la scadenza e il rinnovo.  
-   Viene supportato il rinnovo automatico con autorità di certificazione selezionate, ad esempio provider di certificati X509 / Autorità di certificazione partner dell'insieme di credenziali delle chiavi.

>[!Note]
>Sono ammessi anche i provider e le autorità di certificazione senza partnership, ma non supportano la funzionalità di rinnovo automatico.

###  <a name="BKMK_CompositionOfCertificate"></a> Componenti di un certificato

Quando viene creato un certificato Key Vault, vengono creati anche una chiave e un segreto indirizzabili con lo stesso nome. La chiave Key Vault consente operazioni di chiave e il segreto Key Vault consente il recupero del valore del certificato come segreto. Un certificato Key Vault contiene inoltre metadati del certificato x509 pubblico.  

L'identificatore e la versione dei certificati sono simili a quelli delle chiavi e segreti. Una versione specifica di una chiave e del segreto indirizzabili creati con la versione di certificato Key Vault è disponibile nella risposta di certificato Key Vault.
 
![I certificati sono oggetti complessi](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Chiave esportabile o non esportabile

Quando viene creato un certificato Key Vault, questo può essere recuperato dal segreto di indirizzabile con la chiave privata nel formato PFX o PEM se i criteri utilizzati per creare il certificato indicano che la chiave è esportabile. Se i criteri utilizzati per creare il certificato Key Vault indicano la chiave come non esportabile, la chiave privata non è una parte del valore quando viene recuperata come un segreto.  

La chiave indirizzabile diventa maggiormente pertinente con certificati KV non esportabili. Le operazioni della chiave KV indirizzabile sono mappate dal campo *keyusage* dei criteri del certificato KV utilizzato per creare il certificato KV.  

Sono supportati due tipi di chiave *RSA* oppure *RSA HSM* con certificati. La possibilità di essere esportabile è consentita solo con RSA, non è supportata da RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Tag e attributi del certificato

Oltre ai metadati del certificato, a una chiave indirizzabile e a un segreto indirizzabile, anche un certificato Key Vault contiene gli attributi e tag.  

#### <a name="attributes"></a>Attributi

Gli attributi di certificato si riflettono negli attributi della chiave e del segreto indirizzabile creati quando viene creato KV certificato.  

Un certificato Key Vault ha gli attributi seguenti:  

-   *abilitato*: il valore predefinito booleano facoltativo è **vero**. Questo attributo può essere specificato per indicare se i dati del certificato possono essere recuperati come segreti o eseguibili come chiave. Questo viene usato in combinazione con *nbf* ed *exp* quando si verifica un'operazione tra *nbf* ed *exp*, sarà consentito solo se abilitato è impostato su vero. Le operazioni all'esterno della finestra di *nbf* ed *exp* non sono consentite automaticamente.  

Sono disponibili altri attributi di sola lettura che sono inclusi in una risposta:

-   *creato*: IntDate: indica quando è stata creata questa versione del certificato.  
-   *aggiornato*: IntDate: indica quando è stata aggiornata questa versione del certificato.  
-   *exp*: IntDate: contiene il valore della data di scadenza del certificato x509.  
-   *nbf*: IntDate: contiene il valore della data del certificato x509.  

> [!Note] 
> Se un certificato in Key Vault scade, la chiave e il segreto indirizzabili sono inutilizzabili.  

#### <a name="tags"></a>Tag

 Dizionario specificato dal client di coppie chiave-valore, simile ai tag delle chiavi e dei segreti.  

 > [!Note]
> I tag sono leggibili da un chiamante se hanno l’autorizzazione *elenca* o *ottieni* a tale tipo di oggetto, chiave, segreto o certificato.

###  <a name="BKMK_CertificatePolicy"></a> Criteri dei certificati

Un criterio di certificato contiene informazioni sulla creazione e la gestione del ciclo di vita di un certificato KV. Quando un certificato con chiave privata viene importato nell'insieme di credenziali delle chiavi, viene creato un criterio predefinito leggendo il certificato x509.  

Quando viene creato un certificato KV da zero, Key Vault deve ricevere un criterio per specificare le modalità di creazione di questa versione del certificato KV o della versione successiva del certificato KV. Dopo aver stabilito un criterio, questo non richiesto con le successive operazioni di creazione per creare le versioni successive del certificato KV.  

È disponibile una sola istanza di un criterio per tutte le versioni di un certificato KV.  

In generale, i criteri dei certificati contengono quanto segue:  

-   Proprietà del certificato X509: contiene il nome oggetto, nomi alternativi dell’oggetto e così via. utilizzata per creare una richiesta di certificato x509.  
-   Proprietà della chiave: contiene il tipo di chiave, la lunghezza della chiave e i campi chiave della chiave esportabile e riutilizza. Questi campi indicare all’insieme di credenziali delle chiavi come generare una chiave.  
-   Proprietà del segreto: contiene le proprietà del segreto, ad esempio tipo di contenuto del segreto indirizzabile per generare il valore del segreto, al fine di recuperare il certificato come segreto.  
-   Azioni di durata: contiene le azioni di durata per il certificato KV. Ogni azione di durata contiene:  

     - Trigger: specificato tramite i giorni precedenti alla scadenza o alla percentuale dell’intervallo di vita  

     - Azione: specificare il tipo di azione *emailContacts* o *autoRenew*  

-   Autorità di certificazione: i parametri relativi all’autorità di certificazione del certificato da utilizzare per l’emissione dei certificati x509.  
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

###  <a name="BKMK_CertificateIssuer"></a> Autorità di certificazione

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

Per ulteriori informazioni sulla creazione di oggetti di autorità di certificazione dal portale di certificati, vedere il [blog dei certificati Key Vault](http://aka.ms/kvcertsblog)  

Key Vault consente di creare più oggetti di autorità di certificazione con una differente configurazione del provider dell'autorità di certificazione. Dopo che è stato creato un oggetto di autorità di certificazione, è possibile fare riferimento al suo nome in uno o più criteri del certificato KV. Fare riferimento all'oggetto dell'autorità di certificazione indica a Key Vault di usare la configurazione come specificato nell'oggetto dell'autorità di certificazione quando si richiede il certificato x509 dal provider di autorità di certificazione durante la creazione e il rinnovo di certificati.  

Gli oggetti di autorità di certificazione vengono creati nell'insieme di credenziali e possono essere utilizzati solo con certificati KV nello stesso insieme di credenziali.  

###  <a name="BKMK_CertificateContacts"></a> Contatti relativi al certificato

I contatti relativi al certificato contengono le informazioni di contatto per inviare notifiche attivate da eventi di durata dei certificati. Le informazioni dei contatti vengono condivise da tutti i certificati nell'insieme di credenziali delle chiavi. Viene inviata una notifica a tutti i contatti specificati per un evento per qualsiasi certificato presente nell'insieme di credenziali delle chiavi.  

Se i criteri del certificato sono impostato per il rinnovo automatico, viene inviata una notifica sugli eventi seguenti.  

-   Prima del rinnovo del certificato
-   Dopo il rinnovo del certificato, che indica se il certificato è stato rinnovato, o se si è verificato un errore, richiede il rinnovo manuale del certificato.  

 Se i criteri del certificato sono impostati per essere manualmente rinnovati (solo posta elettronica), viene inviata una notifica quando giunge il momento di rinnovare il certificato.  

###  <a name="BKMK_CertificateAccessControl"></a> Controllo di accesso di certificato

 Il controllo di accesso per i certificati gestiti da Key Vault e fornito a livello di un Key Vault che funge da contenitore di tali certificati. Vi è un criterio di controllo di accesso per i certificati distinto dal criterio di controllo di accesso per le chiavi e i segreti nello stesso Key Vault. Gli utenti possono creare uno o più insiemi di credenziali per i certificati e sono necessari per mantenere una segmentazione di scenario e una gestione dei certificati appropriati.  

 Le autorizzazioni seguenti sono utilizzabili, su base principale, nella voce di controllo di accesso dei segreti in un insieme di credenziali delle chiavi e riflettono fedelmente le operazioni consentite su un oggetto segreto:  

-   *ottieni*: consente di ottenere qualsiasi versione di un certificato o la versione corrente di un certificato 
-   *elenca*: consente di elencare qualsiasi certificato o versione di un certificato  
-   *elimina*: consente l'eliminazione di un certificato, i criterio e tutte le versioni corrispondenti  
-   *crea*: consente di creare un certificato Key Vault.  
-   *importa*: consente l'importazione del materiale di certificato in un certificato Key Vault.  
-   *aggiorna*: consente l'aggiornamento di un certificato.  
-   *manageconnects*: consente la gestione dei contatti del certificato Key Vault  
-   *getissuers*: consente di ottenere l’autorità di certificazione del certificato  
-   *listissuers*: consente di elencare le autorità di certificazione del certificato  
-   *setissuers*: consente di creare o aggiornare l’autorità di certificazione Key Vault  
-   *deleteissuers*: consente di eliminare le autorità di certificazione Key Vault  
-   *tutti*: concede tutte le autorizzazioni  

## <a name="additional-information-for-certificates"></a>Informazioni aggiuntive per i certificati

- [Certificati e criteri](/rest/api/keyvault/certificates-and-policies)
- [Autorità di certificazione](/rest/api/keyvault/certificate-issuers)
- [Contatti relativi al certificato](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>Vedere anche

- [Autenticazione, richieste e risposte](authentication-requests-and-responses.md)
- [Versioni di Key Vault](key-vault-versions.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](/azure/key-vault/key-vault-developers-guide)
