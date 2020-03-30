---
title: Cronologia delle versioni di Azure Site Recovery Deployment Planner
description: Correzioni note diverse di Site Recovery Planner e limitazioni note insieme alle relative date di rilascio.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433419"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Cronologia delle versioni di Azure Site Recovery Deployment Planner

Questo articolo fornisce la cronologia di tutte le versioni di Azure Site Recovery Deployment Planner insieme alle correzioni, limitazioni note in ognuna e nelle relative date di rilascio.

## <a name="version-251"></a>Versione 2.51Version 2.51

**Data di rilascio: 22 agosto 2019Release Date: August 22, 2019**

**Correzioni:**

- Risolto il problema relativo alla raccomandazione dei costi con Deployment Planner versione 2.5

## <a name="version-25"></a>Versione 2.5

**Data di rilascio: 29 luglio 2019**

**Correzioni:**

- Per le macchine virtuali VMware e le macchine fisiche, si consiglia di aggiornare in base alla replica in dischi gestiti.
- Aggiunto il supporto per Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 o versione successiva

## <a name="version-24"></a>Versione 2.4

**Data di rilascio: 17 aprile 2019**

**Correzioni:**

- Migliorata la compatibilità del sistema operativo, in particolare quando si gestiscono gli errori basati sulla localizzazione.
- Aggiunte macchine virtuali con un massimo di 20 Mbps di velocità di modifica dei dati (varianza) all'elenco di controllo di compatibilità.
- Messaggi di errore migliorati
- Aggiunto il supporto per vCenter 6.7.
- Aggiunto il supporto per Windows Server 2019 e la workstation Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versione 2.3

**Data di rilascio: 3 dicembre 2018Release Date: December 3, 2018**

**Correzioni:**

- È stato risolto un problema che impediva a Pianificazione distribuzione di generare un report con il percorso di destinazione e la sottoscrizione specificati.

## <a name="version-22"></a>Versione 2.2 

**Data di rilascio: 25 aprile 2018Release Date: April 25, 2018**

**Correzioni:**

- Operazioni GetVMList:
  - Risolto un problema che causava l'esito negativo di GetVMList se la cartella specificata non esiste. Ora crea la directory predefinita o crea la directory specificata nel parametro outputfile.
  - Aggiunti motivi di errore più dettagliati per GetVMList.Added more detaildetail failure reasons for GetVMList.
- Sono state aggiunte informazioni sul tipo di macchina virtuale come colonna nel foglio Macchine virtuali compatibili del report Pianificazione distribuzione.
- Ripristino di emergenza da Hyper-V ad Azure:Hyper-V to Azure disaster recovery:
  - Macchine virtuali escluse con dischi rigidi virtuali condivisi e dischi pass-through dalla profilatura. L'operazione Startprofiling mostra l'elenco delle macchine virtuali escluse nella console.
  - Sono state aggiunte macchine virtuali con più di 64 dischi all'elenco delle macchine virtuali incompatibili.
  - Sono stati aggiornati il fattore di compressione di replica iniziale (IR) e di replica differenziale (DR).
  - Aggiunto un supporto limitato per l'archiviazione SMB.

## <a name="version-21"></a>Versione 2.1

**Data di rilascio: 3 gennaio 2018Release Date: January 3, 2018**

**Correzioni:**

- Aggiornato il report di Excel.
- Corretti i bug nell'operazione GetThroughput.
- Aggiunta l'opzione per limitare il numero di macchine virtuali per profilare o generare il report. Il limite predefinito è 1.000 macchine virtuali.
- Da VMware al ripristino di emergenza di Azure:VMware to Azure disaster recovery:
  - Risolto un problema di VM di Windows Server 2016 che entrava nella tabella incompatibile. 
  - Messaggi di compatibilità aggiornati per le macchine virtuali Windows EFI (Extensible Firmware Interface).
- Aggiornato VMware ad Azure e Hyper-V ad Azure, il limite di varianza dei dati della macchina virtuale per ogni macchina virtuale. 
- Miglioramento dell'affidabilità dell'analisi dei file dell'elenco delle macchine virtuali.

## <a name="version-201"></a>Versione 2.0.1

**Data di rilascio: 7 dicembre 2017Release Date: December 7, 2017**

**Correzioni:**

- Aggiunta raccomandazione per ottimizzare la larghezza di banda di rete.

## <a name="version-20"></a>Versione 2.0

**Data di rilascio: 28 novembre 2017Release Date: November 28, 2017**

**Correzioni:**

