---
title: Cronologia delle versioni di Azure Site Recovery Deployment Planner
description: Correzioni di Site Recovery Deployment Planner versioni diverse note e limitazioni note con le date di rilascio.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927373"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Cronologia delle versioni di Azure Site Recovery Deployment Planner

Questo articolo fornisce una cronologia di tutte le versioni di Azure Site Recovery Deployment Planner insieme le correzioni, denominato limitazioni in ognuno, mentre le date di rilascio.

## <a name="version-24"></a>Versione 2.4

**Data di rilascio: 17 aprile 2019**

**Correzioni:**

- Migliorata la compatibilità del sistema operativo, in modo specifico la gestione degli errori basata sulla localizzazione.
- Aggiunte le macchine virtuali con fino a 20 Mbps dei dati variazioni tariffa (varianza) per l'elenco di controllo di compatibilità.
- Messaggi di errore migliorati
  - Aggiunta del supporto per vCenter 6.7.
  - Aggiunta del supporto per workstation Windows Server 2019 e Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Versione 2.3

**Data di rilascio: 3 dicembre 2018**

**Correzioni:**

- Risolto un problema che impediva la generazione di un report con il percorso di destinazione specificato e la sottoscrizione di Deployment Planner.

## <a name="version-22"></a>Versione 2.2 

**Data di rilascio: 25 aprile 2018**

**Correzioni:**

- Operazioni di GetVMList:
  - Risolto un problema che causava GetVMList esito negativo se la cartella specificata non esiste. Ora consente di creare la directory predefinita oppure crea la directory specificata nel parametro outputfile.
  - Aggiungere che altre cause dell'errore per GetVMList.
- Informazioni di tipo macchina virtuale aggiunta in una colonna nella finestra delle macchine virtuali compatibile del report di Deployment Planner.
- Hyper-V per il ripristino di emergenza di Azure:
  - I dischi esclusione macchine virtuali con dischi rigidi virtuali condivisi e il pass-through di profilatura. L'operazione Startprofiling Mostra l'elenco di macchine virtuali esclusione nella console.
  - Aggiunte le macchine virtuali con più di 64 dischi all'elenco delle VM incompatibili.
  - Aggiornata la replica iniziale (IR) e fattore di compressione differenziale replica (ripristino di emergenza).
  - Aggiunta del supporto limitata per l'archiviazione SMB.

## <a name="version-21"></a>Versione 2.1

**Data di rilascio: 3 gennaio 2018**

**Correzioni:**

- Aggiornare il report di Excel.
- Bug risolti per l'operazione GetThroughput.
- Aggiunta l'opzione per limitare il numero di macchine virtuali da profilare o generare il report. Il limite predefinito è 1.000 VM.
- VMware per il ripristino di emergenza di Azure:
  - Risolto un problema di VM Windows Server 2016 verso la tabella non compatibile. 
  - Aggiornare i messaggi sulla compatibilità per le macchine virtuali Windows di interfaccia EFI (Extensible Firmware Interface).
- Aggiornato VMware ad Azure e Hyper-V ad Azure, dati della macchina virtuale, la varianza del limite per ogni macchina virtuale. 
- Miglioramento dell'affidabilità del file di elenco VM di analisi.

## <a name="version-201"></a>Versione 2.0.1

**Data di rilascio: 7 dicembre 2017**

**Correzioni:**

- Aggiunta raccomandazione per ottimizzare la larghezza di banda di rete.

## <a name="version-20"></a>Versione 2.0

**Data di rilascio: 28 novembre 2017**

**Correzioni:**

- Aggiunta del supporto per Hyper-V per il ripristino di emergenza in Azure.
- Calcolatore di costi aggiuntivi.
- Aggiunta controllo versione sistema operativo per VMware per il ripristino di emergenza in Azure per determinare se la macchina virtuale è compatibile o incompatibile per la protezione. Lo strumento Usa la stringa di versione del sistema operativo restituito dal server vCenter per la macchina virtuale. Si tratta della versione del sistema operativo guest che l'utente ha selezionato durante la creazione della macchina virtuale in VMware.

**Limitazioni note:**

- Per Hyper-V per il ripristino di emergenza in Azure, macchina virtuale con un nome contenente caratteri, ad esempio: `,`, `"`, `[`, `]`, e ``` ` ``` non sono supportati. Se incluso nella profilatura, la generazione del report avranno esito negativo o avranno un risultato errato.
- Per VMware per il ripristino di emergenza in Azure, macchina virtuale con nome contenente virgole non è supportata. Se incluso nella profilatura, si verifica un errore di generazione di report o avranno un risultato errato.

## <a name="version-131"></a>Versione 1.3.1

**Data di rilascio: 19 luglio 2017** 

**Correzioni:**

- Aggiunta del supporto per dischi di grandi dimensioni (> 1 TB) nella generazione del report. Ora è possibile usare Deployment Planner per pianificare la replica di macchine virtuali con dischi di dimensioni superiori a 1 TB (fino a 4095 GB).
Per altre informazioni, vedere il post di blog sul [supporto di dischi di grandi dimensioni in Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/).

## <a name="version-13"></a>Versione 1.3

**Data di rilascio: 9 maggio 2017**

**Correzioni:**

- Aggiunta del supporto per il disco gestito nella generazione del report. Il numero di macchine virtuali che possono essere inseriti in un account di archiviazione singolo viene calcolato in base se si seleziona il disco gestito per il Failover di Test/Failover.

## <a name="version-12"></a>versione 1.2

**Data di rilascio: 7 aprile 2017**

**Correzioni:**

- Avvio aggiunta tipo (BIOS o EFI) verifica che ogni macchina virtuale per determinare se la macchina virtuale è compatibile o incompatibile per la protezione.
- Aggiunta del sistema operativo informazioni sul tipo per ogni macchina virtuale in macchine virtuali compatibili e incompatibili fogli di lavoro per le macchine virtuali.
- Aggiunta del supporto per l'operazione GetThroughput per le aree US Government e Cina di Microsoft Azure.
- Aggiunte alcune altre verifiche dei prerequisiti per server vCenter ed ESXi.
- Risolto un problema di report non corretti vengano generati quando le impostazioni locali sono impostate su inglese.

## <a name="version-11"></a>Versione 1.1

**Data di rilascio: Del 9 marzo 2017**

**Correzioni:**

- Risolto un problema che impediva la profilatura delle VM quando sono presenti due o più macchine virtuali con lo stesso nome o indirizzo IP in host ESXi vCenter differenti.
- Risolto un problema che causava copia e la ricerca deve essere disabilitata per le VM compatibili e incompatibili fogli di lavoro per le macchine virtuali.

## <a name="version-10"></a>Versione 1.0

**Data di rilascio: 23 febbraio 2017**

**Limitazioni note:**

- Supporta solo per VMware in scenari di ripristino di emergenza in Azure. Per Hyper-V per scenari di ripristino di emergenza in Azure, usare il [strumento capacity planner di Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Non supporta l'operazione GetThroughput per le aree US Government e Cina di Microsoft Azure.
- Il profilo strumento le macchine virtuali se il server vCenter ha due o più macchine virtuali con lo stesso nome o indirizzo IP in host ESXi differenti.
In questa versione, lo strumento ignora la profilatura di VM con nomi o indirizzi IP duplicati nel parametro VMListFile. La soluzione alternativa prevede di profilare le VM usando un host ESXi invece del server vCenter. Assicurarsi di eseguire una sola istanza per ogni host ESXi.
