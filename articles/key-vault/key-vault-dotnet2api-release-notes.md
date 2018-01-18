---
title: Note sulla versione dell'API .NET 2.x per l'insieme di credenziali delle chiavi | Microsoft Docs
description: Gli sviluppatori .NET useranno questa API per scrivere il codice per Azure Key Vault
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: 5b03f5092ee4236ca3e7b12db37dc47bd6d3a309
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Guida alla migrazione e note sulla versione di .NET 2.0 per Azure Key Vault
Le note e le linee guida seguenti sono destinate agli sviluppatori che usano la libreria .NET / C# di Azure Key Vault. Nel passaggio dalla versione 1.0 alla versione 2.0 sono state apportate alcune modifiche. Per poter però usufruire dei miglioramenti funzionali e delle nuove funzionalità, ad esempio la funzionalità **Certificati Key Vault**, è necessaria un'operazione di migrazione nel codice.

## <a name="key-vault-certificates"></a>Certificati Key Vault

La funzionalità Certificati dell'insieme di credenziali delle chiavi supporta la gestione di certificati X509 e consente di eseguire le operazioni seguenti:  

* Il proprietario di un certificato può creare un certificato tramite un processo di creazione dell'insieme di credenziali delle chiavi o tramite l'importazione di un certificato esistente. Sono inclusi i certificati autofirmati e i certificati generati dall'autorità di certificazione.
* Il proprietario di un certificato dell'insieme di credenziali delle chiavi può implementare l'archiviazione sicura e la gestione di certificati X509 senza interagire con materiale della chiave privata.  
* Il proprietario di un certificato può creare criteri che guidano l'insieme di credenziali delle chiavi nella gestione del ciclo di vita di un certificato.  
* I proprietari di un certificato possono specificare informazioni sul contatto per notificare eventi sul ciclo di vita di un certificato, come la scadenza e il rinnovo.  
* Viene supportato il rinnovo automatico con autorità di certificazione selezionate, ad esempio provider di certificati X509 / Autorità di certificazione partner dell'insieme di credenziali delle chiavi.
  
  * NOTA: sono ammessi anche i provider e le autorità di certificazione senza partnership, ma non supportano la funzionalità di rinnovo automatico.

## <a name="net-support"></a>Supporto .NET

* **.NET 4.0** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault
* **.NET Framework 4.5.2** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault
* **.NET Standard 1.4** non è supportata dalla versione 2.0 della libreria .NET di Azure Key Vault

## <a name="namespaces"></a>Spazi dei nomi

* Lo spazio dei nomi per i **modelli** viene cambiato da **Microsoft.Azure.KeyVault** a **Microsoft.Azure.KeyVault.Models**.
* Lo spazio dei nomi **Microsoft.Azure.KeyVault.Internal** viene rimosso.
* Lo spazio dei nomi delle dipendenze di Azure SDK viene modificato da **Hyak.Common** e **Hyak.Common.Internals** in **Microsoft.Rest** e **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Modifiche del tipo

* *Secret* modificato in *SecretBundle*
* *Dictionary* modificato in *IDictionary*
* *List<T>, string []* modificato in *IList<T>*
* *NextList* modificato in *NextPageLink*

## <a name="return-types"></a>Tipi restituiti

* **KeyList** e **SecretList** restituiranno *IPage<T>* anziché *ListKeysResponseMessage*
* L'oggetto **BackupKeyAsync** generato restituirà *BackupKeyResult* che contiene *Value* (BLOB di backup). Prima veniva eseguito il wrapping del metodo e veniva restituito solo il valore.

## <a name="exceptions"></a>Eccezioni

* *KeyVaultClientException* viene modificato in *KeyVaultErrorException*
* L'errore del servizio viene modificato da *exception.Error* in *exception.Body.Error.Message*.
* Le informazioni aggiuntive sono rimosse dal messaggio di errore per **[JsonExtensionData]**.

## <a name="constructors"></a>Costruttori

* Invece di accettare *HttpClient* come argomento del costruttore, il costruttore accetta solo *HttpClientHandler* o *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pacchetti scaricati

Durante l'elaborazione di una dipendenza dall'insieme di credenziali delle chiavi da parte del client, venivano scaricati i pacchetti seguenti

### <a name="previous-package-list"></a>Elenco dei pacchetti precedenti

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>Elenco dei pacchetti correnti

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>Modifiche alle classi

* La **UnixEpoch** è stata rimossa
* La classe **Base64UrlConverter** viene rinominata in **Base64UrlJsonConverter**

## <a name="other-changes"></a>Altre modifiche

* A questa versione dell'API è stato aggiunto il supporto per la configurazione dei criteri per i tentativi dell'operazione insieme di credenziali delle chiavi su errori temporanei.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Per le operazioni che restituivano un *insieme di credenziali*, il tipo restituito era una classe che conteneva una proprietà Vault. Il tipo restituito è ora *Vault*.
* *PermissionsToKeys* e *PermissionsToSecrets* ora sono *Permissions.Keys* e *Permissions.Secrets*
* Alcune delle modifiche che riguardano i tipi restituiti si applicano anche al piano di controllo.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Il pacchetto viene interrotto a **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** per le operazioni di crittografia.

