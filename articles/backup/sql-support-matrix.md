---
title: Azure Backup support matrix for SQL Server Backup in Azure VMs
description: Viene fornito un riepilogo delle impostazioni di supporto e delle limitazioni quando si esegue il backup di SQL Server nelle macchine virtuali di Azure con il servizio Backup di Azure.Provides a summary of support settings and limitations when backing up SQL Server in Azure VMs with the Azure Backup service.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409999"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matrice di supporto per il backup di SQL Server nelle macchine virtuali di AzureSupport matrix for SQL Server Backup in Azure VMs

È possibile usare Backup di Azure per eseguire il backup dei database di SQL Server nelle macchine virtuali di Azure ospitate nella piattaforma cloud di Microsoft Azure.You can use Azure Backup to back up SQL Server databases in Azure VMs hosted on the Microsoft Azure cloud platform. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per scenari e distribuzioni di Backup di SQL Server nelle macchine virtuali di Azure.This article summarizes the general support settings and limitations for scenarios and deployments of SQL Server Backup in Azure VMs.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree geografiche supportate** | Australia sud-orientale (ASE), Australia orientale (AE), Australia centrale (AC), Australia centrale 2 (AC) <br> Brasile meridionale (BRS)<br> Canada centrale (CNC), Canada orientale (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro-occidentali (WCUS), Stati Uniti occidentali (WUS), Stati Uniti occidentali 2 (WUS 2), Stati Uniti centro-settentrionali (NCUS), Stati Uniti centrali (CUS), Stati Uniti centro-meridionali (SCUS) <br> India centrale (INC), India meridionale (INS), India occidentale <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD (area centrale), US DoD (area orientale) <br> Germania settentrionale, Germania centro-occidentale <br> Svizzera settentrionale, Svizzera occidentale <br> Francia centrale <br> Cina orientale, Cina orientale 2, Cina settentrionale, Cina settentrionale 2
**Sistemi operativi supportati** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2019, SQL Server 2017 come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SP come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versioni di .NET supportate** | .NET Framework 4.5.2 o versioni successive installato nella macchina virtuale

## <a name="feature-consideration-and-limitations"></a>Considerazioni e limitazioni della funzionalità

* Il backup di SQL Server può essere configurato nel portale di Azure o in **PowerShell**. Non supportiamo CLI.
* La soluzione è supportata in entrambe le tipologie di [distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model): macchine virtuali di Azure Resource Manager e macchine virtuali classiche.
* La macchina virtuale che esegue SQL Server richiede la connettività Internet per accedere agli indirizzi IP pubblici di Azure.
* Sql Server **Failover Cluster Instance (FCI)** non è supportato.
* Le operazioni di backup e ripristino per i database mirror e gli snapshot di database non sono supportate.
* L'uso di più soluzioni per eseguire il backup dell'istanza di SQL Server autonoma o del gruppo di disponibilità AlwaysOn di SQL potrebbe generare un errore di backup. Evitare questo approccio.
* Anche il backup di due nodi di un gruppo di disponibilità singolarmente con soluzioni uguali o differenti potrebbe generare errori.
* Backup di Azure supporta solo i tipi di backup completo e completo solo copia per i database di **sola lettura**
* Non è possibile proteggere i database con un numero elevato di file. Il numero massimo di file supportato è **~1000**.  
* È possibile eseguire il backup di **2000** database di SQL Server in un insieme di credenziali. Nel caso di un numero elevato di database, è possibile creare più insiemi di credenziali.
* È possibile configurare il backup per un totale di **50** database alla volta. Questa restrizione contribuisce a ottimizzare i carichi di backup.
* Supportiamo database di dimensioni fino a **2 TB;** per dimensioni superiori a quelle che potrebbero verificarsi problemi di prestazioni.
* Per avere un'idea del numero di database che possono essere protetti per server, considerare fattori quali larghezza di banda, dimensioni della macchina virtuale, frequenza di backup, dimensioni del database e così via. [Scaricare](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) la pianificazione delle risorse per calcolare il numero approssimativo di database che è possibile avere per server in base alle risorse della macchina virtuale e ai criteri di backup.
* Quando vengono configurati i gruppi di disponibilità, i backup vengono eseguiti dai diversi nodi in base a alcuni fattori. Il comportamento di backup per un gruppo di disponibilità è riepilogato di seguito.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento di backup con gruppi di disponibilità AlwaysOnBack behavior with Always on availability groups

È consigliabile che il backup sia configurato in un solo nodo di un gruppo di disponibilità. Configurare sempre il backup nella stessa area del nodo primario. In altre parole, è sempre necessario che il nodo primario sia presente nell'area in cui si sta configurando il backup. Se tutti i nodi del gruppo di disponibilità si trovano nella stessa area in cui è configurato il backup, non vi è alcun problema.

#### <a name="for-cross-region-ag"></a>Per i gruppi di disponibilità tra aree

* Indipendentemente dalla preferenza di backup, i backup verranno eseguiti solo dai nodi che si trovano nella stessa area in cui è configurato il backup. Ciò è dovuto al fatto che i backup tra aree non sono supportati. Se si dispone solo di due nodi e il nodo secondario si trova nell'altra area, i backup continueranno a essere eseguiti dal nodo primario (a meno che la preferenza di backup non sia "solo secondaria").
* Se un nodo esegue il failover in un'area diversa da quella in cui è configurato il backup, i backup avranno esito negativo nei nodi nell'area di cui è stato eseguito il failover.

In base alle preferenze e ai tipi di backup (completo/differenziale/log/completo solo copia), i backup vengono eseguiti da uno specifico nodo (primario o secondario).

#### <a name="backup-preference-primary"></a>Preferenza di backup: Principale

**Tipo di backup** | **Nodo**
--- | ---
Full | Primaria
Differenziale | Primaria
File di log |  Primaria
Completo solo copia |  Primaria

#### <a name="backup-preference-secondary-only"></a>Preferenza di backup: Solo secondaria

**Tipo di backup** | **Nodo**
--- | ---
Full | Primaria
Differenziale | Primaria
File di log |  Secondari
Completo solo copia |  Secondari

#### <a name="backup-preference-secondary"></a>Preferenza di backup: Secondaria

**Tipo di backup** | **Nodo**
--- | ---
Full | Primaria
Differenziale | Primaria
File di log |  Secondari
Completo solo copia |  Secondari

#### <a name="no-backup-preference"></a>Nessuna preferenza di backup

**Tipo di backup** | **Nodo**
--- | ---
Full | Primaria
Differenziale | Primaria
File di log |  Secondari
Completo solo copia |  Secondari

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il backup di un database di SQL Server in esecuzione in una macchina virtuale di Azure.Learn how to back up a [SQL Server database](backup-azure-sql-database.md) that's running on an Azure VM.
