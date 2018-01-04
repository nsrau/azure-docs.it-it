---
title: Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager | Documentazione Microsoft
description: Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: 246032701d97fc7d16e6cb38ee79fbd5470f65d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager
Anche se Azure Resource Manager offre molte funzionalità straordinarie, è fondamentale pianificare la migrazione in modo che avvenga senza problemi. Dedicare tempo alla pianificazione garantisce che non si verifichino problemi durante l'esecuzione delle attività di migrazione. 

> [!NOTE] 
> Alle linee guida seguenti hanno fornito un importante contributo il team Azure Customer Advisory e gli architetti delle soluzioni cloud che lavorano con i clienti per la migrazione ambienti di grandi dimensioni. È consigliabile controllare questo documento di tanto in tanto perché verrà aggiornato mano a mano che emergeranno nuovi modelli di successo.

Il percorso di migrazione include quattro fasi generali:

![Fasi di migrazione](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Pianificazione

### <a name="technical-considerations-and-tradeoffs"></a>Considerazioni tecniche e compromessi

In base alla quantità di requisiti tecnici, alle aree geografiche e alle procedure operative, è necessario considerare quanto segue:

1. Perché si vuole usare Azure Resource Manager per l'organizzazione?  Quali sono i motivi aziendali della scelta della migrazione?
2. Quali sono i motivi tecnici della scelta di Azure Resource Manager?  Quali eventuali servizi aggiuntivi di Azure si desidera sfruttare?
3. Quale applicazione (o set di macchine virtuali) è inclusa nella migrazione?
4. Quali scenari sono supportati con l'API di migrazione?  Esaminare le [funzionalità e configurazioni non supportate](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. I team operativi supporteranno le applicazioni/macchine virtuali sia nel modello di distribuzione classica che in Azure Resource Manager?
6. Come cambieranno eventualmente i processi di distribuzione, gestione, monitoraggio e report delle VM con Azure Resource Manager?  Gli script di distribuzione devono essere aggiornati?
7. Come è organizzato il piano delle comunicazioni per informare gli stakeholder ossia gli utenti finali, i proprietari delle applicazioni e i proprietari delle infrastrutture?
8. A seconda della complessità dell'ambiente, è previsto un periodo di manutenzione in cui l'applicazione non sarà disponibile per gli utenti finali e i proprietari dell'applicazione?  In questo caso, per quanto tempo?
9. Come è organizzato il piano di formazione per l'addestramento e la conoscenza di Azure Resource Manager per gli stakeholder?
10. Come è organizzato il piano di gestione del progetto o del programma per la migrazione?
11. Quali sono le sequenze temporali della migrazione di Azure Resource Manager e gli altri piani d'azione correlati alla tecnologia?  Sono allineati in modo ottimale?

### <a name="patterns-of-success"></a>Modelli di successo

I clienti di successo hanno piani dettagliati in cui le domande precedenti sono discusse, documentate e organizzate.  Assicurarsi che i piani di migrazione vengano dettagliatamente comunicati agli sponsor e agli stakeholder.  Acquisire familiarità con le opzioni di migrazione; è consigliabile leggere i documenti sulla migrazione qui di seguito.

* [Overview of platform-supported migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Panoramica sulla migrazione supportata dalla piattaforma per risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Errori da evitare

- Errori di pianificazione.  I passaggi tecnici della migrazione sono collaudati e il risultato è prevedibile.
- Si presuppone che l'API per la migrazione supportata dalla piattaforma terrà in considerazione tutti gli scenari. Leggere la sezione [funzionalità e configurazioni non supportate](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) per comprendere quali sono gli scenari supportati.
- Nessuna pianificazione di potenziali interruzioni delle applicazioni per gli utenti finali.  Pianificare un buffer sufficiente per informare adeguatamente gli utenti finali della potenziale non disponibilità delle applicazioni.


## <a name="lab-test"></a>Test di laboratorio 

**Replicare l'ambiente ed eseguire una migrazione di test**
  > [!NOTE]
  > La replica esatta dell'ambiente esistente viene eseguita tramite uno strumento creato dalla community che non è ufficialmente supportato dal supporto tecnico Microsoft. È pertanto un passaggio **facoltativo** ma è il modo migliore per individuare i problemi senza modificare gli ambienti di produzione. Se non è possibile usare uno strumento creato dalla community, leggere le raccomandazioni su convalida/preparazione/interruzione di prova qui di seguito.
  >
  
  L'esecuzione di un test di laboratorio dello scenario esatto ossia calcolo, rete e archiviazione è il modo migliore per garantire una migrazione senza problemi. In modo da garantire:

  - Un laboratorio completamente separato o un ambiente non di produzione esistente su cui eseguire test. È consigliabile un laboratorio totalmente separato che possa essere migrato ripetutamente e modificato in modo distruttivo.  Gli script per raccogliere/idratare i metadati delle sottoscrizioni reali sono elencati di seguito.
  - È consigliabile creare il laboratorio in una sottoscrizione separata. Il motivo è che il laboratorio verrà ripetutamente rimosso e avere una sottoscrizione separata riduce il rischio di eliminare accidentalmente componenti reali.

  Questa operazione si può fare usando lo strumento AsmMetadataParser. [Per altre informazioni su questo strumento vedere qui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Modelli di successo

Di seguito sono elencati i problemi rilevati in molte migrazioni di grandi dimensioni. Non si tratta di un elenco completo. Per altri dettagli fare riferimento a [funzionalità e configurazioni non supportate](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations). Questi problemi tecnici potrebbero anche non verificarsi, ma se vengono risolti prima della migrazione, questa sarà più semplice.

- **Eseguire una convalida/preparazione/interruzione di prova** - Questo passaggio è probabilmente quello più importante per garantire una corretta migrazione dal modello di distribuzione classica ad Azure Resource Manager. L'API di migrazione prevede tre passaggi principali: Convalida, Preparazione e Commit. La convalida leggerà lo stato dell'ambiente di distribuzione classica e restituirà come risultato tutti i problemi. Alcuni problemi tuttavia potrebbero non essere rilevati in quanto presenti nello stack di Azure Resource Manager. Il passaggio successivo nel processo di migrazione, la preparazione, consente di rilevare questi problemi. La preparazione sposterà i metadati dal modello di distribuzione classica ad Azure Resource Manager ma non eseguirà il commit dello spostamento e non rimuoverà o modificherà nulla nel modello di distribuzione classica. L'interruzione di prova consiste nel preparare la migrazione e quindi di interrompere la preparazione ossia **non eseguire il commit**. L'obiettivo di convalida/preparazione/interruzione di prova è visualizzare tutti i metadati nello stack Azure Resource Manager, esaminarli (*a livello di codice o nel portale*), verificare che la migrazione avvenga correttamente e risolvere i problemi tecnici.  Inoltre si avrà un'idea della durata della migrazione in modo che sia possibile pianificare di conseguenza i tempi di inattività.  Una convalida/preparazione/interruzione di prova non causa tempi di inattività e pertanto non crea problemi per l'uso delle applicazioni.
  - Gli elementi seguenti dovranno essere risolti prima della prova, ma un test di interruzione di prova rileverà anche eventuali passaggi di preparazione mancanti. Durante la migrazione al livello enterprise, è stato rilevato che la prova è un modo sicuro e prezioso per semplificare la migrazione.
  - Nella fase di preparazione il piano di controllo, ossia le operazioni di gestione di Azure, verrà bloccato per tutta la rete virtuale e pertanto non sarà possibile apportare modifiche ai metadati delle macchine virtuali durante la convalida/preparazione/interruzione.  A parte questo, tutte le funzioni delle applicazioni, ad esempio l'uso del desktop remoto, le macchine virtuali e così via, non saranno interessate.  Gli utenti delle VM non sapranno che è in esecuzione la prova.

- **Circuiti ExpressRoute e VPN**. I gateway ExpressRoute con collegamenti di autorizzazione attualmente non possono essere migrati senza tempi di inattività. Per una soluzione vedere [Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Estensioni VM** - Le estensioni macchina virtuale sono potenzialmente uno degli ostacoli principali della migrazione di macchine virtuali in esecuzione. Pianificare tenendo conto che la correzione delle estensioni VM potrebbe richiedere fino a 1-2 giorni.  È necessario un agente Azure funzionante per segnalare lo stato delle estensioni VM delle VM in esecuzione. Se per una VM in esecuzione viene restituito uno stato non valido, la migrazione si arresta. L'agente stesso non deve essere funzionante per abilitare la migrazione, ma se esistono estensioni nella macchina virtuale, per il progredire della migrazione saranno necessari sia un agente funzionante SIA una connessione Internet in uscita (con DNS).
  - Se la connettività a un server DNS viene persa durante la migrazione, tutte le estensioni delle VM tranne BGInfo versione 1.\* devono essere rimosse da ogni VM prima della preparazione della migrazione e quindi aggiunte di nuovo alla VM dopo la migrazione ad Azure Resource Manager.  **Questo vale solo per le VM in esecuzione.**  Se le macchine virtuali sono arrestate (deallocate), non è necessario rimuovere le estensioni VM. **Nota:** molte estensioni come la diagnostica Azure e il monitoraggio del centro sicurezza si reinstalleranno automaticamente dopo la migrazione, per cui la loro rimozione non è un problema.
  - Assicurarsi inoltre che non ci siano Gruppi di sicurezza di rete che limitano l'accesso Internet in uscita. Questa situazione può verificarsi con alcune configurazioni di Gruppi di sicurezza di rete. Per la migrazione delle estensioni VM ad Azure Resource Manager è necessario l'accesso a Internet in uscita e DNS. 
  - Esistono due versioni dell'estensione BGInfo: v1 e v2.  Se la macchina virtuale è stata creata tramite il portale di Azure o PowerShell, la macchina virtuale avrà probabilmente l'estensione v1 su di esso. Questa estensione non deve essere rimossa e verrà ignorata (non migrata) dall'API di migrazione. Tuttavia, se la macchina virtuale classica è stata creata con il nuovo portale di Azure, probabilmente avrà la versione v2 basata su JSON di BGInfo, che è possibile migrare ad Azure Resource Manager se l'agente funziona e ha accesso a Internet in uscita e DNS. 
  - **Opzione di correzione 1**. Se si prevede che le macchine virtuali non disporranno di accesso a Internet in uscita, di un servizio DNS funzionante e di agenti di Azure funzionanti su di esse, disinstallare tutte le estensioni VM come parte della migrazione prima della preparazione, quindi reinstallarle dopo la migrazione. 
  - **Opzione di correzione 2**. Se le estensioni VM sono un ostacolo troppo grande, un'altra opzione è quella di arrestare/deallocare tutte le macchine virtuali prima della migrazione. Eseguire la migrazione delle VM deallocate, quindi riavviarle in Azure Resource Manager. Il vantaggio è che le estensioni VM verranno migrate. Lo svantaggio è che tutti gli indirizzi IP virtuali pubblici andranno persi con esito potenzialmente negativo e ovviamente le VM si arresteranno causando un impatto maggiore sulle applicazioni in funzione.

    > [!NOTE] 
    > Se per le macchine virtuali che si stanno migrando sono configurati criteri del Centro sicurezza di Azure, i criteri di sicurezza devono essere arrestati prima di rimuovere le estensioni altrimenti l'estensione di monitoraggio della protezione verrà reinstallata automaticamente nella macchina virtuale dopo la sua rimozione.

- **Set di disponibilità**. Per una rete virtuale (vNet) da migrare ad Azure Resource Manager, è necessario che le macchine virtuali contenute nel modello di distribuzione classica, ossia il servizio cloud, siano tutte in un set di disponibilità oppure che nessuna di esse sia in un set di disponibilità. Il servizio cloud con più di un set di disponibilità non è compatibile con Azure Resource Manager e interromperà la migrazione.  Non ci possono essere inoltre alcune macchine virtuali in un set di disponibilità e alcune macchine virtuali non in un set di disponibilità. Per risolvere questo problema, è necessario correggere o ridisporre il servizio cloud.  Pianificare di conseguenza, in quanto questa operazione potrebbe richiedere molto tempo. 

- **Distribuzioni del ruolo Web/di lavoro**. I servizi Cloud che contengono ruoli Web e di lavoro non possono migrare ad Azure Resource Manager. I ruoli Web/di lavoro devono essere rimossi dalla rete virtuale prima di avviare la migrazione.  Una soluzione tipica è quella di spostare le istanze del ruolo Web/di lavoro in una rete virtuale classica separata che è collegata anche a un circuito ExpressRoute o di migrare il codice a servizi app PaaS più recenti (questa discussione va oltre l'ambito di questo documento). Nel primo caso di ridistribuzione, creare una nuova rete virtuale classica, spostare/ridistribuire i ruoli Web/di lavoro nella nuova rete virtuale, quindi eliminare le distribuzioni dalla rete virtuale da spostare. Non è necessaria alcuna modifica nel codice. La nuova funzionalità [Peering reti virtuali](../../virtual-network/virtual-network-peering-overview.md) può essere usata per eseguire il peering della rete virtuale classica contenente i ruoli Web/di lavoro e di altre reti virtuali nella stessa area di Azure, ad esempio la rete virtuale che si sta migrando, **dopo il completamento della migrazione della rete virtuale poiché le reti virtuali sottoposte a peering non possono essere migrate**, garantendo le stesse funzionalità senza perdita di prestazioni e senza problemi di larghezza di banda/latenza. Con l'aggiunta del [Peering reti virtuali](../../virtual-network/virtual-network-peering-overview.md) le distribuzioni del ruolo Web/di lavoro ora possono essere facilmente ridotte e non bloccano la migrazione ad Azure Resource Manager.

- **Le quote di Azure Resource Manager** - Le aree di Azure hanno di quote/limiti separati per il modello di distribuzione classica e per Azure Resource Manager. Anche se in uno scenario di migrazione non è usato nuovo hardware in quanto *si stanno scambiando macchine virtuali esistenti dalla distribuzione classica ad Azure Resource Manager*, le quote di Azure Resource Manager devono comunque avere una capacità sufficiente prima di avviare la migrazione. Di seguito sono elencati i principali limiti che causano problemi.  Aprire un ticket di supporto di quota per aumentare i limiti. 

    > [!NOTE]
    > Questi limiti devono essere aumentati nella stessa area dell'ambiente da migrare.
    >

    - Interfacce di rete
    - Servizi di bilanciamento del carico
    - Indirizzi IP pubblici
    - Indirizzi IP pubblici statici
    - Core
    - Gruppi di sicurezza di rete
    - Tabelle di route

    È possibile controllare le quote correnti Azure Resource Manager usando i seguenti comandi con la versione più recente dell'interfaccia della riga di comando 2.0.

    **Calcolo** *(memoria centrale, set di disponibilità)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **Rete***(reti virtuali, indirizzi IP statici, indirizzi IP pubblici, gruppi di sicurezza di rete, interfacce di rete, bilanciamenti del carico, tabelle route)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Risorsa di archiviazione** *(account di archiviazione)*
    
    ```bash
    az storage account show-usage
    ```

- **Limitazioni dell'API di Azure Resource Manager** - In presenza di un ambiente sufficientemente grande, ad esempio > 400 VM in una rete virtuale, è possibile che vengano raggiunti i limiti predefiniti per le scritture dell'API (attualmente **1200 scritture/ora**) in Azure Resource Manager. Prima di iniziare la migrazione, è necessario generare un ticket di supporto per aumentare questo limite per la sottoscrizione.

- **Il provisioning ha raggiunto il timeout dello stato della VM**: se lo stato di qualsiasi VM è **Timeout provisioning**, questo problema deve essere risolto prima della migrazione. L'unico modo per eseguire questa operazione è con il tempo di inattività mediante il deprovisioning/nuovo provisioning della macchina virtuale: eliminarla, mantenere il disco e ricreare la macchina virtuale. 

- **Stato RoleStateUnknown della VM**: se la migrazione si arresta a causa del messaggio di errore **Stato del ruolo sconosciuto**, controllare la VM usando il portale e verificare che sia in esecuzione. Questo errore in genere scompare da solo (nessuna correzione necessaria) dopo alcuni minuti, è spesso temporaneo e si verifica durante un'operazione di **avvio**, **arresto** o **riavvio** di una macchina virtuale. **Procedura consigliata:** ritentare nuovamente la migrazione dopo alcuni minuti. 

- **Cluster di infrastruttura inesistente**: in alcuni casi, alcune VM non possono essere migrate per vari motivi. Uno di questi casi noti è se la VM è stata creata di recente, circa da una settimana, ed è stata indirizzata a un cluster di Azure non ancora configurato per carichi di lavoro di Azure Resource Manager.  Verrà visualizzato un messaggio di errore che indica che **il cluster non esiste** e che non è possibile eseguire la migrazione della VM. Di solito è sufficiente attendere un paio di giorni per risolvere il problema in quanto il cluster verrà abilitato per Azure Resource Manager. Una soluzione immediata è `stop-deallocate` la VM, quindi proseguire con la migrazione e riavviare la VM in Azure Resource Manager dopo la migrazione.

### <a name="pitfalls-to-avoid"></a>Errori da evitare

- Non usare scorciatoie e non eseguire le operazioni di convalida/preparazione/interruzione di prova della migrazione.
- La maggior parte, se non tutti i potenziali problemi, verrà rilevata durante i passaggi di convalida/preparare/interruzione.

## <a name="migration"></a>Migrazione

### <a name="technical-considerations-and-tradeoffs"></a>Considerazioni tecniche e compromessi

A questo punto è possibile iniziare perché sono stati affrontati i problemi noti dell'ambiente.

Per le migrazioni reali, considerare quanto segue:

1. Pianificare la rete virtuale, la più piccola unità di migrazione, con priorità crescente.  Iniziare con le reti virtuali semplici e proseguire con le reti virtuali più complesse.
2. La maggior parte dei clienti avrà ambienti non di produzione e di produzione.  Pianificare per ultimo l'ambiente di produzione.
3. **(FACOLTATIVO)**  Pianificare un tempo di inattività di manutenzione con molto buffer nel caso in cui si verifichino problemi imprevisti.
4. Comunicare e allinearsi con i team di supporto nel caso in cui si verifichino problemi.

### <a name="patterns-of-success"></a>Modelli di successo

Le indicazioni tecniche fornite dalla sezione Test Lab in precedenza devono essere considerate e risolte prima della migrazione reale.  Grazie a test adeguati, la migrazione non è un avvenimento eccezionale.  Per gli ambienti di produzione, potrebbe essere utile disporre di ulteriore supporto, ad esempio un partner Microsoft attendibile o i servizi Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Errori da evitare

Test incompleti possono causare problemi e ritardare la migrazione.  

## <a name="beyond-migration"></a>Oltre la migrazione

### <a name="technical-considerations-and-tradeoffs"></a>Considerazioni tecniche e compromessi

Ora che si è in Azure Resource Manager, ottimizzare la piattaforma.  Leggere la sezione [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/resource-group-overview.md) per scoprire i vantaggi aggiuntivi.

Ecco alcuni aspetti da considerare:

- Unire la migrazione ad altre attività.  La maggior parte dei clienti opta per una finestra di manutenzione dell'applicazione.  In questo caso, si potrebbe usare questo periodo di inattività per abilitare altre funzionalità di Azure Resource Manager come la crittografia e la migrazione a Managed Disks.
- Riesaminare i motivi tecnici e aziendali della scelta di Azure Resource Manager; abilitare i servizi aggiuntivi disponibili solo in Azure Resource Manager che si applicano all'ambiente.
- Aggiornare l'ambiente con servizi PaaS.

### <a name="patterns-of-success"></a>Modelli di successo

Decidere quali servizi si desidera abilitare ora in Azure Resource Manager.  Molti clienti ritengono efficace quanto segue per i propri ambienti Azure:

- [controllo degli accessi in base al ruolo](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Modelli di Azure Resource Manager per una distribuzione più semplice e controllata](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Tag](../../azure-resource-manager/resource-group-using-tags.md).
- [Controllo di attività](../../azure-resource-manager/resource-group-audit.md)
- [Criteri di Azure](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Errori da evitare

Tenere a mente il motivo per cui è stata eseguita la migrazione dalla distribuzione classica ad Azure Resource Manager.  Quali sono i motivi aziendali originali? Sono stati raggiunti i motivi aziendali?


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
