---
title: Laboratori di classi in Azure Lab Services - Domande frequenti Documenti Microsoft
description: Questo articolo fornisce le risposte alle domande frequenti sui laboratori di classe in Azure Lab Services.This article provides answers to frequently asked questions (FAQ) about classroom labs in Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443500"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratori di classi in Azure Lab Services: domande frequenti (FAQ)
Risposte ad alcune delle domande più comuni sui laboratori di classe in Azure Lab Services.Get answers to some of the most common questions about classroom labs in Azure Lab Services. 

## <a name="quotas"></a>Quote

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>La quota per utente o per settimana o per l'intera durata del lab? 
La quota impostata per un lab è per ogni studente per l'intera durata del lab. Inoltre, il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non viene conteggiato rispetto alla quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali.  Per ulteriori informazioni sulle quote, vedere [Impostare le quote per gli utenti.](how-to-configure-student-usage.md#set-quotas-for-users)

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se il professore attiva una macchina virtuale per studenti, ciò influisce sulla quota degli studenti? 
No. Non è così. Quando il professore attiva la macchina virtuale studente, non influisce sulla quota assegnata allo studente. 

## <a name="schedules"></a>Pianificazioni

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Tutte le macchine virtuali nel lab vengono avviate automaticamente quando viene impostata una pianificazione? 
No. Non tutte le macchine virtuali. Solo le macchine virtuali assegnate agli utenti in base a una pianificazione. Le macchine virtuali non assegnate a un utente non vengono avviate automaticamente. È per design. 

## <a name="lab-accounts"></a>Account lab

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Perché non è possibile creare un lab a causa dell'indisponibilità dell'intervallo di indirizzi? 
I laboratori di classe possono creare macchine virtuali lab all'interno di un intervallo di indirizzi IP specificato durante la creazione dell'account lab nel portale di Azure.Classroom labs can create lab VMs within an IP address range you specify when creating your lab account in the Azure portal. Quando viene fornito un intervallo di indirizzi, ogni lab creato dopo l'assegnata a 512 indirizzi IP per le macchine virtuali lab. L'intervallo di indirizzi per l'account lab deve essere sufficientemente grande da contenere tutti i lab che si intende creare con l'account lab. 

Ad esempio, se si dispone di un blocco di /19 - 10.0.0.0/19, questo intervallo di indirizzi supporta 8192 indirizzi IP e 16 lab(8192/512 - 16 labs). In questo caso, la creazione del lab non riesce durante la creazione del laboratorio.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quali intervalli di porte è necessario aprire nell'impostazione del firewall dell'organizzazione per connettersi alle macchine virtuali Lab tramite RDP/SSH?

I porti sono: 49152-65535. I laboratori di Classroom si esibisceno dietro un servizio di bilanciamento del carico. Ogni lab ha un singolo indirizzo IP pubblico e ogni macchina virtuale nel lab ha una porta univoca. 

È anche possibile visualizzare l'indirizzo IP privato di ogni macchina virtuale nella scheda **Pool di macchine virtuali** della home page per il lab nel portale di Azure.You can also see the private IP address of each virtual machine tab on the Virtual machine Pool tab of the home page for lab in the Azure portal. Se si ripubblica un lab, l'indirizzo IP pubblico del lab non verrà modificato, ma l'indirizzo IP privato e il numero di porta di ogni macchina virtuale nel lab possono cambiare. Per altre informazioni, vedere l'articolo: [Impostazioni del firewall per Azure Lab Services.](how-to-configure-firewall-settings.md)

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quale intervallo di indirizzi IP pubblici è necessario aprire nelle impostazioni del firewall dell'organizzazione per connettersi alle macchine virtuali lab tramite RDP/SSH?
Vedere [Intervalli IP di Azure e tag](https://www.microsoft.com/download/details.aspx?id=56519)di servizio - Public Cloud , che fornisce l'intervallo di indirizzi IP pubblici per i data center in Azure.See Azure IP Ranges and Service Tags - Public Cloud , which provides the public IP address range for data centers in Azure. È possibile aprire gli indirizzi IP per le aree in cui si trovano gli account lab.

## <a name="virtual-machine-images"></a>Immagini di macchine virtuali

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>In qualità di creatore di lab, perché non è possibile abilitare opzioni di immagine aggiuntive nell'elenco a discesa delle immagini della macchina virtuale durante la creazione di un nuovo lab?

Quando un amministratore aggiunge l'utente come creatore di lab a un account lab, viene concessa la possibilità di creare lab. Tuttavia, non si dispone delle autorizzazioni necessarie per modificare le impostazioni all'interno dell'account lab, incluso l'elenco delle immagini delle macchine virtuali abilitate. Per abilitare immagini aggiuntive, contattare l'amministratore dell'account lab per eseguire questa operazione automaticamente oppure chiedere all'amministratore di aggiungere l'utente come ruolo Collaboratore all'account lab. Il ruolo Collaboratore concederà le autorizzazioni per modificare l'elenco immagini della macchina virtuale nell'account lab.

## <a name="users"></a>Utenti

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quanti utenti possono essere in un laboratorio della classe?
Puoi aggiungere fino a 400 utenti a un laboratorio della classe. 

## <a name="blog-post"></a>Post di blog
Sottoscrivere il blog di [Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Notifiche di aggiornamento
Sottoscrivere gli aggiornamenti di [Lab Services](https://azure.microsoft.com/updates/?product=lab-services) per essere sempre informati sulle nuove funzionalità di Lab Services.

## <a name="general"></a>Generale
### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la tua domanda non è elencata qui, comunicacelo, in modo che possiamo aiutarti a trovare una risposta.

- Pubblicare una domanda alla fine di questo articolo di domande frequenti. 
- Per raggiungere un pubblico più ampio, pubblicare una domanda nel [forum Azure Lab Services - Overflow dello stack](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- Per le richieste di funzionalità, inviare le richieste e le idee a [Azure Lab Services - User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