- Aggiunto il supporto per Hyper-V al ripristino di emergenza di Azure.Added support for Hyper-V to Azure disaster recovery.
- Aggiunto calcolatore dei costi.
- Aggiunto il controllo della versione del sistema operativo per VMware al ripristino di emergenza di Azure per determinare se la macchina virtuale è compatibile o incompatibile per la protezione. Lo strumento utilizza la stringa di versione del sistema operativo restituita dal server vCenter per tale macchina virtuale. È la versione del sistema operativo guest selezionata dall'utente durante la creazione della macchina virtuale in VMware.

**Limitazioni note:**

- Per il ripristino di emergenza da Hyper-V `,`ad `"` `[`Azure, VM con nome contenente i caratteri, ad esempio: , , , `]`e ``` ` ``` non sono supportati. Se profilato, la generazione del report avrà esito negativo o avrà un risultato non corretto.
- Per il ripristino di emergenza da VMware ad Azure, la macchina virtuale con nome contenente virgole non è supportata. Se profilato, la generazione del report non riesce o avrà un risultato non corretto.

## <a name="version-131"></a>Versione 1.3.1

**Data di rilascio: 19 luglio 2017Release Date: July 19, 2017** 

**Correzioni:**

- Aggiunto il supporto per dischi di grandi dimensioni (> 1 TB) nella generazione di report. Ora è possibile usare Pianificazione distribuzione per pianificare la replica per le macchine virtuali con dimensioni del disco superiori a 1 TB (fino a 4095 GB).
Per altre informazioni, vedere il post di blog sul [supporto di dischi di grandi dimensioni in Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/).

## <a name="version-13"></a>Versione 1.3

**Data di rilascio: 9 maggio 2017Release Date: May 9, 2017**

**Correzioni:**

- Aggiunto il supporto per il disco gestito nella generazione di report. Il numero di macchine virtuali che possono essere inserite in un singolo account di archiviazione viene calcolato in base al fatto che il disco gestito è selezionato per Failover/Failover di test.

## <a name="version-12"></a>Versione 1.2

**Data di rilascio: 7 aprile 2017Release Date: April 7, 2017**

**Correzioni:**

- Il tipo di avvio aggiunto (BIOS o EFI) controlla ogni macchina virtuale per determinare se la macchina virtuale è compatibile o incompatibile per la protezione.
- Aggiunte informazioni sul tipo di sistema operativo per ogni macchina virtuale nei fogli di lavoro per macchine virtuali compatibili e nelle macchine virtuali incompatibili.
- Aggiunto il supporto per l'operazione GetThroughput per le aree di Microsoft Azure per enti pubblici e Cina negli Stati Uniti.
- Aggiunte alcune altre verifiche dei prerequisiti per server vCenter ed ESXi.
- Risolto un problema di generazione di report non corretti quando le impostazioni delle impostazioni locali sono impostate su un indirizzo non in lingua inglese.

## <a name="version-11"></a>Versione 1.1

**Data di rilascio: 9 marzo 2017Release Date: March 9, 2017**

**Correzioni:**

- È stato risolto un problema che impediva la profilatura delle macchine virtuali quando sono presenti due o più macchine virtuali con lo stesso nome o indirizzo IP tra diversi host eSXi vCenter.
- Corretto un problema che causava la disabilitazione della copia e della ricerca per le macchine virtuali compatibili e i fogli di lavoro delle macchine virtuali incompatibili.

## <a name="version-10"></a>Versione 1.0

**Data di rilascio: 23 febbraio 2017Release Date: February 23, 2017**

**Limitazioni note:**

- Supporta solo scenari di ripristino di emergenza da VMware ad Azure.Supports only for VMware to Azure disaster recovery scenarios. Per gli scenari di ripristino di emergenza da Hyper-V ad Azure, usare [lo strumento di pianificazione della capacità di Hyper-V.](./site-recovery-capacity-planning-for-hyper-v-replication.md)
- Non supporta l'operazione GetThroughput per le aree di Microsoft Azure per enti pubblici e Cina negli Stati Uniti.
- Lo strumento non può profilare le macchine virtuali se il server vCenter dispone di due o più macchine virtuali con lo stesso nome o indirizzo IP tra vari host ESXi.
In questa versione, lo strumento ignora la profilatura di VM con nomi o indirizzi IP duplicati nel parametro VMListFile. La soluzione alternativa prevede di profilare le VM usando un host ESXi invece del server vCenter. Assicurarsi di eseguire un'istanza per ogni host ESXi.
