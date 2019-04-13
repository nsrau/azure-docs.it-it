---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 4c5b4c5eacd4be751004af551e3753a61873c7a7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551565"
---
**Ultimo aggiornamento del documento**: 14 agosto 2018 10 12:00 AM PST.

La divulgazione di una [nuova classe di vulnerabilità della CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), definita attacchi del canale laterale per l'esecuzione speculativa, ha suscitato molte domande e richieste di chiarimenti da parte dei clienti.  

Microsoft ha distribuito soluzioni di mitigazione dei rischi in tutti i servizi cloud. L'infrastruttura che esegue Azure e isola i carichi di lavoro dei clienti gli uni da gli altri è protetta. Ciò significa che un potenziale utente malintenzionato che usa la stessa infrastruttura non potrà attaccare l'applicazione tramite queste vulnerabilità.

Azure usa la [manutenzione con mantenimento della memoria](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot), laddove possibile, per ridurre al minimo l'impatto sul cliente ed eliminare la necessità di riavvii. Azure continuerà a usare questi metodi nell'esecuzione di aggiornamenti a livello di sistema per l'host e per proteggere i clienti.

Altre informazioni sul modo in cui la sicurezza è integrata in ogni aspetto di Azure sono disponibili sul sito relativo alla [documentazione di sicurezza di Azure](https://docs.microsoft.com/azure/security/). 

> [!NOTE] 
> Poiché questo documento è stato pubblicato prima di tutto, sono state comunicate più varianti di questa classe di vulnerabilità. Microsoft continua a investire in modo consistente nella protezione dei clienti e a fornire informazioni aggiuntive. Questa pagina verrà aggiornata per rilasciare altre correzioni. 
> 
> Il 14 agosto 2018, il settore ha divulgato una nuova vulnerabilità di canale sul lato dell'esecuzione speculativa, nota come [L1 Terminal Fault](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF), a cui sono stati assegnati più CVE ([CVE-2018-3615, CVE-2018-3620 e CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Questa vulnerabilità interessa i processori Intel® Core® e Intel® Xeon®. Attraverso i propri servizi cloud, Microsoft ha distribuito soluzioni di mitigazione che rafforzano l'isolamento tra i clienti. Leggere di seguito per altro materiale sussidiario per la protezione da L1TF e da vulnerabilità precedenti ([Spectre Variant 2 CVE-2017-5715 e Meltdown Variant 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenere aggiornato il sistema operativo

Benché non sia necessario un aggiornamento del sistema operativo per isolare le applicazioni in esecuzione su Azure da altri clienti Azure, è sempre consigliabile mantenere aggiornate le versioni del software. I più recenti rollup di sicurezza per Windows contengono soluzioni di mitigazione per diverse vulnerabilità di canale sul lato dell'esecuzione speculativa. Analogamente, le distribuzioni Linux hanno rilasciato più aggiornamenti per risolvere tali vulnerabilità. Di seguito sono illustrate le azioni consigliate per aggiornare il sistema operativo:

| Offerta | Azione consigliata  |
|----------|---------------------|
| Servizi cloud di Azure  | Abilitare l'[aggiornamento automatico](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) o assicurarsi che sia in esecuzione il sistema operativo Guest più recente. |
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

È possibile abilitare funzionalità di sicurezza aggiuntive all'interno nella macchina virtuale o in un servizio Cloud.

### <a name="windows"></a>Windows 

Per abilitare queste funzionalità di sicurezza aggiuntive, il sistema operativo di destinazione deve essere aggiornato. Sebbene numerose mitigazioni di canale sul lato dell'esecuzione speculativa siano abilitate per impostazione predefinita, le funzionalità aggiuntive descritte di seguito devono essere abilitate manualmente e possono causare una riduzione delle prestazioni. 

**Passaggio 1**: [Contattare il supporto tecnico di Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) espongono aggiornato del firmware (abilitare il microcodice) in macchine virtuali. 

**Passaggio 2**: Abilitare il supporto del Kernel virtuale indirizzo Shadowing (KVAS) e inserimento destinazione diramazione (BTI) del sistema operativo. Seguire le istruzioni riportate in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per abilitare le funzionalità di protezione tramite le chiavi del Registro di sistema `Session Manager`. È necessario riavviare. 

**Passaggio 3**: Per le distribuzioni che usano [virtualizzazione nidificata](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 ed E3 solo): Queste istruzioni si applicano all'interno della VM si usa come host Hyper-V. 

1. Seguire le istruzioni riportate in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per abilitare le funzionalità di protezione tramite le chiavi del Registro di sistema `MinVmVersionForCpuBasedMitigations`.  
 
1. Impostare il tipo di utilità di pianificazione dell'hypervisor **Core** seguendo le istruzioni riportate [qui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**Passaggio 4**: Seguire le istruzioni in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per verificare le protezioni sono abilitate tramite il [SpeculationControl](https://aka.ms/SpeculationControlPS) modulo di PowerShell. 

> [!NOTE]
> Se il modulo è stato scaricato in precedenza, sarà necessario installare la versione più recente.
>

Tutte le macchine virtuali devono mostrare:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>Per abilitare il set di funzionalità di sicurezza aggiuntive all'interno è necessario che il sistema operativo di destinazione sia completamente aggiornato. Alcune soluzioni di mitigazione verranno abilitate per impostazione predefinita. La sezione seguente descrive le funzionalità che sono disattivate per impostazione predefinita e/o fanno affidamento su supporto hardware (microcodice). L'abilitazione di queste funzionalità può causare un impatto sulle prestazioni. Fare riferimento alla documentazione del provider del sistema operativo per altre istruzioni
 
**Passaggio 1**: [Contattare il supporto tecnico di Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) espongono aggiornato del firmware (abilitare il microcodice) in macchine virtuali.
 
**Passaggio 2**: Abilitare il supporto di inserimento destinazione diramazione (BTI) del sistema operativo mitigare CVE-2017-5715 (di Spectre variante 2), seguire la documentazione del provider di sistema operativo. 
 
**Passaggio 3**: Abilitare Kernel pagina tabella isolamento si esegue per mitigare CVE-2017-5754 (Meltdown Variant 3), seguire la documentazione del provider di sistema operativo. 
 
Altre informazioni sono disponibili presso il provider del sistema operativo:  
 
- [RedHat e CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [Suse](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protezione dei clienti di Azure dalla vulnerabilità della CPU).
