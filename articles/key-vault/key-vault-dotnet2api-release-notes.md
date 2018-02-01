---
title: Note sulla versione dell'API .NET 2.x per l'insieme di credenziali delle chiavi | Microsoft Docs
description: Gli sviluppatori .NET useranno questa API per scrivere il codice dell'insieme di credenziali delle chiavi di Azure
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Guida alla migrazione e note sulla versione .NET 2.0 per l'insieme di credenziali delle chiavi di Azure
Le informazioni seguenti consentono la migrazione alla versione 2.0 della libreria di Azure Key Vault per C# e .NET.  È necessario che le app scritte per le versioni precedenti siano aggiornate per supportare la versione più recente.  Queste modifiche sono necessarie per supportare completamente le funzionalità nuove e migliorate, come ad esempio i **certificati Key Vault**.

## <a name="key-vault-certificates"></a>Certificati Key Vault

I certificati Key Vault gestiscono i certificati x509 e supportano i comportamenti seguenti:  

* Creare dei certificati tramite un processo di creazione Key Vault o importare un certificato esistente. Sono inclusi sia i certificati autofirmati che i certificati generati dall'autorità di certificazione.
* Archiviare e gestire in modo sicuro l'archiviazione di certificati x509 senza alcuna interazione tramite materiale con chiave privata.  
* Definire i criteri necessari per l'insieme di credenziali chiave per la gestione del ciclo di vita dei certificati.  
* Fornire informazioni di contatto per eventi inerenti il ciclo di vita, ad esempio avvisi di scadenza e notifiche di rinnovo.  
* Rinnovare automaticamente i certificati con autorità emittenti selezionate (provider di certificati X509 partner per Key Vault e autorità di certificazione).* Supportare certificati forniti da parti (non-partner) e autorità di certificazione altenrative (non supporta il rinnovo automatico).  

## <a name="net-support"></a>Supporto .NET

* **.NET 4.0** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault
* **.NET Framework 4.5.2** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault
* **.NET Standard 1.4** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault

## <a name="namespaces"></a>Spazi dei nomi

* Lo spazio dei nomi per i **modelli** viene cambiato da **Microsoft.Azure.KeyVault** a **Microsoft.Azure.KeyVault.Models**.
* Lo spazio dei nomi **Microsoft.Azure.KeyVault.Internal** viene rimosso.
* Nello spazio dei nomi delle seguenti dipendenze SDK di Azure 

    - **Hyak.Common** è ora **Microsoft.Rest**.
    - **Hyak.Common.Internals** è ora **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Modifiche del tipo

* *Secret* modificato in *SecretBundle*
* *Dictionary* modificato in *IDictionary*
* *List<T>, string []* modificato in *IList<T>*
* *NextList* modificato in *NextPageLink*

## <a name="return-types"></a>Tipi restituiti

* **KeyList** e **SecretList** restituiranno *IPage<T>* anziché *ListKeysResponseMessage*
* L'oggetto **BackupKeyAsync** generato restituirà *BackupKeyResult* che contiene *Valore* (BLOB di backup). Prima veniva eseguito il wrapping del metodo e veniva restituito solo il valore.

## <a name="exceptions"></a>Eccezioni

* *KeyVaultClientException* viene modificato in *KeyVaultErrorException*
* L'errore del servizio è stato modificato da *exception.Error* in *exception.Body.Error.Message*.
* Le informazioni aggiuntive sono rimosse dal messaggio di errore per **[JsonExtensionData]**.

## <a name="constructors"></a>Costruttori

* Invece di accettare *HttpClient* come argomento del costruttore, il costruttore accetta solo *HttpClientHandler* o *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pacchetti scaricati

Quando un client elabora una dipendenza Key Vault, vengono scaricati i pacchetti seguenti:

### <a name="previous-package-list"></a>Elenco dei pacchetti precedenti

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Elenco dei pacchetti correnti

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Modifiche alle classi

* La classe **UnixEpoch** è stata rimossa.
* La classe **Base64UrlConverter** viene rinominata **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Altre modifiche

* A questa versione dell'API è stato aggiunto il supporto per la configurazione dei criteri per i tentativi dell'operazione insieme di credenziali delle chiavi su errori temporanei.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Per le operazioni che restituivano un *insieme di credenziali*, il tipo restituito era una classe che conteneva una proprietà **Vault**. Il tipo restituito è ora *Vault*.
* *PermissionsToKeys* e *PermissionsToSecrets* ora sono *Permissions.Keys* e *Permissions.Secrets*
* Alcune delle modifiche che riguardano i tipi restituiti si applicano anche al piano di controllo.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Il pacchetto viene interrotto a **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** per le operazioni di crittografia.

