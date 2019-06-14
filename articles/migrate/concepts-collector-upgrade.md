---
title: Aggiornare l'appliance Agente di raccolta in Azure Migrate | Microsoft Docs
description: L'articolo contiene informazioni sugli aggiornamenti per l'appliance Agente di raccolta di Azure Migrate.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685920"
---
# <a name="collector-appliance-updates"></a>Aggiornamenti all'appliance dell'agente di raccolta

Questo articolo riepiloga le informazioni sull'aggiornamento per l'appliance Agente di raccolta in [Azure Migrate](migrate-overview.md).

Agente di raccolta di Azure Migrate è un'appliance leggera usata per individuare l'ambiente vCenter locale a scopo di valutazione prima della migrazione ad Azure. [Altre informazioni](concepts-collector.md)

## <a name="how-to-upgrade-the-appliance"></a>Come aggiornare l'appliance

È possibile aggiornare Agente di raccolta alla versione più recente senza scaricare nuovamente il file OVA.

1. Chiudere tutte le finestre del browser ed eventuali aprire file e cartelle nell'appliance.
2. Scaricare il pacchetto di aggiornamento più recente dall'elenco degli aggiornamenti indicato di seguito in questo articolo.
3. Per assicurarsi che il pacchetto scaricato è sicuro, aprire una finestra di comando di amministratore ed eseguire il comando seguente per generare l'hash del file ZIP. L'hash generato deve corrispondere con all'hash indicato nella versione specifica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Esempio: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Copiare il file con estensione zip nell'appliance Agente di raccolta della macchina virtuale.
5. Fare clic con il pulsante destro del mouse sul file con estensione zip > **Estrai tutto**.
6. Fare clic con il pulsante destro su **Setup.ps1** > **Esegui con PowerShell** e seguire le istruzioni di installazione.

## <a name="collector-update-release-history"></a>Cronologia delle versioni di aggiornamento dell'agente di raccolta

### <a name="continuous-discovery-upgrade-versions"></a>Individuazione continua: Versioni di aggiornamento

#### <a name="version-101014-released-on-03292019"></a>Versione 1.0.10.14 (rilasciato 03/29/2019)

Contiene alcuni miglioramenti dell'interfaccia utente.

Hash di valori per l'aggiornamento [1.0.10.14 del pacchetto](https://aka.ms/migrate/col/upgrade_10_14)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>Versione 1.0.10.12 (rilasciato 03/13/2019)

Contiene correzioni per problemi in Azure selezionando il nell'appliance cloud.

Hash di valori per l'aggiornamento [1.0.10.12 del pacchetto](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Individuazione una tantum (ora deprecata): Versioni di aggiornamento precedenti

> [!NOTE]
> L'appliance per l'individuazione una tantum è ora deprecata poiché questo metodo si basava sulle impostazioni delle statistiche del server vCenter relative alla disponibilità dei punti dati delle prestazioni e raccoglieva i contatori delle prestazioni medie, determinando così un sottodimensionamento delle macchine virtuali per la migrazione ad Azure.

#### <a name="version-10916-released-on-10292018"></a>Versione 1.0.9.16 (data di rilascio 29/10/2018)

Contiene correzioni per i problemi di PowerCLI che si verificano durante la configurazione dell'appliance.

Valori hash per l'aggiornamento al [pacchetto 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Versione 1.0.9.14

Valori hash per l'aggiornamento al [pacchetto 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Versione 1.0.9.13

Valori hash per l'aggiornamento al [pacchetto 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](concepts-collector.md) sull'appliance Agente di raccolta.
- [Eseguire una valutazione](tutorial-assessment-vmware.md) per le macchine virtuali VMware.
