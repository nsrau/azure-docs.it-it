---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: d2312fac64515756f5ed2e0feb22fdc6b7205376
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125178"
---
**Ultimo aggiornamento del documento**: 14 maggio 2019 10 12:00 AM PST.

La divulgazione di una [nuova classe di vulnerabilità della CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), definita attacchi del canale laterale per l'esecuzione speculativa, ha suscitato molte domande e richieste di chiarimenti da parte dei clienti.  

Microsoft ha distribuito soluzioni di mitigazione dei rischi in tutti i servizi cloud. L'infrastruttura che esegue Azure e isola i carichi di lavoro dei clienti gli uni da gli altri è protetta. Ciò significa che un potenziale utente malintenzionato che usa la stessa infrastruttura non potrà attaccare l'applicazione tramite queste vulnerabilità.

Azure usa la [manutenzione con mantenimento della memoria](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot), laddove possibile, per ridurre al minimo l'impatto sul cliente ed eliminare la necessità di riavvii. Azure continuerà a usare questi metodi nell'esecuzione di aggiornamenti a livello di sistema per l'host e per proteggere i clienti.

Altre informazioni sul modo in cui la sicurezza è integrata in ogni aspetto di Azure sono disponibili sul sito relativo alla [documentazione di sicurezza di Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Poiché questo documento è stato pubblicato prima di tutto, sono state comunicate più varianti di questa classe di vulnerabilità. Microsoft continua a investire in modo consistente nella protezione dei clienti e a fornire informazioni aggiuntive. Questa pagina verrà aggiornata per rilasciare altre correzioni. 
> 
> Il 14 maggio 2019 [Intel divulgate](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) un nuovo set di una vulnerabilità del canale sul lato dell'esecuzione speculativa noto come campionamento dei dati della microarchitettura (MDS vedere la Guida alla sicurezza Microsoft [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), ovvero è stato assegnato CVE segnalati in più: 
> - CVE-2019-11091 - i dati della microarchitettura il campionamento di memoria Uncacheable (MDSUM)
> - CVE-2018-12126 - i dati nel Buffer della microarchitettura Store campionamento (MSBDS) 
> - CVE-2018-12127 - porta di carico della microarchitettura dati di campionamento (MLPDS)
> - CVE-2018-12130 - i dati nel Buffer della microarchitettura riempimento campionamento (MFBDS)
>
> Questa vulnerabilità interessa i processori Intel® Core® e Intel® Xeon®.  Microsoft Azure ha rilasciato gli aggiornamenti del sistema operativo ed è la distribuzione di abilitare il microcodice nuovo, come viene reso disponibile da Intel, tutta la flotta per proteggere i clienti su queste nuove vulnerabilità.   Azure collabora strettamente con Intel per testare e convalidare di nuovo abilitare il microcodice prima del rilascio ufficiale della piattaforma. 
>
> **I clienti che eseguono non attendibili di codice all'interno la macchina virtuale** per intraprendere azioni per la protezione da queste vulnerabilità per la lettura di seguito per informazioni aggiuntive su tutte le vulnerabilità del canale laterale dell'esecuzione speculativa (ADV di avvisi di Microsoft [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), e [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Altri clienti devono valutare le vulnerabilità da una difesa nella prospettiva di profondità e considerare le implicazioni di sicurezza e le prestazioni della relativa configurazione scelta.



## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenere aggiornati i sistemi operativi

Benché non sia necessario un aggiornamento del sistema operativo per isolare le applicazioni in esecuzione su Azure da altri clienti Azure, è sempre consigliabile mantenere aggiornate le versioni del software. I più recenti rollup di sicurezza per Windows contengono soluzioni di mitigazione per diverse vulnerabilità di canale sul lato dell'esecuzione speculativa. Analogamente, le distribuzioni Linux hanno rilasciato più aggiornamenti per risolvere tali vulnerabilità. Di seguito sono illustrate le azioni consigliate per l'aggiornamento del sistema operativo:

| Offerta | Azione consigliata  |
|----------|---------------------|
| Servizi cloud di Azure  | Abilitare l'[aggiornamento automatico](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) o assicurarsi che sia in esecuzione il sistema operativo Guest più recente. |
| Macchine virtuali Linux di Azure | Installare aggiornamenti dal provider del sistema operativo. Per altre informazioni, vedere [Linux](#linux) più avanti in questo documento. |
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

Se si esegue codice non attendibile, è possibile abilitare funzionalità di sicurezza aggiuntive all'interno nella macchina virtuale o un servizio Cloud. In parallelo, verificare che il sistema operativo sia aggiornato per abilitare le funzionalità di sicurezza all'interno nella macchina virtuale o un servizio Cloud

### <a name="windows"></a>Windows 

Per abilitare queste funzionalità di sicurezza aggiuntive, il sistema operativo di destinazione deve essere aggiornato. Sebbene numerose mitigazioni di canale sul lato dell'esecuzione speculativa siano abilitate per impostazione predefinita, le funzionalità aggiuntive descritte di seguito devono essere abilitate manualmente e possono causare una riduzione delle prestazioni. 


**Passaggio 1: Disabilitare l'hyperthreading nella macchina virtuale** : i clienti che eseguono codice non attendibile in una macchina virtuale sarà necessario disabilitare l'hyperthreading o spostare in una dimensione di macchina virtuale non Hyper-Threading di Threading. Per verificare se la macchina virtuale con hyperthreading abilitato, consultare il seguito script utilizzando la riga di comando di Windows dall'interno della macchina virtuale.

Tipo `wmic` per accedere all'interfaccia interattiva. Quindi digitare il seguente per visualizzare la quantità di fisica e logica processori nella macchina virtuale.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Se il numero di processori logici è maggiore di processori fisici (Core), viene abilitato l'hyperthreading.  Se si esegue una macchina virtuale Hyper-Threading, please [contattare il supporto tecnico di Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) per ottenere l'Hyper-Threading disabilitato.  Dopo aver disabilitato, l'Hyper-Threading **il supporto richiederà un riavvio della macchina virtuale completo**. 


**Passaggio 2**: In parallelo al passaggio 1, seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per verificare le protezioni sono abilitate tramite il [SpeculationControl](https://aka.ms/SpeculationControlPS) modulo di PowerShell.

> [!NOTE]
> Se il modulo è stato scaricato in precedenza, sarà necessario installare la versione più recente.
>


Deve avere l'output dello script PowerShell di seguito i valori da convalidare abilitate misure di protezione contro le vulnerabilità:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

Se l'output mostra `MDS mitigation is enabled: False`, verificare [contattare il supporto tecnico di Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) per le opzioni di attenuazione disponibili.



**Passaggio 3**: Per abilitare il Kernel virtuale indirizzo Shadowing (KVAS) e il supporto di inserimento destinazione diramazione (BTI) del sistema operativo, seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per abilitare la funzionalità di protezione usando il `Session Manager` le chiavi del Registro di sistema. È necessario riavviare.


**Passaggio 4**: Per le distribuzioni che usano [virtualizzazione nidificata](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 ed E3 solo): Queste istruzioni si applicano all'interno della VM si usa come host Hyper-V.

1.  Seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per abilitare la funzionalità di protezione usando il `MinVmVersionForCpuBasedMitigations` le chiavi del Registro di sistema.
2.  Impostare il tipo di hypervisor dell'utilità di pianificazione `Core` seguendo le istruzioni [qui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>Per abilitare il set di funzionalità di sicurezza aggiuntive all'interno è necessario che il sistema operativo di destinazione sia completamente aggiornato. Alcune soluzioni di mitigazione verranno abilitate per impostazione predefinita. La sezione seguente descrive le funzionalità che sono disattivate per impostazione predefinita e/o fanno affidamento su supporto hardware (microcodice). L'abilitazione di queste funzionalità può causare un impatto sulle prestazioni. Fare riferimento alla documentazione del provider del sistema operativo per altre istruzioni


**Passaggio 1: Disabilitare l'hyperthreading nella macchina virtuale** : i clienti che eseguono codice non attendibile in una macchina virtuale sarà necessario disabilitare l'hyperthreading o spostare una macchina virtuale non Hyper-Threading di Threading.  Per controllare se si esegue una macchina virtuale Hyper-Threading, eseguire il `lscpu` comando nella VM Linux. 

Se `Thread(s) per core = 2`, quindi è stato abilitato l'hyperthreading. 

Se `Thread(s) per core = 1`, quindi l'hyperthreading è stata disabilitata. 

 
Esempio di output per una macchina virtuale con Hyper-Threading abilitato: 

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

Se si esegue una macchina virtuale Hyper-Threading, please [contattare il supporto tecnico di Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) per ottenere l'Hyper-Threading disabilitato.  Dopo aver disabilitato, l'Hyper-Threading **il supporto richiederà un riavvio della macchina virtuale completo**.


**Passaggio 2**: Per ridurre i rischi uno qualsiasi del seguito vulnerabilità del canale laterale dell'esecuzione speculativa, fare riferimento alla documentazione del provider di sistema operativo:   
 
- [RedHat e CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>Passaggi successivi

Questo articolo fornisce indicazioni per la sotto attacchi al canale laterale dell'esecuzione speculativa che influiscono sul numero di processori moderni:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - inserimento destinazione diramazione (BTI)  
- CVE-2017-5754 - isolamento tramite Kernel pagina tabella (si esegue)
- CVE-2018-3639 – Store speculativa Bypass (si esegue) 
 
[L1 Errore Terminal (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - sistemi operativi (OS) e modalità di gestione del sistema (SMM)
- CVE-2018-3646: impatto di Virtual Machine Manager (VMM)

[Campionamento dei dati della microarchitettura](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - i dati della microarchitettura il campionamento di memoria Uncacheable (MDSUM)
- CVE-2018-12126 - i dati nel Buffer della microarchitettura Store campionamento (MSBDS)
- CVE-2018-12127 - porta di carico della microarchitettura dati di campionamento (MLPDS)
- CVE-2018-12130 - i dati nel Buffer della microarchitettura riempimento campionamento (MFBDS)








