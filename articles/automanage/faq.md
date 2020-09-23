---
title: Domande frequenti su gestione automatici di Azure per le macchine virtuali
description: Risposte alle domande frequenti su gestione automatici di Azure per le macchine virtuali.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: ed97f7861f5dd959fd41ac22b4e497f492dbc3a3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940170"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Domande frequenti su Azure automanage per le macchine virtuali

Questo articolo fornisce le risposte ad alcune delle domande più comuni su [gestione automatici di Azure per le macchine virtuali](automanage-virtual-machines.md).

Se il problema riguardante Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto, nella [pagina supporto tecnico di Azure](https://azure.microsoft.com/support/options/)selezionare **Ottieni supporto**.


## <a name="azure-automanage-for-virtual-machines"></a>Gestione automatici di Azure per le macchine virtuali

**Quali sono tutti i prerequisiti necessari per abilitare la gestione di Azure automanage?**

Di seguito sono riportati i prerequisiti per l'abilitazione di Azure automanage:
- Solo macchine virtuali Windows Server
- Le macchine virtuali devono essere in esecuzione
- Le macchine virtuali devono trovarsi in un'area supportata
- L'utente deve disporre delle autorizzazioni corrette
- Solo macchine virtuali con set di scalabilità
- Le macchine virtuali non devono essere collegate a un'area di lavoro di log Analytics in una sottoscrizione diversa

**Quale autorizzazione RBAC è necessaria per abilitare la gestione della funzionalità automanage?**

Gli utenti devono avere il ruolo di proprietario. In alternativa, gli utenti possono avere il ruolo Collaboratore insieme al ruolo amministratore accesso utenti per applicare la gestione.


**Quali aree sono supportate?**

Sono supportate le macchine virtuali nelle aree seguenti: Europa occidentale, Stati Uniti orientali, Stati Uniti occidentali 2, Canada centrale, Stati Uniti centro-occidentali.


**Quali sono le funzionalità gestite da gestione automatica di Azure?**

Consente di registrare, configurare e monitorare in tutto il ciclo di vita della VM i servizi elencati [qui](virtual-machines-best-practices.md).


**È possibile personalizzare le configurazioni in Azure automanage?**

I clienti possono personalizzare le impostazioni per servizi specifici, ad esempio la conservazione di backup di Azure, tramite le preferenze di configurazione. Per l'elenco completo delle impostazioni che è possibile modificare, vedere la documentazione [qui](virtual-machines-best-practices.md).


**Azure automanage funziona con macchine virtuali Linux e Windows?**

Attualmente, automanage supporta le macchine virtuali di Windows Server Azure.


**È possibile applicare la gestione in modo selettivo solo a un set di macchine virtuali?**

La funzionalità automanage può essere abilitata con l'opzione semplicità clic e punto nelle VM nuove ed esistenti selezionate. È anche possibile disabilitare la gestione in qualsiasi momento.


**Azure automanage supporta le macchine virtuali in un set di scalabilità di macchine virtuali?**

No, Azure automanage attualmente non supporta le macchine virtuali in un set di scalabilità di macchine virtuali.


**Quanto costa Azure automanage?**

Gestione automatica di Azure è disponibile senza costi aggiuntivi nell'anteprima pubblica. Le risorse di Azure associate, ad esempio backup di Azure, comporteranno costi.


**È possibile applicare la gestione tramite criteri di Azure?**

Sì, abbiamo un criterio predefinito che applica automaticamente la gestione automatica a tutte le macchine virtuali all'interno dell'ambito definito. Si specificherà anche il profilo di configurazione (DevTest o Production) insieme all'account automanage. Per altre informazioni sull'abilitazione della gestione tramite criteri di Azure, vedere [qui](virtual-machines-policy-enable.md).


**Che cos'è un account di automanage?**

L'account di gestione automatica è un file MSI (identità del servizio gestita) che fornisce il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche.


**È possibile modificare il profilo di configurazione della macchina virtuale?**

A questo punto, sarà necessario disabilitare la gestione automatica per tale macchina virtuale e quindi abilitare di nuovo la gestione automatica con il profilo di configurazione e le preferenze desiderate.


**Se la macchina virtuale è già configurata per un servizio, ad esempio Gestione aggiornamenti, gestirà automaticamente la riconfigurazione?**
No, la funzione di gestione automaticamente non la riconfigura. Si inizierà a monitorare le risorse associate a tale servizio per la deriva.


**Perché la macchina virtuale ha uno stato di errore nel portale di automanage?**

Se lo stato viene visualizzato come *non riuscito*, è possibile risolvere i problemi di distribuzione tramite il gruppo di risorse in cui si trova la macchina virtuale. Passare a **gruppi di risorse**, selezionare il gruppo di risorse, fare clic su **distribuzioni** e vedere lo stato *non riuscito* insieme ai dettagli dell'errore.

**Come è possibile ottenere supporto per la risoluzione dei problemi per la gestione di automanage?**

È possibile archiviare un [ticket del caso di supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Per l'opzione **Service** , cercare e selezionare *automanage* nella sezione *monitoraggio e gestione* .


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la gestione automatici per le macchine virtuali nella portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)