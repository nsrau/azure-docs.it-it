---
title: Cronologia delle versioni di Azure Site Recovery Deployment Planner
description: Sono note Site Recovery diverse versioni Deployment Planner correzioni e limitazioni note insieme alle date di rilascio.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 07/29/2019
ms.author: dapatil
ms.openlocfilehash: acce72a5ddfaab56a7fcce92f0153bb06cb1ae71
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620110"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Cronologia delle versioni di Azure Site Recovery Deployment Planner

Questo articolo fornisce la cronologia di tutte le versioni di Azure Site Recovery Deployment Planner con le correzioni, le limitazioni note in ognuna e le date di rilascio.

## <a name="version-25"></a>Versione 2,5

**Data di rilascio: 29 luglio 2019**

**Correzioni**

- Per le macchine virtuali VMware e i computer fisici, la raccomandazione viene aggiornata in base alla replica per Managed Disks.
- Aggiunta del supporto per Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 o versione successiva

## <a name="version-24"></a>Versione 2,4

**Data di rilascio: 17 aprile 2019**

**Correzioni**

- Maggiore compatibilità del sistema operativo, in particolare quando si gestiscono errori basati sulla localizzazione.
- Sono state aggiunte VM con un massimo di 20 Mbps di frequenza di modifica dei dati (varianza) nell'elenco di controllo della compatibilità.
- Messaggi di errore migliorati
- Aggiunta del supporto per vCenter 6,7.
- Aggiunta del supporto per workstation Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versione 2,3

**Data di rilascio: 3 dicembre 2018**

**Correzioni**

- È stato risolto un problema che impediva al Deployment Planner di generare un report con il percorso di destinazione e la sottoscrizione specificati.

## <a name="version-22"></a>Versione 2,2 

**Data di rilascio: 25 aprile 2018**

**Correzioni**

- Operazioni GetVMList:
  - È stato risolto un problema che causava l'esito negativo di GetVMList se la cartella specificata non esiste. Ora crea la directory predefinita o crea la directory specificata nel parametro outputfile.
  - Aggiunta di motivi di errore più dettagliati per GetVMList.
- Aggiunta di informazioni sul tipo di macchina virtuale come colonna nel foglio delle VM compatibili del report Deployment Planner.
- Ripristino di emergenza da Hyper-V ad Azure:
  - Profilatura delle VM escluse con dischi rigidi virtuali condivisi e dischi pass-through. L'operazione Startprofiling Visualizza l'elenco delle macchine virtuali escluse nella console di.
  - Sono state aggiunte VM con più di 64 dischi all'elenco di VM incompatibili.
  - Aggiornamento del fattore di compressione della replica iniziale (IR) e del delta replication (DR).
  - Aggiunta del supporto limitato per l'archiviazione SMB.

## <a name="version-21"></a>Versione 2,1

**Data di rilascio: 3 gennaio 2018**

**Correzioni**

- Il report di Excel è stato aggiornato.
- Correzione dei bug nell'operazione getthroughput.
- Aggiunta dell'opzione per limitare il numero di macchine virtuali da profilare o generare il report. Il limite predefinito è 1.000 VM.
- Ripristino di emergenza da VMware ad Azure:
  - Correzione di un problema relativo alla macchina virtuale Windows Server 2016 nella tabella incompatibile. 
  - Messaggi di compatibilità aggiornati per le macchine virtuali Windows Extensible Firmware Interface (EFI).
- Aggiornamento da VMware ad Azure e da Hyper-V ad Azure, limite della varianza dei dati della VM per macchina virtuale. 
- Miglioramento dell'affidabilità dell'analisi dei file di elenco delle macchine virtuali.

## <a name="version-201"></a>Versione 2.0.1

**Data di rilascio: 7 dicembre 2017**

**Correzioni**

- Aggiunta raccomandazione per ottimizzare la larghezza di banda di rete.

## <a name="version-20"></a>Versione 2,0

**Data di rilascio: 28 novembre 2017**

**Correzioni**

