---
title: Criteri di manutenzione Azure Stack | Microsoft Docs
description: Informazioni su Azure Stack di manutenzione dei criteri e come mantenere un sistema integrato in uno stato supportato.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: 523be365e8b66c157546470a70ddec73d3e1e5d1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630682"
---
# <a name="azure-stack-servicing-policy"></a>Criteri di manutenzione Azure Stack
Questo articolo descrive i criteri di manutenzione per i sistemi integrati di Azure Stack e le operazioni da eseguire per mantenere il sistema in uno stato supportato. 

## <a name="update-package-types"></a>Tipi di pacchetto di aggiornamento

Esistono due tipi di pacchetti di aggiornamento per i sistemi integrati: 

- **Gli aggiornamenti software Microsoft**. Microsoft è responsabile per il ciclo di vita di manutenzione end-to-end per i pacchetti di aggiornamento software Microsoft. Questi pacchetti possono includere i recenti aggiornamenti di protezione di Windows Server, gli aggiornamenti non relativi alla sicurezza e gli aggiornamenti delle funzionalità di Azure Stack. È possibile scaricare i pacchetti di aggiornamento theses direttamente da Microsoft.

- **Gli aggiornamenti forniti dal produttore hardware OEM**. I partner hardware di Azure Stack sono responsabili di end-to-end per la manutenzione del ciclo di vita (incluse indicazioni) per il firmware correlato all'hardware e i pacchetti di aggiornamento del driver. Inoltre, i partner hardware di Azure Stack proprietari e mantenere linee guida per tutti i software e hardware nell'host del ciclo di vita dell'hardware. Il fornitore dell'hardware OEM ospita tali pacchetti nel proprio sito di download di aggiornamento.


## <a name="update-package-release-cadence"></a>Frequenza di rilascio del pacchetto di aggiornamento
Microsoft prevede di rilasciare pacchetti di aggiornamento software una cadenza mensile. Tuttavia, è possibile avere nessuna o più versioni di aggiornamento in un mese. I fornitori di hardware OEM rilasciare gli aggiornamenti in base alle esigenze. 

Documentazione su come pianificare e gestire gli aggiornamenti e come determinare la versione corrente in [Gestisci Aggiorna Panoramica](azure-stack-updates.md). Per informazioni su uno specifico aggiornamento, incluso come scaricarlo, vedere le note sulla versione per cui aggiornare: 
- [Aggiornamento di Azure Stack 1808](azure-stack-update-1808.md)
- [Aggiornamento di Azure Stack 1807](azure-stack-update-1807.md)
- [Aggiornamento di Azure Stack 1805](azure-stack-update-1805.md)


## <a name="hotfixes"></a>Hotfix
In alcuni casi, Microsoft fornisce le correzioni rapide per Azure Stack tale indirizzo di un problema specifico che è spesso preventive e scadenza.  Ciascun aggiornamento rapido viene rilasciato con un articolo della Microsoft Knowledge Base corrispondente che descrive in dettaglio il problema, cause e risoluzioni. 

Gli aggiornamenti rapidi vengono scaricati e installati come i pacchetti di aggiornamento completo regolare per Azure Stack. Tuttavia, a differenza di un aggiornamento completo, gli aggiornamenti rapidi possono installare in pochi minuti. Si consiglia di che operatori di Azure Stack impostare le finestre di manutenzione quando si installa gli aggiornamenti rapidi. Gli aggiornamenti rapidi aggiornare la versione di cloud di Azure Stack in modo che è possibile stabilire facilmente se è stato applicato l'aggiornamento rapido. Viene fornito un hotfix separato per ogni versione di Azure Stack che è ancora in modalità di supporto. Ogni correzione per un'iterazione specifica è cumulativo e comprende i precedenti aggiornamenti per la stessa versione. Altre informazioni sull'applicabilità di un hotfix specifico in un correzioni corrispondente della Knowledge Base articolo.  


## <a name="keep-your-system-under-support"></a>Mantenere il sistema di supporto
Per continuare a ricevere supporto, è necessario mantenere la distribuzione di Azure Stack corrente. I criteri di rinvio degli aggiornamenti sono: per la distribuzione di Azure Stack rimanere in modalità di supporto, è necessario eseguire la versione rilasciata più recente di aggiornamento o eseguire una delle due versioni di aggiornamento precedente. Gli aggiornamenti rapidi non vengono considerati le versioni di aggiornamento principale. Se cloud di Azure Stack è protetta dal *più di due aggiornamenti*, viene considerato non conforme e si deve aggiornare per almeno la versione minima supportata per ricevere supporto. 

Ad esempio, se la versione di aggiornamento più recente disponibile è 1805 e i due pacchetti di aggiornamento precedente sono stati versione 1804 e 1803, 1803 sia 1804 rimangono in modalità di supporto. Tuttavia, 1802 è più supportata. Il criterio contenga true quando non è disponibile alcuna versione per un mese o due. Ad esempio, se la versione corrente è 1805 e si è verificato alcun versione 1804, precedente due pacchetti di aggiornamento 1802 e 1803 rimangono in modalità di supporto.

Pacchetti di aggiornamento software Microsoft sono non cumulativo e richiedono il pacchetto di aggiornamento precedente come prerequisito. Se si decide di rinviare gli aggiornamenti di uno o più, prendere in considerazione la fase di esecuzione complessivo se si vuole ottenere la versione più recente. 

## <a name="get-support"></a>Supporto
Azure Stack segue lo stesso processo di supporto di Azure. I clienti aziendali possono seguire la procedura descritta nel [come creare una richiesta di supporto tecnico di Azure](/azure/azure-supportability/how-to-create-azure-support-request). Se sei un cliente di un Provider del servizio Cloud (CSP), contattare il CSP per il supporto.  Per altre informazioni, vedere le [domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Passaggi successivi

- [Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)


