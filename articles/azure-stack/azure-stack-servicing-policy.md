---
title: Azure Stack di manutenzione dei criteri | Documenti Microsoft
description: Informazioni sullo Stack di Azure per la manutenzione dei criteri e come mantenere un sistema integrato in uno stato supportato.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 77cc2f80588a104880e8149daccc6debd1ec43bc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack di manutenzione dei criteri
Questo articolo descrive i criteri di manutenzione per i sistemi Azure Stack integrato e le operazioni da eseguire per mantenere il sistema in uno stato supportato. 

## <a name="update-package-types"></a>Tipi di pacchetto di aggiornamento

Esistono due tipi di pacchetti di aggiornamento per sistemi integrati: 

- **Gli aggiornamenti software Microsoft**. Microsoft è responsabile per il ciclo di vita di manutenzione end-to-end per i pacchetti di aggiornamento del software Microsoft. Questi pacchetti possono includere gli ultimi aggiornamenti di sicurezza di Windows Server, non correlato alla sicurezza aggiornamenti e gli aggiornamenti di funzionalità dello Stack di Azure. È possibile scaricare i pacchetti di aggiornamento theses direttamente da Microsoft.

- **Aggiornamenti fornito dal fornitore dell'hardware OEM**. I partner hardware di Azure Stack sono responsabili per l'end-to-end per la manutenzione del ciclo di vita (incluse indicazioni) per il firmware correlati all'hardware e i pacchetti di aggiornamento del driver. Inoltre, i partner hardware di Azure Stack proprietari e gestire linee guida per tutti i software e hardware nell'host del ciclo di vita dell'hardware. Il fornitore dell'hardware OEM ospita tali pacchetti nel proprio sito di download di aggiornamento.


## <a name="update-package-release-cadence"></a>Rilasci del pacchetto di aggiornamento
Microsoft prevede di rilasciare i pacchetti di aggiornamento software a un ritmo mensile. Tuttavia, è possibile avere nessuna o più versioni di aggiornamento in un mese. I fornitori di hardware OEM rilasciare gli aggiornamenti in base alle esigenze. 

Documentazione su come pianificare e gestire gli aggiornamenti e come determinare la versione corrente in [Gestisci Aggiorna Panoramica](azure-stack-updates.md). Per informazioni su uno specifico aggiornamento, incluso come scaricarlo, vedere le note sulla versione per l'aggiornamento: 
- [Aggiornamento dello Stack 1803 Azure](azure-stack-update-1803.md)
- [Aggiornamento dello Stack 1802 Azure](azure-stack-update-1802.md)
- [Aggiornamento dello Stack 1712 Azure](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Hotfix
In alcuni casi, Microsoft fornisce gli aggiornamenti rapidi per Azure Stack tale indirizzo di un problema specifico che viene spesso preventive o tempi sono importanti.  Ciascun aggiornamento rapido viene rilasciato con un articolo della Microsoft Knowledge Base corrispondente che descrive in dettaglio il problema, causa e risoluzione. 

Gli hotfix vengono scaricati e installati esattamente come i pacchetti di aggiornamento completo regolare per lo Stack di Azure. Tuttavia, a differenza di un aggiornamento completo, hotfix è possono installare in minuti. È consigliabile che operatori dello Stack di Azure impostare le finestre di manutenzione quando si installa gli aggiornamenti rapidi. Gli hotfix aggiornare la versione del cloud di Azure Stack in modo è possibile stabilire facilmente se è stato applicato l'aggiornamento rapido. Viene fornito un hotfix separato per ogni versione di Azure lo Stack risulta essere ancora nel supporto. Ogni correzione per un'iterazione specifica è cumulativo e include gli aggiornamenti precedenti per la stessa versione. Altre informazioni sull'applicabilità di un hotfix specifico in un correzioni corrispondenti della Knowledge Base dell'articolo.  


## <a name="keep-your-system-under-support"></a>Mantenere il sistema di supporto
Per continuare a ricevere supporto, è necessario mantenere la distribuzione di Azure Stack corrente. I criteri di rinvio degli aggiornamenti sono: per la distribuzione di Azure Stack deve rimanere nel supporto, deve eseguire la versione rilasciata di recente aggiornamento oppure eseguire uno dei due versioni di aggiornamento precedente. Gli aggiornamenti rapidi non vengono considerati le versioni di aggiornamento principale. Se il cloud di Azure Stack si trova dietro dal *più di due aggiornamenti*, è considerata non conforme e si deve aggiornare almeno la versione minima supportata per ricevere assistenza. 

Ad esempio, se la versione dell'aggiornamento più recente disponibile è 1805 e due pacchetti di aggiornamento precedente sono stati versioni 1804 e 1803, 1803 sia 1804 rimangono in modalità di supporto. Tuttavia, 1802 è più supportata. I criteri vale quando non si verifica alcuna versione per un mese o due. Ad esempio, se la versione corrente è 1805 e si è verificato alcun versione 1804, precedente due pacchetti di aggiornamento di 1803 e 1802 rimangono in modalità di supporto.

I pacchetti di aggiornamento software Microsoft sono non cumulativi e richiedono che il pacchetto di aggiornamento precedente come prerequisito. Se si decide di rinviare gli aggiornamenti di uno o più, prendere in considerazione la fase di esecuzione complessivo se si vuole ottenere la versione più recente. 

## <a name="get-support"></a>Supporto
Stack di Azure segue lo stesso processo di supporto come Azure. I clienti aziendali possono seguire il processo descritto in [come creare una richiesta di supporto tecnico di Azure](/azure/azure-supportability/how-to-create-azure-support-request). Se si è un cliente di un Provider del servizio Cloud (CSP), contattare il CSP per il supporto.  Per altre informazioni, vedere le [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)


