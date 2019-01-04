---
title: Differenze di archiviazione di Azure stack e considerazioni | Microsoft Docs
description: Comprendere le differenze tra l'archiviazione di Azure stack e archiviazione di Azure, insieme a considerazioni sulla distribuzione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 1393dd32aea8cb6d348092ea1fc56752f659beab
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717875"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Archiviazione di Azure Stack: Differenze e considerazioni

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Archiviazione di Azure Stack è il set di servizi di archiviazione cloud in Microsoft Azure Stack. Archiviazione di Azure Stack offre funzionalità di gestione di account con una semantica coerente con Azure, tabella, coda e blob.

Questo articolo riepiloga le differenze note di archiviazione di Azure Stack dai servizi di archiviazione di Azure. Elenca anche tenere in considerazione quando si distribuisce Azure Stack. Per altre informazioni sulle differenze generali tra globale di Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

## <a name="cheat-sheet-storage-differences"></a>Foglio informativo: Differenze di archiviazione

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Archiviazione file|Basato sul cloud di condivisioni file SMB è supportate|Non è ancora supportata
|Crittografia del servizio di archiviazione di Azure per dati inattivi|crittografia AES a 256 bit. Supporta la crittografia con chiavi gestite dal cliente in Azure Key Vault.|Crittografia AES a 128 bit di BitLocker. Crittografia con chiavi gestite dal cliente non è supportata.
|Tipo di account di archiviazione|Account di archiviazione per utilizzo generico V1, V2 e Blob|Solo per utilizzo generico V1.
|Opzioni di replica|Archiviazione con ridondanza locale, archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura e archiviazione con ridondanza della zona|Archiviazione con ridondanza locale.
|Archiviazione Premium|Supporto completo|Può essere eseguito il provisioning, ma nessun limite delle prestazioni o garanzia.
|Dischi gestiti|Premium e standard supportati|È supportato quando si usa 1808 o versione successiva.
|Nome del BLOB|1024 caratteri (2.048 byte)|880 caratteri (1,760 byte)
|Dimensioni massime blob di blocco|4,75 TB (100 MB X 50.000 blocchi)|4,75 TB (100 MB x 50.000 blocchi) per l'aggiornamento 1802 o una versione più recente. 50.000 x 4 MB (circa 195 GB) per le versioni precedenti.
|Copia di snapshot blob di pagine|Backup non gestito della macchina virtuale nei dischi Azure collegati a una macchina virtuale in esecuzione supportata|Non è ancora supportata.
|Copia di snapshot incrementale blob di pagine|Premium e standard BLOB di Azure supportate|Non è ancora supportata.
|Livelli di archiviazione per l'archiviazione blob|Accesso frequente, sporadico e archivio i livelli di archiviazione.|Non è ancora supportata.
|Eliminazione temporanea per l'archiviazione blob|Disponibilità generale|Non è ancora supportata.
|Dimensioni massime blob pagina|8 TB|1 TB
|Dimensioni di pagina pagina blob|512 byte|4 KB
|Dimensioni chiave di riga e chiave di partizione di tabella|1024 caratteri (2.048 byte)|400 caratteri (800 byte)
|Snapshot BLOB|Il numero massimo di snapshot di un blob non è limitato.|Il numero massimo di snapshot di un blob è 1.000.
|Autenticazione di Azure AD per l'archiviazione|In anteprima|Non è ancora supportata.
|BLOB non modificabili|Disponibilità generale|Non è ancora supportata.
|Firewall e regole della rete virtuale per l'archiviazione|Disponibilità generale|Non è ancora supportata.|

Esistono anche differenze con le metriche di archiviazione:

* I dati delle transazioni metriche di archiviazione non viene fatta distinzione della larghezza di banda di rete interna o esterna.
* I dati delle transazioni metriche di archiviazione non includono l'accesso alle macchine virtuali nei dischi montati.

## <a name="api-version"></a>Versione dell'API

Con archiviazione di Azure Stack sono supportate le versioni seguenti:

API per i servizi archiviazione di Azure:

1811 update o versioni più recenti:

 - [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
 - [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

aggiornamento 1802 update 1809:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versioni precedenti:

- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Le API di gestione dei servizi di archiviazione di Azure:

- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Versioni precedenti:

 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 
## <a name="sdk-versions"></a>Versioni del SDK

Archiviazione di Azure Stack supporta le librerie client seguenti:

| Libreria client | Versione supportata di Azure Stack | Collegamento                                                                                                                                                                                                                                                                                                                                     | Specifica dell'endpoint       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Da 6.2.0 a 8.7.0.          | Pacchetto NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | file app. config              |
| Java           | Da 4.1.0 a 6.1.0           | Pacchetto Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configurazione della stringa di connessione      |
| Node.js        | Da 1.1.0 per 2.7.0           | Collegamento NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Ad esempio: eseguire "npm installare azure-storage@2.7.0")<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Dichiarazione dell'istanza del servizio |
| C++            | Dalla versione 2.4.0 alla 3.1.0           | Pacchetto NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configurazione della stringa di connessione      |
| PHP            | Da 0.15.0 su 1.0.0          | Versione di GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Installazione tramite Composer (vedere i dettagli sotto)                                                                                                                                                                                                                  | Configurazione della stringa di connessione      |
| Python         | Da 0.30.0 su 1.0.0          | Versione di GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Dichiarazione dell'istanza del servizio |
| Ruby           | Da 0.12.1 a 1.0.1          | Pacchetto RubyGems:<br>Comuni:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Coda: https://rubygems.org/gems/azure-storage-queue/<br>tavolo: https://rubygems.org/gems/azure-storage-table/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configurazione della stringa di connessione      |

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure](azure-stack-storage-dev.md)
* [Introduzione ad archiviazione di Azure Stack](azure-stack-storage-overview.md)
