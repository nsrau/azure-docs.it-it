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
ms.openlocfilehash: fa97935c10b2a5df70c9babf031a889a9c1e9f34
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837931"
---
## <a name="what-is-the-time-required-for-migration"></a>Qual è il tempo necessario per una migrazione?

La pianificazione e l'esecuzione di una migrazione dipendono notevolmente dalla complessità dell'architettura e possono richiedere circa due mesi.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Qual è la definizione di nuovo cliente per macchine virtuali IaaS (classiche)?

I clienti che non avevano macchine virtuali IaaS (classiche) nelle proprie sottoscrizioni nel mese di febbraio 2020 (un mese prima dell'avvio della deprecazione) vengono considerati nuovi clienti. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Qual è la definizione di cliente esistente per macchine virtuali IaaS (classiche)?

I clienti che avevano macchine virtuali IaaS (classiche) attive o arrestate ma allocate nelle proprie sottoscrizioni nel mese di febbraio 2020 vengono considerati clienti esistenti. Solo questi clienti hanno tempo fino al 1° marzo 2023 per eseguire la migrazione delle macchine virtuali da Azure Service Manager ad Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Perché viene visualizzato l'errore "NewClassicVMCreationNotAllowedForSubscription"?

Nell'ambito del processo di ritiro, le macchine virtuali IaaS (classiche) non sono più disponibili per i nuovi clienti. L'utente è stato identificato come nuovo cliente e quindi l'operazione non è stata autorizzata. È consigliabile usare [macchine virtuali di Azure con ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Se non è possibile usare macchine virtuali di Azure con ARM, contattare il supporto per l'inserimento della sottoscrizione nell'elenco elementi consentiti.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Il piano di migrazione influisce sui servizi o sulle applicazioni esistenti in esecuzione nelle macchine virtuali di Azure? 

Non fino al 1° marzo 2023 per le macchine virtuali IaaS (classiche). Le macchine virtuali IaaS (classiche) sono servizi completamente supportati con disponibilità generale. È possibile continuare a usare queste risorse per sfruttare meglio la presenza su Microsoft Azure. Il 1° marzo 2023 queste macchine virtuali verranno ritirate completamente e tutte le macchine virtuali attive o allocate verranno arrestate e deallocate. Le altre risorse classiche come Servizi cloud (versione classica), Account di archiviazione (versione classica) e così via non verranno interessate.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Che cosa accade alle macchine virtuali se non è prevista la migrazione a breve? 

Il 1° marzo 2023 le macchine virtuali IaaS (classiche) verranno ritirate completamente e tutte le macchine virtuali attive o allocate verranno arrestate e deallocate. Per evitare l'impatto sull'azienda, è consigliabile iniziare a pianificare la migrazione da subito e completarla prima del 1° marzo 2023. Le API, Servizi cloud e il modello di risorse classici non verranno deprecati. L'obiettivo è quello di semplificare la migrazione, tenendo conto delle funzionalità avanzate disponibili nel modello di distribuzione di Resource Manager. È consigliabile iniziare a pianificare la migrazione di queste risorse in Azure Resource Manager. 

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

Ogni utente è invitato a pubblicare problemi e domande sulla migrazione nella [pagina delle domande di Domande e risposte Microsoft per le VM](https://docs.microsoft.com/answers/topics/azure-virtual-machines.html) con la parola chiave ClassicIaaSMigration. Si consiglia di pubblicare tutte le eventuali domande in questo forum. Se si dispone di un contratto di supporto, è possibile anche registrare un ticket di supporto.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Che cosa succede se non si vogliono usare i nomi scelti dalla piattaforma per le risorse durante la migrazione? 

Tutti i nomi di risorse specificati in modo esplicito nel modello di distribuzione classica vengono mantenuti durante la migrazione. In alcuni casi vengono create nuove risorse. Ad esempio, viene creata un'interfaccia di rete per ogni VM. Non è attualmente supportata la possibilità di controllare i nomi delle nuove risorse create durante la migrazione. Nel [forum di commenti e suggerimenti su Azure](https://feedback.azure.com)è possibile registrare i voti per questa funzionalità.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>È possibile eseguire la migrazione dei circuiti ExpressRoute usati in diverse sottoscrizioni con i collegamenti di autorizzazione? 

Non è possibile eseguire automaticamente la migrazione dei circuiti ExpressRoute che usano collegamenti di autorizzazione tra sottoscrizioni senza tempi di inattività. Sono disponibili indicazioni su come eseguire la questa migrazione seguendo una procedura manuale. Per la procedura e altre informazioni, vedere [Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Il messaggio indica che la *"VM segnala uno stato complessivo dell'agente come Non pronto. Di conseguenza, non è possibile eseguire la migrazione della VM. Il messaggio invita a controllare che l'agente VM segnali lo stato Pronto*. In un altro messaggio si legge che *la VM contiene un'estensione di cui non segnala lo stato. Per questo motivo, non è possibile eseguire la migrazione della macchina virtuale".*

Questo messaggio viene ricevuto quando la VM non dispone di connettività in uscita a Internet. L'agente VM usa la connettività in uscita per raggiungere l'account di archiviazione di Azure in modo da aggiornare lo stato dell'agente ogni 5 minuti.
