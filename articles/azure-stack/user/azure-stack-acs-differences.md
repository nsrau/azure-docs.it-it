---
title: 'Stack di archiviazione Azure: Considerazioni e le differenze'
description: Comprendere le differenze tra lo Stack di archiviazione di Azure e l'archiviazione di Azure, insieme a considerazioni sulla distribuzione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 165a899dbad0893b3a2bddcfc68c9b5d737e9d3d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Stack di archiviazione Azure: Considerazioni e le differenze

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di archiviazione di Azure è il set di servizi di archiviazione cloud in Microsoft Azure Stack. Stack di archiviazione di Azure fornisce blob, tabelle, code e funzionalità di gestione di account con semantica coerente con Azure.

In questo articolo riepiloga le differenze di Stack di archiviazione di Azure note da archiviazione di Azure. Inoltre, riepiloga altre considerazioni da tenere presenti quando si distribuisce Azure Stack. Per ulteriori informazioni sulle differenze generali tra Stack di Azure e Azure, vedere il [considerazioni chiave](azure-stack-considerations.md) argomento.

## <a name="cheat-sheet-storage-differences"></a>Schede di riferimento rapido: differenze di archiviazione

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Archiviazione file|Basato su cloud di condivisioni file SMB è supportate|Non è ancora supportato
|Crittografia del servizio di archiviazione di Azure per dati inattivi|crittografia AES a 256 bit|Crittografia AES a 128 bit BitLocker
|Tipo di account di archiviazione|Account di archiviazione Blob di Azure e di utilizzo generale|Utilizzo generale solo.
|Opzioni di replica|Archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica, l'archiviazione con ridondanza geografica e accesso in lettura e archiviazione con ridondanza della zona|Archiviazione con ridondanza locale.
|Archiviazione Premium|Supporto completo|Possibile eseguirne il provisioning, ma nessun limite delle prestazioni o garanzia.
|Dischi gestiti|Premium e standard supportati|Non è ancora supportata.
|Nome del BLOB|1.024 caratteri (2.048 byte)|880 caratteri (1,760 byte)
|Dimensioni massime blob di blocco|4,75 TB (100 MB X 50.000 blocchi)|4,75 TB (100 MB x 50.000 blocchi) per l'aggiornamento di 1802 o una versione più recente. 50.000 x 4 MB (circa 195 GB) per le versioni precedenti.
|Copia snapshot blob di pagina|Backup Azure non gestito macchina virtuale i dischi collegati a una macchina virtuale in esecuzione supportata|Non è ancora supportata.
|Copia dello snapshot incrementale blob di pagina|Premium e BLOB di pagine di Azure standard supportati|Non è ancora supportata.
|Livelli di archiviazione per l'archiviazione blob|A caldo, interessanti e archiviare i livelli di archiviazione.|Non è ancora supportata.
Eliminazione temporanea per l'archiviazione blob|Preview|Non è ancora supportata.
|Dimensioni massime blob di pagina|8 TB|1 TB
|Dimensioni di pagina pagina blob|512 byte|4 KB
|Dimensione di chiave di riga e chiave di partizione di tabella|1.024 caratteri (2.048 byte)|400 caratteri (800 byte)
|Snapshot del BLOB|Il numero massimo di snapshot di un blob non è limitato.|Il numero massimo di snapshot di un blob è 1.000.|

### <a name="metrics"></a>Metriche
Esistono inoltre alcune differenze con le metriche di archiviazione:
* I dati delle transazioni di metriche di archiviazione non viene applicata alcuna distinzione della larghezza di banda di rete interna o esterna.
* I dati delle transazioni di metriche di archiviazione non include macchina virtuale di accedere ai dischi installati.

## <a name="api-version"></a>Versione dell'API
Con Stack di archiviazione di Azure sono supportate le seguenti versioni:

API dei servizi di archiviazione Azure:

aggiornare 1802 o versione successiva:
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versioni precedenti:
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)


Le API di gestione dei servizi di archiviazione Azure:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Versioni SDK

Le librerie client seguenti sono supportate con Stack di archiviazione di Azure:

| Libreria client | Versione supportata di Azure Stack | Collegamento                                                                                                                                                                                                                                                                                                                                     | Specifica dell'endpoint       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Da 6.2.0 a 8.7.0.          | Pacchetto NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | file app. config              |
| Java           | Da 4.1.0 a 6.1.0           | Pacchetto di Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configurazione della stringa di connessione      |
| Node.js        | Da 1.1.0 per 2.7.0           | Collegamento NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Ad esempio: eseguire "installare npm azure-storage@2.7.0")<br> <br>Versione di Github:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Dichiarazione dell'istanza del servizio |
| C++            | Da 2.4.0 a 3.1.0           | Pacchetto NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configurazione della stringa di connessione      |
| PHP            | Da 0.15.0 a 1.0.0          | Versione di GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Installazione tramite Composer (vedere i dettagli riportati di seguito)                                                                                                                                                                                                                  | Configurazione della stringa di connessione      |
| Python         | Da 0.30.0 a 1.0.0          | Versione di GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Dichiarazione dell'istanza del servizio |
| Ruby           | Da 0.12.1 a 1.0.1          | Pacchetto RubyGems:<br>Comuni:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Coda: https://rubygems.org/gems/azure-storage-queue/<br>tavolo: https://rubygems.org/gems/azure-storage-table/<br> <br>Versione di GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configurazione della stringa di connessione      |

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a strumenti di sviluppo dello Stack di archiviazione di Azure](azure-stack-storage-dev.md)
* [Introduzione all'archiviazione di Azure Stack](azure-stack-storage-overview.md)

