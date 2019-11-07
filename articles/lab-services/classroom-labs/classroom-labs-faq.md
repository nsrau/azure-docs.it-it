---
title: Laboratori in aula in Azure Lab Services-domande frequenti | Microsoft Docs
description: Trovare le risposte alle domande più comuni sui Lab in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 91c5c1480669829bad7f8d89ce360bb806d6c997
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646861"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Lab in aula in Azure Lab Services-domande frequenti
Ottenere le risposte ad alcune delle domande più comuni sui Lab in Azure Lab Services. 

## <a name="quotas"></a>Quote

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>La quota per utente o per settimana o per l'intera durata del Lab? 
La quota impostata per un Lab riguarda ogni studente per tutta la durata del Lab. Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non viene conteggiato rispetto alla quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali.  Per altre informazioni sulle quote, vedere [impostare le quote per gli utenti](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Pianificazioni

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Tutte le macchine virtuali nel Lab vengono avviate automaticamente quando viene impostata una pianificazione? 
No. Non tutte le macchine virtuali. Solo le macchine virtuali assegnate agli utenti in base a una pianificazione. Le macchine virtuali non assegnate a un utente non vengono avviate automaticamente. Si tratta di un progetto. 

## <a name="lab-accounts"></a>Account Lab

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Perché non è possibile creare un Lab a causa della mancata disponibilità dell'intervallo di indirizzi? 
I Lab in aula possono creare VM Lab all'interno di un intervallo di indirizzi IP specificato durante la creazione dell'account Lab nel portale di Azure. Quando viene fornito un intervallo di indirizzi, ogni Lab creato dopo aver assegnato 512 indirizzi IP per le macchine virtuali del Lab. L'intervallo di indirizzi per l'account lab deve essere sufficientemente grande da contenere tutti i Lab che si intende creare con l'account Lab. 

Se, ad esempio, si dispone di un blocco di/19-10.0.0.0/19, questo intervallo di indirizzi include 8192 indirizzi IP e 16 Lab (8192/512 = 16 Labs). In questo caso, la creazione del Lab non riesce dopo la creazione del diciassettesimo Lab.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quali intervalli di porte è necessario aprire nell'impostazione del firewall dell'organizzazione per connettersi alle macchine virtuali del Lab tramite RDP/SSH?

Le porte sono: 49152 – 65535. I Lab della classe si trovano dietro un servizio di bilanciamento del carico, quindi tutte le macchine virtuali in un Lab hanno un unico indirizzo IP e ogni macchina virtuale nel Lab ha una porta univoca. I numeri di porta e l'indirizzo IP pubblico possono cambiare ogni volta che il Lab viene ripubblicato.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quale intervallo di indirizzi IP pubblici è necessario aprire sulle impostazioni del firewall dell'organizzazione per connettersi alle macchine virtuali del Lab tramite RDP/SSH?
Vedere [Azure IP Ranges and Service Tags (cloud pubblico)](https://www.microsoft.com/download/details.aspx?id=56519), che fornisce l'intervallo di indirizzi IP pubblici per i Data Center in Azure. È possibile aprire gli indirizzi IP per le aree in cui si trovano gli account Lab.

## <a name="blog-post"></a>Post di Blog
Sottoscrivere il [blog Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notifiche degli aggiornamenti
Sottoscrivere [gli aggiornamenti di Lab Services](https://azure.microsoft.com/updates/?product=lab-services) per rimanere sempre aggiornati sulle nuove funzionalità di Lab Services.

## <a name="general"></a>Generale
### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la domanda non è elencata, è possibile inviarla a Microsoft, per consentire di trovare una risposta.

- Pubblicare una domanda alla fine di questo articolo di domande frequenti. 
- Per raggiungere un pubblico più ampio, pubblicare una domanda nel [forum Azure Lab Services-stack overflow](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Per le richieste di funzionalità, inviare le richieste e le idee a [Azure Lab Services-User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

