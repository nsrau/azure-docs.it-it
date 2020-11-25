---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019140"
---
|Nome parametro| Tipo | Descrizione| Valori possibili|
|-|-|-|-|
| /Modalità server|Obbligatorio|Specifica se devono essere installati i server di configurazione e di elaborazione o solo il server di elaborazione|CS<br>PS|
|/InstallLocation|Obbligatorio|Cartella in cui sono installati i componenti| Qualsiasi cartella del computer|
|/MySQLCredsFilePath|Obbligatorio|Percorso del file in cui sono archiviate le credenziali del server MySQL|Il file deve essere nel formato specificato di seguito|
|/VaultCredsFilePath|Obbligatorio|Percorso del file di credenziali dell'insieme di credenziali|Percorso del file valido|
|/EnvType|Obbligatorio|Tipo di ambiente che si vuole proteggere |VMware<br>NonVMware|
|/PSIP|Obbligatorio|Indirizzo IP della scheda di interfaccia di rete da utilizzare per il trasferimento di dati di replica| Qualsiasi indirizzo IP valido|
|/CSIP|Obbligatorio|Indirizzo IP della scheda di interfaccia di rete su cui il server di configurazione è in ascolto| Qualsiasi indirizzo IP valido|
|/PassphraseFilePath|Obbligatorio|Percorso completo del file della passphrase|Percorso del file valido|
|/BypassProxy|Facoltativo|Specifica che il server di configurazione si connette ad Azure senza un proxy||
|/ProxySettingsFilePath|Facoltativo|Impostazioni proxy, il proxy predefinito richiede l'autenticazione o un proxy personalizzato|Il file deve essere nel formato specificato di seguito|
|DataTransferSecurePort|Facoltativo|Numero di porta su PSIP da usare per i dati di replica| Numero di porta valido (il valore predefinito è 9433)|
|/SkipSpaceCheck|Facoltativo|Ignora la verifica dello spazio per il disco della cache| |
|/AcceptThirdpartyEULA|Obbligatorio|Il flag implica l'accettazione dell'EULA di terze parti| |
|/ShowThirdpartyEULA|Facoltativo|Visualizza le condizioni di licenza di terze parti. Se specificato come input, tutti gli altri parametri vengono ignorati| |
