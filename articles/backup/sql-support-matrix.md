---
title: Matrice di supporto di backup di Azure per SQL Server backup in macchine virtuali di Azure
description: Fornisce un riepilogo delle impostazioni e delle limitazioni di supporto durante l'esecuzione del backup SQL Server in macchine virtuali di Azure con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: b189eceb6b5a7f2e508387c0b91b238ff5fcb088
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174053"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matrice di supporto per il backup SQL Server in macchine virtuali di Azure

È possibile usare backup di Azure per eseguire il backup di database SQL Server in macchine virtuali di Azure ospitate nella piattaforma Microsoft Azure cloud. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per gli scenari e le distribuzioni di SQL Server backup nelle VM di Azure.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree supportate** | Australia sud-orientale (ASE), Australia orientale (AE), Australia centrale (AC), Australia centrale 2 (AC) <br> Brasile meridionale (BRS)<br> Canada centrale (CNC), Canada orientale (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro-occidentali (WCUS), Stati Uniti occidentali (WUS), Stati Uniti occidentali 2 (WUS 2), Stati Uniti centro-settentrionali (NCUS), Stati Uniti centrali (CUS), Stati Uniti centro-meridionali (SCUS) <br> India centrale (INC), India meridionale (INS), India occidentale <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD (area centrale), US DoD (area orientale) <br> Germania settentrionale, Germania centro-occidentale <br> Svizzera settentrionale, Svizzera occidentale <br> Francia centrale <br> Cina orientale, Cina orientale 2, Cina settentrionale, Cina settentrionale 2
**Sistemi operativi supportati** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2019, SQL Server 2017 come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SP come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Le versioni di database locali Express non sono supportate.
**Versioni di .NET supportate** | .NET Framework 4.5.2 o versioni successive installato nella macchina virtuale

## <a name="feature-considerations-and-limitations"></a>Considerazioni sulle funzionalità e limitazioni

|Impostazione  |Limite massimo |
|---------|---------|
|Numero di database che possono essere protetti in un server e in un insieme di credenziali    |   2000      |
|Dimensioni del database supportate (oltre a questo, è possibile che si verifichino problemi di prestazioni)   |   2 TB      |
|Numero di file supportati in un database    |   1000      |

>[!NOTE]
> [Scaricare il pianificatore di risorse dettagliato](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) per calcolare il numero approssimativo di database protetti consigliati per ogni server in base alle risorse della macchina virtuale, alla larghezza di banda e ai criteri di backup.

* Il backup di SQL Server può essere configurato nel portale di Azure o in **PowerShell**. CLI non è supportato.
* La soluzione è supportata in entrambe le tipologie di [distribuzione](../azure-resource-manager/management/deployment-models.md): macchine virtuali di Azure Resource Manager e macchine virtuali classiche.
* Sono supportati tutti i tipi di backup (completo/differenziale/log) e i modelli di recupero (semplice/completo/con registrazione minima delle operazioni bulk).
* Per i database di sola **lettura** sono supportati i tipi di backup completo completi e di sola copia.
* La compressione SQL nativa è supportata se l'utente è abilitato in modo esplicito nel criterio di backup. Backup di Azure esegue l'override delle impostazioni predefinite a livello di istanza con la clausola COMPRESSION/NO_COMPRESSION, a seconda del valore di questo controllo impostato dall'utente.
* Il backup del database abilitato per Transparent Data Encryption è supportato. Per ripristinare un database crittografato con Transparent Data Encryption in un altro SQL Server, è necessario innanzitutto [ripristinare il certificato nel server di destinazione](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). È disponibile la compressione dei backup per i database abilitati per Transparent Data Encryption per SQL Server 2016 e versioni successive, ma con una dimensione di trasferimento inferiore, come illustrato [qui](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Le operazioni di backup e ripristino per i database mirror e gli snapshot di database non sono supportate.
* SQL Server **istanza del cluster di failover (FCI)** non è supportata.
* L'uso di più soluzioni di backup per eseguire il backup dell'istanza di SQL Server autonoma o del gruppo di disponibilità SQL always on può causare un errore di backup. Evitare di eseguire questa operazione. Anche il backup di due nodi di un gruppo di disponibilità singolarmente con soluzioni uguali o differenti potrebbe generare errori.
* Quando i gruppi di disponibilità sono configurati, i backup vengono eseguiti dai diversi nodi in base a alcuni fattori. Il comportamento di backup per un gruppo di disponibilità è riepilogato di seguito.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento di backup con i gruppi di disponibilità AlwaysOn

Si consiglia di configurare il backup solo in un nodo di un gruppo di disponibilità (AG). Configurare sempre il backup nella stessa area del nodo primario. In altre parole, è sempre necessario che il nodo primario sia presente nell'area in cui si sta configurando il backup. Se tutti i nodi del gruppo di disponibilità si trovano nella stessa area in cui è configurato il backup, non vi sono problemi.

#### <a name="for-cross-region-ag"></a>Per i gruppi di disponibilità tra aree

* Indipendentemente dalla preferenza di backup, i backup verranno eseguiti solo dai nodi che si trovano nella stessa area in cui è configurato il backup. Questo perché i backup tra aree non sono supportati. Se si dispone solo di due nodi e il nodo secondario si trova nell'altra area, i backup continueranno a essere eseguiti dal nodo primario (a meno che la preferenza di backup non sia "solo secondaria").
* Se viene eseguito il failover di un nodo in un'area diversa da quella in cui è configurato il backup, i backup avranno esito negativo nei nodi dell'area sottoposta a failover.

In base alle preferenze e ai tipi di backup (completo/differenziale/log/completo solo copia), i backup vengono eseguiti da uno specifico nodo (primario o secondario).

#### <a name="backup-preference-primary"></a>Preferenza di backup: primario

**Tipo di backup** | **Node**
--- | ---
Completo | Principale
Differenziale | Principale
File di log |  Principale
Completo solo copia |  Principale

#### <a name="backup-preference-secondary-only"></a>Preferenza di backup: solo secondario

**Tipo di backup** | **Node**
--- | ---
Completo | Principale
Differenziale | Principale
File di log |  Secondari
Completo solo copia |  Secondari

#### <a name="backup-preference-secondary"></a>Preferenza di backup: secondaria

**Tipo di backup** | **Node**
--- | ---
Completo | Principale
Differenziale | Principale
File di log |  Secondari
Completo solo copia |  Secondari

#### <a name="no-backup-preference"></a>Nessuna preferenza di backup

**Tipo di backup** | **Node**
--- | ---
Completo | Principale
Differenziale | Principale
File di log |  Secondari
Completo solo copia |  Secondari

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di un database di SQL Server](backup-azure-sql-database.md) in esecuzione in una macchina virtuale di Azure.