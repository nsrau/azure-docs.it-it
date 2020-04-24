---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383794"
---
## <a name="what-is-the-time-required-for-migration"></a>Qual è il tempo necessario per la migrazione?

La pianificazione e l'esecuzione della migrazione variano notevolmente a seconda della complessità dell'architettura e possono richiedere un paio di mesi.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Qual è la definizione di un nuovo cliente in macchine virtuali IaaS (classico)?

I clienti che non hanno macchine virtuali IaaS (classico) nelle sottoscrizioni del mese di febbraio 2020 (un mese prima dell'avvio della deprecazione) vengono considerati nuovi clienti. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Qual è la definizione di un cliente esistente in macchine virtuali IaaS (classico)?

Il cliente che aveva attivato o arrestato, ma ha allocato le VM IaaS (classico) nelle proprie sottoscrizioni nel mese di febbraio 2020, viene considerato un cliente esistente. Solo questi clienti ricevono fino al 1 ° marzo 2023 per eseguire la migrazione delle macchine virtuali da Azure Service Manager Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Perché viene visualizzato un errore che indica "NewClassicVMCreationNotAllowedForSubscription"?

Come parte del processo di ritiro, la macchina virtuale IaaS (classica) non è più disponibile per i nuovi clienti. Sono stati identificati come nuovi clienti e pertanto l'operazione non è stata autorizzata. Si consiglia vivamente di usare [macchine virtuali di Azure con ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Se non è possibile usare le macchine virtuali di Azure con ARM, contattare il supporto per l'elenco elementi consentiti della sottoscrizione.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Il piano di migrazione influisce sui servizi o sulle applicazioni esistenti in esecuzione nelle macchine virtuali di Azure? 

Non fino al 1 ° marzo 2023 per le macchine virtuali IaaS (classico). Le macchine virtuali IaaS (classiche) sono servizi completamente supportati in disponibilità generale. È possibile continuare a usare queste risorse per sfruttare meglio la presenza su Microsoft Azure. Il 1 ° marzo 2023 queste VM verranno ritirate completamente e tutte le macchine virtuali attive o allocate verranno arrestate & deallocate. Non vi sarà alcun effetto sulle altre risorse classiche come servizi cloud (versione classica), account di archiviazione (classico) e così via.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Che cosa accade alle macchine virtuali se non è prevista la migrazione a breve? 

Il 1 ° marzo 2023, le VM IaaS (classico) verranno ritirate completamente e tutte le macchine virtuali attive o allocate verranno arrestate & deallocate. Per evitare un impatto aziendale, si consiglia vivamente di iniziare a pianificare la migrazione oggi e completarla prima del 1 ° marzo 2023. Non sono state deprecate le API classiche, i servizi cloud e il modello di risorse esistenti. L'obiettivo è quello di semplificare la migrazione, tenendo conto delle funzionalità avanzate disponibili nel modello di distribuzione di Resource Manager. Si consiglia di iniziare a pianificare la migrazione di queste risorse in Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Qual è l'impatto di questo piano di migrazione per gli strumenti esistenti? 

L'aggiornamento degli strumenti al modello di distribuzione di Resource Manager è una delle modifiche più importanti da tenere in considerazione per i piani di migrazione.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Quale durata avrà il tempo di inattività del piano di gestione? 

Dipende dal numero di risorse sottoposte a migrazione. Per le distribuzioni di dimensioni minori, ovvero con poche decine di VM, l'intera migrazione dovrebbe richiedere meno di un'ora. Per le distribuzioni su larga scala (centinaia di VM), la migrazione può richiedere alcune ore.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>È possibile eseguire il rollback dopo il commit delle risorse sottoposte a migrazione in Resource Manager? 

È possibile interrompere la migrazione finché lo stato delle risorse è Prepared. Il rollback non è supportato dopo il completamento della migrazione delle risorse mediante l'operazione di commit.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>È possibile eseguire il rollback della migrazione in caso di esito negativo dell'operazione di commit? 

Non è possibile interrompere la migrazione in caso di esito negativo dell'operazione di commit. Tutte le operazioni di migrazione, inclusa l'operazione Commit, sono idempotenti. È quindi consigliabile provare a ripetere l'operazione dopo breve tempo. Se si riscontra ancora un errore, creare un ticket di supporto.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>In caso si debba usare le risorse IaaS in Resource Manager, è necessario acquistare un altro circuito ExpressRoute? 

No. È stato recentemente abilitato lo [spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](../articles/expressroute/expressroute-move.md). Se si dispone già di un circuito ExpressRoute, non è necessario acquistare un nuovo.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Che cosa succede se sono stati configurati criteri di controllo degli accessi in base al ruolo per le risorse IaaS classiche? 

Durante la migrazione le risorse si trasformano dal modello di distribuzione classica ad Azure Resource Manager. È quindi consigliabile pianificare gli aggiornamenti dei criteri RBAC che devono essere apportati dopo la migrazione.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>È stato eseguito il backup di macchine virtuali della versione classica in un insieme di credenziali. È possibile eseguire la migrazione delle macchine virtuali dalla modalità classica alla modalità di Resource Manager e proteggerli in un insieme di credenziali dei Servizi di ripristino?

Quando si sposta una macchina virtuale dalla modalità classica alla modalità Resource Manager, i backup eseguiti prima della migrazione non verranno spostati nella macchina virtuale di Resource Manager appena migrata. Tuttavia, se si vogliono conservare i backup delle macchine virtuali classiche, seguire questi passaggi prima della migrazione. 

1. Nell'insieme di credenziali di Servizi di ripristino passare alla scheda **Elementi protetti** e selezionare la macchina virtuale. 
2. Fare clic su Arresta protezione. Lasciare *deselezionata* l'opzione **Elimina i dati di backup associati**.

> [!NOTE]
> I costi dell'istanza di backup verranno addebitati finché si conserveranno i dati. Le copie di backup saranno eliminate in base all'intervallo di conservazione. Tuttavia, l'ultima copia di backup viene sempre mantenuta finché non si eliminano in modo esplicito i dati di backup. È consigliabile controllare l'intervallo di conservazione della macchina virtuale e attivare "Eliminare i dati di backup" sull'elemento protetto nell'insieme di credenziali al termine del periodo di conservazione. 
>
>

Per eseguire la migrazione della macchina virtuale alla modalità Resource Manager: 

1. Eliminare l'estensione di backup/snapshot dalla VM.
2. Eseguire la migrazione della macchina virtuale dalla modalità classica alla modalità Resource Manager. Assicurarsi di eseguire la migrazione alla modalità Resource Manager anche delle informazioni di rete e archiviazione corrispondenti alla macchina virtuale.

Inoltre, se si vuole eseguire il backup della macchina virtuale migrata, passare al pannello di gestione della macchina virtuale per [abilitare il backup](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>È possibile convalidare la sottoscrizione o le risorse per verificare se sono idonee per la migrazione? 

Sì. Nell'opzione di migrazione supportata dalla piattaforma il primo passaggio per la preparazione della migrazione consiste nel verificare se le risorse sono idonee alla migrazione. In caso di esito negativo dell'operazione di convalida, si ricevono messaggi per tutti i motivi che impediscono il completamento della migrazione.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Che cosa succede se si verifica un errore di quota durante la preparazione delle risorse IaaS per la migrazione? 

Si consiglia di interrompere la migrazione e quindi inviare una richiesta di supporto per aumentare le quote nell'area in cui si sta eseguendo la migrazione delle macchine virtuali. Dopo l'approvazione della richiesta relativa alla quota, è possibile avviare di nuovo l'esecuzione della procedura di migrazione.

## <a name="how-do-i-report-an-issue"></a>Come si segnala un problema? 

Ogni utente è invitato a pubblicare problemi e domande sulla migrazione nel [forum sulle VM](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)con la parola chiave ClassicIaaSMigration. Si consiglia di pubblicare tutte le eventuali domande in questo forum. Se si dispone di un contratto di supporto, è possibile anche registrare un ticket di supporto.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Che cosa succede se non si vogliono usare i nomi scelti dalla piattaforma per le risorse durante la migrazione? 

Tutti i nomi di risorse specificati in modo esplicito nel modello di distribuzione classica vengono mantenuti durante la migrazione. In alcuni casi vengono create nuove risorse. Ad esempio, viene creata un'interfaccia di rete per ogni VM. Non è attualmente supportata la possibilità di controllare i nomi delle nuove risorse create durante la migrazione. Nel [forum di commenti e suggerimenti su Azure](https://feedback.azure.com)è possibile registrare i voti per questa funzionalità.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>È possibile eseguire la migrazione dei circuiti ExpressRoute usati in diverse sottoscrizioni con i collegamenti di autorizzazione? 

Non è possibile eseguire automaticamente la migrazione dei circuiti ExpressRoute che usano collegamenti di autorizzazione tra sottoscrizioni senza tempi di inattività. Sono disponibili indicazioni su come eseguire la questa migrazione seguendo una procedura manuale. Per la procedura e altre informazioni, vedere [Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Ho ricevuto il messaggio *"la macchina virtuale riporta lo stato complessivo dell'agente come non pronto. Di conseguenza, non è possibile eseguire la migrazione della macchina virtuale. Verificare che l'agente di macchine virtuali stia segnalando lo stato generale dell'agente come pronto "* o *" la macchina virtuale contiene l'estensione il cui stato non viene segnalato dalla macchina virtuale. Di conseguenza, non è possibile eseguire la migrazione di questa VM. "*

Questo messaggio viene ricevuto quando la VM non dispone di connettività in uscita a Internet. L'agente VM usa la connettività in uscita per raggiungere l'account di archiviazione di Azure in modo da aggiornare lo stato dell'agente ogni 5 minuti.
