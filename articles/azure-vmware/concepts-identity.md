---
title: 'Concetti: identità e accesso'
description: Informazioni sui concetti di identità e accesso della soluzione VMware di Azure
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7127109801d92d2177f6edac3efcaf76ddf217e6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674642"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concetti di identità della soluzione VMware di Azure

Quando si distribuisce un cloud privato, viene eseguito il provisioning di un server vCenter e di gestione NSX-T. VCenter viene usato per gestire i carichi di lavoro delle macchine virtuali (VM). Si usa NSX-T Manager per estendere la rete privata definita dal software.

La gestione dell'accesso e delle identità usa i privilegi del gruppo CloudAdmin per vCenter e i diritti di amministratore limitati per NSX-T Manager. Garantisce che la piattaforma cloud privata venga aggiornata automaticamente con le funzionalità e le patch più recenti.  Per altre informazioni, vedere l' [articolo Concetti relativi agli aggiornamenti per il cloud privato][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>accesso e identità vCenter

I privilegi in vCenter vengono forniti tramite il gruppo CloudAdmin. Il gruppo può essere gestito localmente in vCenter o tramite l'integrazione di vCenter LDAP Single Sign-On con Azure Active Directory. Viene fornita la possibilità di abilitare l'integrazione dopo aver distribuito un cloud privato.

I privilegi CloudAdmin e CloudGlobalAdmin sono illustrati nella tabella seguente.

|  Set di privilegi           | CloudAdmin | CloudGlobalAdmin | Commento |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Avvisi                  | Un utente CloudAdmin dispone di tutti i privilegi di allarme per gli avvisi nel Compute-ResourcePool e nelle macchine virtuali.     |          --        |  -- |
|  Distribuzione automatica             |  --  |        --        |  Microsoft esegue la gestione degli host.  |
|  Certificati            |  --  |        --       |  Microsoft esegue la gestione dei certificati.  |
|  Raccolta contenuto         | Un utente CloudAdmin dispone dei privilegi necessari per creare e usare i file in una raccolta contenuto.    |         Abilitato con SSO.         |  Microsoft distribuirà i file nella raccolta contenuto agli host ESXi.  |
|  Data center              |  --  |        --          |  Microsoft esegue tutte le operazioni data center.  |
|  Archivio dati               | Datastore. AllocateSpace, datastore. browse, Datastore.Config, datastore. DeleteFile, datastore. filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Agent Manager ESX       |  --  |         --       |  Microsoft esegue tutte le operazioni.  |
|  Cartella                  |  Un utente CloudAdmin dispone di tutti i privilegi di cartella.     |  --  |  --  |
|  Global                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. hbr. HbrManagement      |        --          |  Microsoft esegue tutte le altre operazioni host.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Rete                 |  Network.Assign    |                  |  Microsoft esegue tutte le altre operazioni di rete.  |
|  Autorizzazioni             |  --  |        --       |  Microsoft esegue tutte le operazioni di autorizzazione.  |
|  Archiviazione basata su profili  |  --  |        --       |  Microsoft esegue tutte le operazioni di profilo.  |
|  Risorsa                |  Un utente CloudAdmin dispone di tutti i privilegi delle risorse.        |      --       | --   |
|  Attività pianificata.          |  Un utente CloudAdmin ha tutti i privilegi di ScheduleTask.   |   --   | -- |
|  Sessioni                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Microsoft esegue tutte le altre operazioni di sessione.  |
|  Viste di archiviazione           |  StorageViews. View   |        --          |  Microsoft esegue tutte le altre operazioni di visualizzazione dell'archiviazione (Configura servizio).  |
|  Attività                   |  --  |  --   |  Microsoft gestisce le estensioni per la gestione delle attività.  |
|  vApp                    |  Un utente CloudAdmin ha tutti i privilegi di vApp.  |  --  |  --  |
|  Macchina virtuale         |  Un utente CloudAdmin ha tutti i privilegi di VirtualMachine.  |  --  |  --  |
|  vService                |  Un utente CloudAdmin ha tutti i privilegi di vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Accesso e identità di gestione NSX-T

Usare l'account "Administrator" per accedere a NSX-T Manager. Dispone di privilegi completi e consente di creare e gestire i router T1, i commutatori logici e tutti i servizi. I privilegi consentono di accedere al router NSX-T T0. Una modifica al router T0 può comportare un peggioramento delle prestazioni della rete o l'accesso al cloud privato. Aprire una richiesta di supporto nella portale di Azure per richiedere eventuali modifiche al router NSX-T T0.
  
## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'ottenere informazioni sui concetti relativi all' [aggiornamento del cloud privato][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md