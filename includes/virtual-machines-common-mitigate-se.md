---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935898"
---
**Ultimo aggiornamento del documento**: 12 novembre 2019 10:00 PST.

La divulgazione di una [nuova classe di vulnerabilità della CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), definita attacchi del canale laterale per l'esecuzione speculativa, ha suscitato molte domande e richieste di chiarimenti da parte dei clienti.  

Microsoft ha distribuito soluzioni di mitigazione dei rischi in tutti i servizi cloud. L'infrastruttura che esegue Azure e isola i carichi di lavoro dei clienti gli uni da gli altri è protetta. Ciò significa che un potenziale utente malintenzionato che usa la stessa infrastruttura non potrà attaccare l'applicazione tramite queste vulnerabilità.

Azure usa la [manutenzione con mantenimento della memoria](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot), laddove possibile, per ridurre al minimo l'impatto sul cliente ed eliminare la necessità di riavvii. Azure continuerà a usare questi metodi nell'esecuzione di aggiornamenti a livello di sistema per l'host e per proteggere i clienti.

Altre informazioni sul modo in cui la sicurezza è integrata in ogni aspetto di Azure sono disponibili sul sito relativo alla [documentazione di sicurezza di Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Poiché questo documento è stato pubblicato prima di tutto, sono state comunicate più varianti di questa classe di vulnerabilità. Microsoft continua a investire in modo consistente nella protezione dei clienti e a fornire informazioni aggiuntive. Questa pagina verrà aggiornata per rilasciare altre correzioni. 
> 
> Il 12 novembre 2019, [Intel ha pubblicato](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) un advisory tecnico su Intel® vulnerabilità delle transactional synchronization Extensions (Intel® TSX) Transaction Abort (TAA) a cui è assegnato [CVE-2019-11135.](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) Questa vulnerabilità interessa i processori Intel® Core® e Intel® Xeon®.  Microsoft Azure ha rilasciato gli aggiornamenti del sistema operativo e sta implementando un nuovo microcodice, come viene reso disponibile da Intel, in tutta la nostra flotta per proteggere i nostri clienti da queste nuove vulnerabilità.   Azure sta lavorando a stretto contatto con Intel per testare e convalidare il nuovo microcodice prima del suo rilascio ufficiale sulla piattaforma. 
>
> **I clienti che eseguono codice non attendibile all'interno** della macchina virtuale devono intraprendere azioni per la protezione da queste vulnerabilità leggendo di seguito ulteriori informazioni su tutte le vulnerabilità del canale laterale di esecuzione speculativa (Microsoft Advisories ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Altri clienti devono valutare queste vulnerabilità dal punto di vista di difesa in profondità e considerare le implicazioni di sicurezza e prestazioni della configurazione scelta.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenere aggiornati i sistemi operativi

Benché non sia necessario un aggiornamento del sistema operativo per isolare le applicazioni in esecuzione su Azure da altri clienti Azure, è sempre consigliabile mantenere aggiornate le versioni del software. I più recenti rollup di sicurezza per Windows contengono soluzioni di mitigazione per diverse vulnerabilità di canale sul lato dell'esecuzione speculativa. Analogamente, le distribuzioni Linux hanno rilasciato più aggiornamenti per risolvere tali vulnerabilità. Ecco le nostre azioni consigliate per aggiornare il sistema operativo:

| Offerta | Azione consigliata  |
|----------|---------------------|
| Servizi cloud di Azure  | Abilitare [l'aggiornamento automatico](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) o assicurarsi di eseguire il sistema operativo guest più recente. |
| Macchine virtuali Linux in Azure | Installare aggiornamenti dal provider del sistema operativo. Per altre informazioni, vedere [Linux](#linux) più avanti in questo documento. |
| Macchine virtuali Windows in Azure  | Installare il rollup della sicurezza più recente.
| Altri servizi PaaS di Azure | Non sono necessarie azioni da parte dei clienti che usano questi servizi. Azure aggiorna automaticamente le versioni del sistema operativo. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Indicazioni aggiuntive in caso di esecuzione di codice non attendibile 

Per i clienti che consentono agli utenti non attendibili l'esecuzione arbitraria del codice potrebbe essere necessario implementare alcune funzionalità di sicurezza aggiuntive all'interno delle macchine virtuali di Azure o servizi Cloud. Queste funzionalità proteggono contro i vettori di diffusione all'interno del processo descritti da numerose vulnerabilità di esecuzione speculativa.

Scenari di esempio in cui sono consigliate le funzionalità di sicurezza aggiuntive:

- Si consente un codice non attendibile per l'esecuzione all'interno della macchina virtuale.  
    - *Ad esempio, si consente a un cliente di caricare un file binario o uno script che viene quindi eseguito all'interno dell'applicazione*. 
- Si consente a utenti non attendibili di accedere alla macchina virtuale usando account con privilegi bassi.   
    - *Ad esempio, si consente a un utente con privilegi limitati di accedere a una delle macchine virtuali tramite desktop remoto o SSH*.  
- Si consente a utenti non attendibili l'accesso alle macchine virtuali implementate tramite virtualizzazione annidata.  
    - *Ad esempio, si controlla l'host Hyper-V, ma si allocano le macchine virtuali a utenti non attendibili*. 

I clienti che non implementano uno scenario che include un codice non attendibile non devono abilitare queste funzionalità aggiuntive di sicurezza. 

## <a name="enabling-additional-security"></a>Abilitazione della sicurezza aggiuntiva 

È possibile abilitare funzionalità di sicurezza aggiuntive all'interno della macchina virtuale o del servizio cloud se si esegue codice non attendibile. In parallelo, verificare che il sistema operativo sia aggiornato per abilitare le funzionalità di sicurezza all'interno della macchina virtuale o del servizio cloud

### <a name="windows"></a>WINDOWS 

Per abilitare queste funzionalità di sicurezza aggiuntive, il sistema operativo di destinazione deve essere aggiornato. Sebbene numerose mitigazioni di canale sul lato dell'esecuzione speculativa siano abilitate per impostazione predefinita, le funzionalità aggiuntive descritte di seguito devono essere abilitate manualmente e possono causare una riduzione delle prestazioni. 


**Passaggio 1: Disabilitare l'hyperthreading nella macchina virtuale:** i clienti che eseguono codice non attendibile in una macchina virtuale con hyperthreading dovranno disabilitare l'hyperthreading o passare a una dimensione di macchina virtuale non con hyperthreading. Fare riferimento a [questo documento](https://docs.microsoft.com/azure/virtual-machines/windows/acu) per un elenco di dimensioni di VM con hyperthreading (dove il rapporto tra vCPU e Core è 2:1). Per verificare se la macchina virtuale è abilitata per l'hyperthreading, fare riferimento allo script seguente usando la riga di comando di Windows all'interno della macchina virtuale.

Digitare `wmic` per accedere all'interfaccia interattiva. Digitare quindi il codice seguente per visualizzare la quantità di processori fisici e logici nella macchina virtuale.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se il numero di processori logici è maggiore di processori fisici (core), l'hyperthreading è abilitato.  Se si esegue una macchina virtuale con hyperthreading, [contattare](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) il supporto di Azure per disabilitare l'hyperthreading.  Una volta disabilitato l'hyperthreading, **il supporto richiederà un riavvio completo della macchina virtuale.** Fare riferimento a [Conteggio core](#core-count) per comprendere il motivo per cui il conteggio dei core della macchina virtuale è diminuito.


**Passaggio 2**: in parallelo al passaggio 1, seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per verificare che le protezioni siano abilitate utilizzando il modulo [PowerShell SpeculationControl.](https://aka.ms/SpeculationControlPS)

> [!NOTE]
> Se il modulo è stato scaricato in precedenza, sarà necessario installare la versione più recente.
>


L'output dello script di PowerShell deve avere i valori seguenti per convalidare le protezioni abilitate da queste vulnerabilità:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Se l'output viene visualizzato, `MDS mitigation is enabled: False`contattare il supporto di [Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) per le opzioni di attenuazione disponibili.



**Passaggio 3**: Per abilitare il supporto del sistema operativo Kernel Virtual Address Shadowing (KVAS) e Branch Target Injection `Session Manager` (BTI), seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per abilitare le protezioni utilizzando le chiavi del Registro di sistema. È necessario riavviare.


**Passaggio 4:** per le distribuzioni che usano la [virtualizzazione annidata](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (solo D3 ed E3): queste istruzioni si applicano all'interno della macchina virtuale in uso come host Hyper-V.

1.  Seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per `MinVmVersionForCpuBasedMitigations` abilitare le protezioni utilizzando le chiavi del Registro di sistema.
2.  Impostare il tipo di `Core` utilità di pianificazione dell'hypervisor su seguendo le istruzioni [riportate di seguito.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)


### <a name="linux"></a>Linux

<a name="linux"></a>Per abilitare il set di funzionalità di sicurezza aggiuntive all'interno è necessario che il sistema operativo di destinazione sia completamente aggiornato. Alcune soluzioni di mitigazione verranno abilitate per impostazione predefinita. La sezione seguente descrive le funzionalità che sono disattivate per impostazione predefinita e/o fanno affidamento su supporto hardware (microcodice). L'abilitazione di queste funzionalità può causare un impatto sulle prestazioni. Fare riferimento alla documentazione del provider del sistema operativo per altre istruzioni


**Passaggio 1: Disabilitare l'hyperthreading nella macchina virtuale:** i clienti che eseguono codice non attendibile in una macchina virtuale con hyperthreading dovranno disabilitare l'hyperthreading o passare a una macchina virtuale non con hyperthreading.  Fare riferimento a [questo documento](https://docs.microsoft.com/azure/virtual-machines/linux/acu) per un elenco di dimensioni di VM con hyperthreading (dove il rapporto tra vCPU e Core è 2:1). Per verificare se è in esecuzione una `lscpu` macchina virtuale con hyperthreading, eseguire il comando nella macchina virtuale Linux.To check if you are running a hyper-threaded VM, run the command in the Linux VM. 

Se `Thread(s) per core = 2`, l'hyperthreading è stato abilitato. 

Se `Thread(s) per core = 1`, l'hyperthreading è stato disabilitato. 

 
Output di esempio per una macchina virtuale con hyperthreading abilitato:Sample output for a VM with hyper-threading enabled: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Se si esegue una macchina virtuale con hyperthreading, [contattare](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) il supporto di Azure per disabilitare l'hyperthreading.  Una volta disabilitato l'hyperthreading, **il supporto richiederà un riavvio completo della macchina virtuale.** Fare riferimento a [Conteggio core](#core-count) per comprendere il motivo per cui il conteggio dei core della macchina virtuale è diminuito.



**Passaggio 2**: Per mitigare le seguenti vulnerabilità del canale laterale di esecuzione speculativa, fare riferimento alla documentazione del provider del sistema operativo:   
 
- [RedHat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [Suse](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Conteggio core

Quando viene creata una macchina virtuale con hyperthreading, Azure alloca 2 thread per core, denominati vCPU. Quando l'hyperthreading è disabilitato, Azure rimuove un thread e si riduce a core a thread singolo (core fisici). Il rapporto tra vCPU e CPU è 2:1, quindi una volta che l'hyperthreading è disabilitato, il numero di CPU nella macchina virtuale sembrerà essere diminuito della metà. Ad esempio, una macchina virtuale D8_v3 è una macchina virtuale con hyperthreading in esecuzione su 8 vCPU (2 thread per core x 4 core).  Quando l'hyperthreading è disabilitato, le CPU scenderanno a 4 core fisici con 1 thread per core. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo vengono fornite indicazioni per i seguenti attacchi di canale laterale di esecuzione speculativa che interessano molti processori moderni:This article provides guidance to the below speculative execution side-channel attacks that affect many modern processors:

[Scioglimento dello Spettro](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - Iniezione di destinazione diramazione (BTI)  
- CVE-2017-5754 - Isolamento tabella pagine kernel (KPTI)
- CVE-2018-3639 – Bypass speculativo del negozio (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows Kernel Information – variante di Spectre Variant 1
 
[Guasto terminale L1 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - Sistemi operativi (OS) e modalità di gestione del sistema (SMM)
- CVE-2018-3646 – impatti Virtual Machine Manager (VMM)

[Campionamento dei dati microarchitettonici](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - Microarchitectural Data Sampling Non cacheable Memory (MDSUM)
- CVE-2018-12126 - Campionamento dati buffer dell'archivio microarchitettonico (MSBDS)
- CVE-2018-12127 - Campionamento dati porta di carico microarchitettonica (MLPDS)
- CVE-2018-12130 - Campionamento dati buffer di riempimento microarchitettonico (MFBDS)

Interruzione asincrona transazione delle estensioni di sincronizzazione transazionale (Intel® TSX):  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Transaction Asynchronous Abort (TAA)