- Aggiunta del supporto per il ripristino di emergenza da Hyper-V ad Azure.
- Aggiunta del calcolatore dei costi.
- Aggiunta del controllo della versione del sistema operativo per il ripristino di emergenza da VMware ad Azure per determinare se la macchina virtuale è compatibile o incompatibile per la protezione. Lo strumento usa la stringa di versione del sistema operativo restituita dal server vCenter per la macchina virtuale. Si tratta della versione del sistema operativo guest selezionata dall'utente durante la creazione della macchina virtuale in VMware.

**Limitazioni note:**

- Per il ripristino di emergenza da Hyper-V ad Azure, la macchina virtuale con nome `,`contenente i `[`caratteri seguenti: ``` ` ``` , `"`,, `]`e non è supportata. Se profilato, la generazione di report avrà esito negativo o avrà un risultato errato.
- Per il ripristino di emergenza da VMware ad Azure, la macchina virtuale con nome contenente la virgola non è supportata. Se profilato, la generazione del report ha esito negativo o avrà un risultato errato.

## <a name="version-131"></a>Versione 1.3.1

**Data di rilascio: 19 luglio 2017** 

**Correzioni**

- Aggiunta del supporto per dischi di grandi dimensioni (> 1 TB) nella generazione di report. A questo punto è possibile usare Deployment Planner per pianificare la replica per le macchine virtuali con dimensioni del disco superiori a 1 TB (fino a 4095 GB).
Per altre informazioni, vedere il post di blog sul [supporto di dischi di grandi dimensioni in Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/).

## <a name="version-13"></a>Versione 1,3

**Data di rilascio: 9 maggio, 2017**

**Correzioni**

- Aggiunta del supporto per il disco gestito nella generazione del report. Il numero di macchine virtuali che possono essere inserite in un singolo account di archiviazione viene calcolato in base a se il disco gestito è selezionato per failover/failover di test.

## <a name="version-12"></a>Versione 1,2

**Data di rilascio: 7 aprile 2017**

**Correzioni**

- Aggiunta dei controlli del tipo di avvio (BIOS o EFI) per ogni macchina virtuale per determinare se la macchina virtuale è compatibile o incompatibile per la protezione.
- Sono state aggiunte informazioni sul tipo di sistema operativo per ogni macchina virtuale nelle macchine virtuali compatibili e nelle macchine virtuali incompatibili.
- Aggiunta del supporto per l'operazione getthroughput per le aree del governo degli Stati Uniti e della Cina Microsoft Azure.
- Aggiunte alcune altre verifiche dei prerequisiti per server vCenter ed ESXi.
- Correzione di un problema relativo alla generazione di report non corretti quando le impostazioni locali sono impostate su una lingua diversa dall'inglese.

## <a name="version-11"></a>Versione 1.1

**Data di rilascio: 9 marzo 2017**

**Correzioni**

- È stato risolto un problema che impediva la profilatura delle VM in presenza di due o più macchine virtuali con lo stesso nome o indirizzo IP tra diversi host vCenter ESXi.
- È stato risolto un problema che causava la disabilitazione della copia e della ricerca per le VM compatibili e le macchine virtuali incompatibili.

## <a name="version-10"></a>Versione 1,0

**Data di rilascio: 23 febbraio 2017**

**Limitazioni note:**

- Supporta solo gli scenari di ripristino di emergenza da VMware ad Azure. Per gli scenari di ripristino di emergenza da Hyper-V ad Azure, usare lo [strumento di pianificazione della capacità di Hyper-v](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Non supporta l'operazione getthroughput per il governo degli Stati Uniti e le aree Microsoft Azure Cina.
- Lo strumento cann ' t profilo VM se il server vCenter dispone di due o più macchine virtuali con lo stesso nome o indirizzo IP in diversi host ESXi.
In questa versione, lo strumento ignora la profilatura di VM con nomi o indirizzi IP duplicati nel parametro VMListFile. La soluzione alternativa prevede di profilare le VM usando un host ESXi invece del server vCenter. Assicurarsi di eseguire un'istanza per ogni host ESXi.
