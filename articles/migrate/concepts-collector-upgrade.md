---
title: Aggiornare l'appliance Agente di raccolta in Azure Migrate | Microsoft Docs
description: L'articolo contiene informazioni sugli aggiornamenti per l'appliance Agente di raccolta di Azure Migrate.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 88077ac965b2abb69be145f29cbadca2ff1128d6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836645"
---
# <a name="collector-update-release-history"></a>Cronologia delle versioni di aggiornamento dell'agente di raccolta

Questo articolo riepiloga le informazioni sull'aggiornamento per l'appliance Agente di raccolta in [Azure Migrate](migrate-overview.md).

Agente di raccolta di Azure Migrate è un'appliance leggera usata per individuare l'ambiente vCenter locale a scopo di valutazione prima della migrazione ad Azure. [Altre informazioni](concepts-collector.md)

## <a name="continuous-discovery-upgrade-versions"></a>Individuazione continua: Versioni di aggiornamento

Non è ancora disponibile alcun aggiornamento per l'appliance di individuazione continua.

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Individuazione una tantum (ora deprecata): Versioni di aggiornamento precedenti

> [!NOTE]
> L'appliance per l'individuazione una tantum è ora deprecata poiché questo metodo si basava sulle impostazioni delle statistiche del server vCenter relative alla disponibilità dei punti dati delle prestazioni e raccoglieva i contatori delle prestazioni medie, determinando così un sottodimensionamento delle macchine virtuali per la migrazione ad Azure.

### <a name="version-10916-released-on-10292018"></a>Versione 1.0.9.16 (data di rilascio 29/10/2018)

Contiene correzioni per i problemi di PowerCLI che si verificano durante la configurazione dell'appliance.

Valori hash per l'aggiornamento al [pacchetto 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Versione 1.0.9.14

Valori hash per l'aggiornamento al [pacchetto 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Versione 1.0.9.13

Valori hash per l'aggiornamento al [pacchetto 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>Eseguire un aggiornamento

È possibile aggiornare Agente di raccolta alla versione più recente senza scaricare nuovamente il file OVA.

1. Scaricare il pacchetto di aggiornamento più recente nell'elenco seguente.
2. Per garantire la sicurezza dell'hotfix scaricato, aprire il prompt dei comandi come amministratore ed eseguire il comando seguente per generare l'hash del file con estensione zip. L'hash generato deve corrispondere con all'hash indicato nella versione specifica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Esempio: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Copiare il file con estensione zip nell'appliance Agente di raccolta della macchina virtuale.
4. Fare clic con il pulsante destro del mouse sul file con estensione zip > **Estrai tutto**.
5. Fare clic con il pulsante destro su **Setup.ps1** > **Esegui con PowerShell** e seguire le istruzioni di installazione.


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](concepts-collector.md) sull'appliance Agente di raccolta.
- [Eseguire una valutazione](tutorial-assessment-vmware.md) per le macchine virtuali VMware.
