---
title: 'Crittografia: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 96e74371fe51a8050a91c86215e3eefab07bbed8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-cryptography--mitigations"></a>Infrastruttura di sicurezza: crittografia - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Applicazione Web** | <ul><li>[Usare solo crittografie a blocchi simmetriche e lunghezze di chiave approvate](#cipher-length)</li><li>[Usare modalità di crittografia a blocchi approvate e vettori di inizializzazione per crittografie simmetriche](#vector-ciphers)</li><li>[Usare riempimenti, lunghezze di chiave e algoritmi asimmetrici approvati](#padding)</li><li>[Usare generatori di numeri casuali approvati](#numgen)</li><li>[Non usare crittografie di flusso simmetriche](#stream-ciphers)</li><li>[Usare algoritmi MAC/HMAC/hash con chiave](#mac-hash)</li><li>[Usare solo funzioni hash crittografiche approvate](#hash-functions)</li></ul> |
| **Database** | <ul><li>[Usare algoritmi di crittografia avanzata per crittografare i dati nel database](#strong-db)</li><li>[I pacchetti SSIS devono essere crittografati ed essere con firma digitale](#ssis-signed)</li><li>[Aggiungere la firma digitale alle entità a protezione diretta del database critiche](#securables-db)</li><li>[Usare EKM di SQL Server per proteggere le chiavi di crittografia](#ekm-keys)</li><li>[Usare la funzionalità AlwaysEncrypted se le chiavi di crittografia non devono essere rivelate al motore di database](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Archiviare le chiavi di crittografia in modo sicuro nel dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway IoT cloud** | <ul><li>[Generare una chiave simmetrica casuale sufficientemente lunga per l'autenticazione nell'hub IoT](#random-hub)</li></ul> | 
| **Client Dynamics CRM Mobile** | <ul><li>[Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede di usare il PIN e consente la cancellazione remota](#pin-remote)</li></ul> | 
| **Client Dynamics CRM per Outlook** | <ul><li>[Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede PIN/password/blocco automatico e crittografa tutti i dati (ad esempio, Bitlocker)](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Assicurarsi che venga eseguito il rollover delle chiavi di firma quando si usa Identity Server](#rolled-server)</li><li>[Assicurarsi che vengano usati un ID client e un segreto client con crittografia complessa in Identity Server](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Usare solo crittografie a blocchi simmetriche e lunghezze di chiave approvate

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>I prodotti devono usare solo le crittografie a blocchi simmetriche e le lunghezze di chiave associate esplicitamente approvate dal consulente per la crittografia dell'organizzazione. Gli algoritmi simmetrici approvati da Microsoft includono le crittografie a blocchi seguenti:</p><ul><li>Per il codice nuovo, sono accettabili AES-128, AES-192 e AES-256.</li><li>Per la compatibilità con le versioni precedenti del codice esistente, è accettabile 3DES a tre chiavi.</li><li>Per i prodotti che usano le crittografie a blocchi simmetriche:<ul><li>Per il codice nuovo, è necessario Advanced Encryption Standard (AES).</li><li>Triple Data Encryption Standard (3DES) a tre chiavi è consentito nel codice esistente per la compatibilità con le versioni precedenti.</li><li>Tutte le altre crittografie a blocchi, incluse RC2, DES, 3DES a 2 chiavi, DESX e Skipjack, possono essere usate solo per decrittografare i dati precedenti e devono essere sostituite se usate per la crittografia.</li></ul></li><li>Per gli algoritmi di crittografia a blocchi simmetrici, è necessaria una lunghezza minima della chiave di 128 bit. L'unico algoritmo di crittografia a blocchi consigliato per il codice nuovo è AES (AES-128, AES-192 e AES-256 sono tutti accettabili)</li><li>3DES a tre chiavi è attualmente accettabile se è già in uso nel codice esistente. È consigliata la transizione ad AES. DES, DESX, RC2 e Skipjack non sono più considerati sicuri. Questi algoritmi possono essere usati solo per decrittografare i dati esistenti ai fini della compatibilità con le versioni precedenti e i dati devono essere crittografati di nuovo con una crittografia a blocchi consigliata.</li></ul><p>Si noti che tutte le crittografie a blocchi simmetriche devono essere usate con una modalità di crittografia approvata, che richiede l'uso di un vettore di inizializzazione appropriato. Un vettore di inizializzazione appropriato è in genere un numero casuale, mai un valore costante.</p><p>L'uso di algoritmi di crittografia legacy o comunque non approvati e di lunghezze di chiavi inferiori per la lettura dei dati esistenti (contrariamente alla scrittura di nuovi dati) può essere consentito dopo la revisione da parte del team di crittografia dell'organizzazione. È tuttavia necessario prevedere un'eccezione per questo requisito. Nelle distribuzioni aziendali inoltre si deve considerare per i prodotti la possibilità di avvisare gli amministratori quando viene usata una crittografia debole per leggere i dati. Tali avvisi devono essere descrittivi e operativi. In alcuni casi, può essere appropriato che i Criteri di gruppo controllino l'uso della crittografia debole.</p><p>Algoritmi .NET consentiti per la flessibilità crittografica gestita (in ordine di preferenza):</p><ul><li>AesCng (conforme allo standard FIPS)</li><li>AuthenticatedAesCng (conforme allo standard FIPS)</li><li>AESCryptoServiceProvider (conforme allo standard FIPS)</li><li>AESManaged (non conforme allo standard FIPS)</li></ul><p>Si noti che nessuno di questi algoritmi può essere specificato tramite i metodi `SymmetricAlgorithm.Create` o `CryptoConfig.CreateFromName` senza apportare modifiche al file machine.config. Tenere anche presente che AES nelle versioni di .NET precedenti a .NET 3.5 viene denominato `RijndaelManaged` e che `AesCng` e `AuthenticatedAesCng` sono disponibili in CodePlex e richiedono CNG nel sistema operativo sottostante.</p>

## <a id="vector-ciphers"></a>Usare modalità di crittografia a blocchi approvate e vettori di inizializzazione per crittografie simmetriche

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Tutte le crittografie a blocchi simmetriche devono essere usate con una modalità di crittografia a blocchi approvata. Le sole modalità approvate sono CBC e CTS. In particolare, è consigliabile evitare la modalità operativa ECB (Electronic Code Book). Per usare ECB, è necessaria la revisione da parte del team di crittografia dell'organizzazione. Ogni utilizzo di OFB, CFB, CTR, CCM e GCM o di altre modalità di crittografia deve essere esaminato dal team di crittografia dell'organizzazione. Con il riutilizzo dello stesso vettore di inizializzazione con crittografie a blocchi nelle "modalità di crittografia di flusso", ad esempio CTR, i dati crittografati potrebbero essere rivelati. Tutte le crittografie a blocchi simmetriche devono anche essere usate con un vettore di inizializzazione appropriato. Un vettore di inizializzazione appropriato è un numero casuale con una crittografia complessa, mai un valore costante. |

## <a id="padding"></a>Usare riempimenti, lunghezze di chiave e algoritmi asimmetrici approvati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>L'uso di algoritmi di crittografia vietati costituisce un grave rischio per la sicurezza dei prodotti e deve essere evitato. I prodotti devono usare solo gli algoritmi di crittografia e le lunghezze di chiave e i riempimenti associati esplicitamente approvati dal team di crittografia dell'organizzazione.</p><ul><li>**RSA**: può essere usato per la crittografia, lo scambio di chiave e la firma. La crittografia RSA deve usare solo le modalità di riempimento OAEP o RSA-KEM. Il codice esistente può usare la modalità di riempimento PKCS #1 v1.5 solo per la compatibilità. L'uso del riempimento null è esplicitamente vietato. Per il codice nuovo sono necessarie chiavi >= 2048 bit. Il codice esistente può supportare chiavi < 2048 bit solo per la compatibilità con le versioni precedenti dopo una revisione da parte del team di crittografia dell'organizzazione. Le chiavi < 1024 bit possono essere usate solo per decrittografare/verificare i dati precedenti e devono essere sostituite se usate per operazioni di crittografia o di firma.</li><li>**ECDSA**: può essere usato solo per la firma. Per il codice nuovo è necessario ECDSA con chiavi >=256 bit. Le firme basate su ECDSA devono usare una delle tre curve approvate da NIST (P-256, P-384 o P521). Le curve che sono state analizzate a fondo possono essere usate solo dopo una revisione da parte del team di crittografia dell'organizzazione.</li><li>**ECDH**: può essere usato solo per lo scambio di chiave. Per il codice nuovo è necessario ECDH con chiavi >=256 bit. Lo scambio di chiave basato su ECDH deve usare una delle tre curve approvate da NIST (P-256, P-384 o P521). Le curve che sono state analizzate a fondo possono essere usate solo dopo una revisione da parte del team di crittografia dell'organizzazione.</li><li>**DSA**: può essere accettabile dopo la revisione e l'approvazione da parte del team di crittografia dell'organizzazione. Contattare il consulente per la sicurezza per pianificare una revisione da parte del team di crittografia dell'organizzazione. Se l'uso di DSA è approvato, tenere presente che sarà necessario impedire l'uso di chiavi con una lunghezza inferiore a 2048 bit. CNG supporta chiavi di lunghezza pari e superiore a 2048 bit a partire da Windows 8.</li><li>**Diffie-Hellman**: può essere usato solo per la gestione delle chiavi della sessione. Per il codice nuovo sono necessarie chiavi di lunghezza >= 2048 bit. Il codice esistente può supportare chiavi di lunghezza < 2048 bit solo per la compatibilità con le versioni precedenti dopo una revisione da parte del team di crittografia dell'organizzazione. Le chiavi < 1024 bit non possono essere usate.</li><ul>

## <a id="numgen"></a>Usare generatori di numeri casuali approvati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>I prodotti devono usare generatori di numeri casuali approvati. Le funzioni pseudocasuali, ad esempio la funzione runtime C rand, la classe .NET Framework System.Random o le funzioni di sistema come GetTickCount, non devono quindi mai essere usate in tale codice. L'uso dell'algoritmo DUAL_EC_DRBG (Dual Elliptic Curve Deterministic Random Bit Generator) è proibito.</p><ul><li>**CNG**: BCryptGenRandom. È consigliato l'uso del flag BCRYPT_USE_SYSTEM_PREFERRED_RNG a meno che il chiamante non possa essere eseguito con un IRQL maggiore di 0, ovvero PASSIVE_LEVEL.</li><li>**CAPI**: cryptGenRandom.</li><li>**Win32/64**: RtlGenRandom (le nuove implementazioni devono usare BCryptGenRandom o CryptGenRandom) * rand_s * SystemPrng (per la modalità kernel)</li><li>**.NET**: RNGCryptoServiceProvider o RNGCng.</li><li>**Applicazioni Windows Store**: Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom o .GenerateRandomNumber.</li><li>**Apple OS X (10.7+)/iOS(2.0+)**: int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t *byte)</li><li>**Apple OS X (<10.7)-** Usare /dev/random per recuperare numeri casuali</li><li>**Java(compreso il codice Java di Google per Android) -** classe java.security.SecureRandom. Tenere presente che, per Android 4.3 (Jelly Bean), gli sviluppatori devono seguire la soluzione alternativa consigliata da Android e aggiornare le applicazioni per inizializzare il generatore PRNG con l'entropia da /dev/urandom o /dev/random.</li></ul>|

## <a id="stream-ciphers"></a>Non usare crittografie di flusso simmetriche

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Non devono essere usate crittografie di flusso simmetriche, ad esempio RC4. Invece delle crittografie di flusso simmetriche, i prodotti devono usare una crittografia a blocchi, in particolare AES con una lunghezza di chiave di almeno 128 bit. |

## <a id="mac-hash"></a>Usare algoritmi MAC/HMAC/hash con chiave

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>I prodotti devono usare solo algoritmi MAC (Message Authentication Code) o HMAC (Hash Message Authentication Code) approvati.</p><p>Un codice MAC è un'informazione allegata a un messaggio che consente ai destinatari di verificare sia l'autenticità del mittente che l'integrità del messaggio con una chiave privata. L'uso di codice MAC basato su hash ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) o di codice [MAC basato sulla crittografia a blocchi](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) è consentito purché anche tutti gli algoritmi di crittografia simmetrica o hash sottostanti siano approvati per l'uso. Attualmente sono inclusi i codici MAC basati sulla crittografia a blocchi CMAC/OMAC1 e OMAC2 (basati su AES) e le funzioni HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 e HMAC-SHA512).</p><p>L'uso di HMAC-SHA1 può essere consentito per la compatibilità con la piattaforma, ma sarà necessario prevedere un'eccezione a questa procedura e superare la revisione del team di crittografia dell'organizzazione. Non è consentito il troncamento dei codici HMAC a meno di 128 bit. L'uso dei metodi dei clienti per l'hash di una chiave e dei dati non è approvato e deve superare la revisione del team di crittografia dell'organizzazione prima di poter essere adottato.</p>|

## <a id="hash-functions"></a>Usare solo funzioni hash crittografiche approvate

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>I prodotti devono usare la famiglia SHA-2 di algoritmi (SHA256, SHA384 e SHA512). Se è necessario un hash più breve, ad esempio una lunghezza di output di 128 bit, per la corrispondenza con una struttura di dati progettata per il più breve hash MD5, i team dei prodotti possono troncare uno degli hash SHA-2 (in genere, SHA256). Si noti che SHA384 è una versione troncata di SHA512. Il troncamento di hash crittografici per motivi di sicurezza a meno di 128 bit non è consentito. Il codice nuovo non deve usare gli algoritmi hash MD2, MD4, MD5, SHA-0, SHA-1 o RIPEMD. Da un punto di vista computazionale, le collisioni di hash sono possibili per questi algoritmi, che li interrompono in modo efficace.</p><p>Algoritmi hash .NET consentiti per la flessibilità crittografica gestita (in ordine di preferenza):</p><ul><li>SHA512Cng (conforme allo standard FIPS)</li><li>SHA384Cng (conforme allo standard FIPS)</li><li>SHA256Cng (conforme allo standard FIPS)</li><li>SHA512Managed (non conforme allo standard FIPS). Usare SHA512 come nome di algoritmo nelle chiamate a HashAlgorithm.Create o CryptoConfig.CreateFromName</li><li>SHA384Managed (non conforme allo standard FIPS). Usare SHA384 come nome di algoritmo nelle chiamate a HashAlgorithm.Create o CryptoConfig.CreateFromName</li><li>SHA256Managed (non conforme allo standard FIPS). Usare SHA256 come nome di algoritmo nelle chiamate a HashAlgorithm.Create o CryptoConfig.CreateFromName</li><li>SHA512CryptoServiceProvider (conforme allo standard FIPS)</li><li>SHA256CryptoServiceProvider (conforme allo standard FIPS)</li><li>SHA384CryptoServiceProvider (conforme allo standard FIPS)</li></ul>| 

## <a id="strong-db"></a>Usare algoritmi di crittografia avanzata per crittografare i dati nel database

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Scelta di un algoritmo di crittografia](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Passaggi** | Gli algoritmi di crittografia definiscono trasformazioni dei dati che non possono essere facilmente invertite da utenti non autorizzati. SQL Server consente ad amministratori e sviluppatori di scegliere tra diversi algoritmi, inclusi DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 a 128 bit, DESX, AES a 128 bit, AES a 192 bit e AES a 256 bit. |

## <a id="ssis-signed"></a>I pacchetti SSIS devono essere crittografati ed essere con firma digitale

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Identificare l'origine dei pacchetti con firme digitali](https://msdn.microsoft.com/library/ms141174.aspx), [Mitigazione di minacce e vulnerabilità (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Passaggi** | L'origine di un pacchetto è la persona o l'organizzazione che ha creato il pacchetto. Eseguire un pacchetto da un'origine sconosciuta o non attendibile può essere rischioso. Per impedire la manomissione non autorizzata dei pacchetti SSIS, è consigliabile usare le firme digitali. Per garantire la riservatezza dei pacchetti durante l'archiviazione o il transito, i pacchetti SSIS devono anche essere crittografati. |

## <a id="securables-db"></a>Aggiungere la firma digitale alle entità a protezione diretta del database critiche

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Passaggi** | Qualora sia necessario verificare l'integrità di un'entità a protezione diretta del database critica, è consigliabile usare le firme digitali. Le entità a protezione diretta del database critiche, ad esempio stored procedure, funzioni, assembly o trigger, possono essere con firma digitale. Ecco un esempio di quando ciò può essere utile: si supponga che un fornitore di software indipendente (ISV) abbia fornito supporto per un software distribuito a uno dei clienti. Prima di fornire il supporto, l'ISV vuole assicurarsi che un'entità a protezione diretta del database nel software non sia stata manomessa per errore o intenzionalmente. Se l'entità a protezione diretta è con firma digitale, l'ISV può verificarne la firma digitale e convalidarne l'integrità.| 

## <a id="ekm-keys"></a>Usare EKM di SQL Server per proteggere le chiavi di crittografia

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Extensible Key Management (EKM) di SQL Server](https://msdn.microsoft.com/library/bb895340), [Extensible Key Management con Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Passaggi** | Extensible Key Management di SQL Server consente di archiviare le chiavi di crittografia che proteggono i file di database in un dispositivo off-box, ad esempio una smart card, un dispositivo USB o un modulo EKM/HSM. Ciò consente anche la protezione dei dati agli amministratori del database, tranne ai membri del gruppo di amministratori di sistema. I dati possono essere crittografati usando le chiavi di crittografia a cui ha accesso solo l'utente del database nel modulo EKM/HSM esterno. |

## <a id="keys-engine"></a>Usare la funzionalità AlwaysEncrypted se le chiavi di crittografia non devono essere rivelate al motore di database

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | SQL Azure, locale |
| **Attributes (Attributi) (Attributi)**              | Versione SQL: V12, MsSQL2016 |
| **Riferimenti**              | [Always Encrypted (Motore di database)](https://msdn.microsoft.com/library/mt163865) |
| **Passaggi** | Always Encrypted è una funzionalità progettata per proteggere i dati sensibili, ad esempio i numeri di carta di credito o i numeri di identificazione nazionale, come i codici fiscali, archiviati nei database SQL di Azure o nei database di SQL Server. La funzionalità Always Encrypted consente ai client di crittografare i dati sensibili nelle applicazioni client e di non rivelare mai le chiavi di crittografia al motore di database (database SQL o SQL Server). Di conseguenza, Always Encrypted consente di separare i proprietari dei dati (che possono visualizzarli) e le persone incaricate della gestione dei dati (che però non devono poter accedere ai dati). |

## <a id="keys-iot"></a>Archiviare le chiavi di crittografia in modo sicuro nel dispositivo IoT

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Sistema operativo dispositivo: Windows IoT Core, connettività dispositivo: Azure IoT SDK per dispositivi |
| **Riferimenti**              | [TPM on Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm) (TPM in Windows IoT Core), [Set up TPM on Windows IoT Core](https://developer.microsoft.com/windows/iot/win10/setuptpm) (Configurare TPM in Windows IoT Core), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) (TPM di Azure IoT SDK per dispositivi) |
| **Passaggi** | Archiviare le chiavi private dei certificati o simmetriche in modo sicuro in una risorsa di archiviazione hardware protetta, ad esempio chip di smart card o TPM. Windows 10 IoT Core supporta l'uso di un modulo TPM. Sono diversi i moduli TPM compatibili che possono essere usati: https://developer.microsoft.com/windows/iot/win10/tpm. È consigliabile usare un modulo TPM firmware o discreto. Un modulo TPM software deve essere usato solo a scopo di sviluppo e test. Quando un modulo TPM è disponibile ed è stato effettuato il provisioning delle chiavi, il codice che genera il token deve essere scritto senza codificare le informazioni sensibili. | 

### <a name="example"></a>Esempio
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Come si può osservare, la chiave primaria del dispositivo non è presente nel codice. Viene invece archiviata nello slot 0 del modulo TPM. Il dispositivo TPM genera un token di firma di accesso condiviso temporaneo che viene quindi usato per connettersi all'hub IoT. 

## <a id="random-hub"></a>Generare una chiave simmetrica casuale sufficientemente lunga per l'autenticazione nell'hub IoT

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Opzione gateway: Hub IoT di Azure |
| **Riferimenti**              | N/D  |
| **Passaggi** | L'hub IoT contiene un registro di identità del dispositivo e durante il provisioning genera automaticamente una chiave simmetrica casuale. È consigliabile usare questa funzionalità del registro di identità dell'hub IoT di Azure per generare la chiave usata per l'autenticazione. L'hub IoT consente anche di specificare una chiave durante la creazione del dispositivo. Se viene generata una chiave all'esterno dell'hub IoT durante il provisioning del dispositivo, è consigliabile creare una chiave simmetrica casuale o almeno a 256 bit. |

## <a id="pin-remote"></a>Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede di usare il PIN e consente la cancellazione remota

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Client Dynamics CRM Mobile | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede di usare il PIN e consente la cancellazione remota. |

## <a id="bitlocker"></a>Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede PIN/password/blocco automatico e crittografa tutti i dati (ad esempio, Bitlocker)

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Client Dynamics CRM per Outlook | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che sia attivo un criterio di gestione dei dispositivi che richiede PIN/password/blocco automatico e crittografa tutti i dati (ad esempio, Bitlocker). |

## <a id="rolled-server"></a>Assicurarsi che venga eseguito il rollover delle chiavi di firma quando si usa Identity Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Identity Server - Keys, Signatures and Cryptography](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) (Identity Server: chiavi, firme e crittografia) |
| **Passaggi** | Assicurarsi che venga eseguito il rollover delle chiavi di firma quando si usa Identity Server. Il collegamento nella sezione Riferimenti illustra come pianificarlo senza causare interruzioni delle applicazioni basate su Identity Server. |

## <a id="client-server"></a>Assicurarsi che vengano usati un ID client e un segreto client con crittografia complessa in Identity Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Assicurarsi che vengano usati un ID client e un segreto client con crittografia complessa in Identity Server. È consigliabile seguire queste linee guida durante la generazione di un ID e un segreto client:</p><ul><li>Generare un GUID casuale come ID client</li><li>Generare una chiave a 256 bit casuale tramite crittografia come segreto</li></ul>|