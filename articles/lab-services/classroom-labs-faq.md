---
title: Lab per le classi in Azure Lab Services - Domande frequenti | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti sui lab per le classi in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ca35d70bc1106e46df4e3c68889b03679fd54b86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443299"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Lab per le classi in Azure Lab Services - Domande frequenti
Trovare le risposte ad alcune domande frequenti sui lab per le classi in Azure Lab Services. 

## <a name="quotas"></a>Quote

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>La quota vale per utente, per settimana o per l'intera durata del lab? 
La quota impostata per un lab riguarda ogni studente per tutta la durata del lab. Inoltre, il [tempo di esecuzione pianificato delle VM](how-to-create-schedules.md) non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali.  Per altre informazioni sulle quote, vedere [Impostare quote per gli utenti](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Se l'educatore attiva la VM di uno studente, questa azione ha effetto sulla sua quota? 
No. Non ha effetto. L'attivazione della VM di uno studente eseguita dall'educatore non ha effetto sulla quota assegnata. 

## <a name="schedules"></a>Pianificazioni

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Tutte le VM del lab vengono avviate automaticamente quando viene impostata una pianificazione? 
No. Non, non tutte le VM, ma solo quelle assegnate agli utenti in base a una pianificazione. Le VM non assegnate a un utente non vengono avviate automaticamente. Questa scelta è da progettazione. 

## <a name="lab-accounts"></a>Account lab

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Perché non è possibile creare un lab a causa della mancata disponibilità dell'intervallo di indirizzi? 
I lab per le classi possono creare VM all'interno di un intervallo di indirizzi IP specificato durante la creazione dell'account lab nel portale di Azure. Quando viene fornito un intervallo di indirizzi, a ogni lab creato in seguito vengono assegnati 512 indirizzi IP per le relative VM. L'intervallo di indirizzi per l'account lab deve essere sufficientemente grande da supportare tutti i lab che si intende creare al suo interno. 

Se ad esempio si ha un blocco /19-10.0.0.0/19, questo intervallo di indirizzi supporta 8192 indirizzi IP e 16 Lab (8192/512 = 16 lab). In questo caso, la creazione del diciassettesimo lab non riesce.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quali intervalli di porte è necessario aprire nell'impostazione del firewall dell'organizzazione per connettersi alle macchine virtuali del lab tramite RDP/SSH?

Le porte sono comprese nell'intervallo 49152–65535. I lab per le classi si trovano dietro a un servizio di bilanciamento del carico. Ogni lab ha un singolo indirizzo IP pubblico e ogni macchina virtuale al suo interno ha una porta univoca. 

È anche possibile vedere l'indirizzo IP privato di ogni macchina virtuale nella scheda **Pool di macchine virtuali** della home page del lab nel portale di Azure. Se si ripubblica un lab, il relativo indirizzo IP pubblico non cambierà, mentre l'indirizzo IP privato e il numero di porta di ogni macchina virtuale nel lab possono cambiare. Per altre informazioni, vedere l'articolo [Impostazioni del firewall per Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Quale intervallo di indirizzi IP pubblici è necessario aprire nell'impostazione del firewall dell'organizzazione per connettersi alle macchine virtuali del lab tramite RDP/SSH?
Vedere [Intervalli di indirizzi IP e tag di servizi di Azure - Cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519), che fornisce l'intervallo di indirizzi IP pubblici per i data center di Azure. È possibile aprire gli indirizzi IP per le aree in cui si trovano gli account lab.

## <a name="virtual-machine-images"></a>Immagini di macchine virtuali

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Perché quando si crea un nuovo lab non è possibile abilitare altre opzioni di immagini nell'elenco a discesa delle immagini di macchine virtuali?

Quando un amministratore aggiunge l'utente come autore del lab a un account lab, gli concede le autorizzazioni per la creazione dei lab. Tuttavia, l'utente non ha le autorizzazioni per modificare le impostazioni all'interno dell'account lab, incluso l'elenco di immagini di macchine virtuali abilitate. Se necessario, chiedere all'amministratore dell'account lab di abilitare altre immagini oppure di essere aggiunti con il ruolo Collaboratore all'account lab. Il ruolo Collaboratore concede le autorizzazioni per modificare l'elenco di immagini di macchine virtuali nell'account lab.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>È possibile collegare altri dischi a una macchina virtuale?
No. Non è possibile collegare altri dischi a una VM di un lab per le classi. 

## <a name="users"></a>Utenti

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Quanti utenti possono essere inclusi in un lab per le classi?
È possibile aggiungere fino a 400 utenti. 

## <a name="blog-post"></a>Post di blog
Sottoscrivere il [blog di Azure Lab Services](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Notifiche sugli aggiornamenti
Per rimanere aggiornati sulle nuove funzionalità di Lab Services, sottoscrivere gli [aggiornamenti di Lab Services](https://azure.microsoft.com/updates/?product=lab-services).

## <a name="general"></a>Generale
### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Nel caso di domande non presenti nell'elenco, segnalarlo per ottenere assistenza.

- Pubblicare una domanda alla fine di questo articolo di domande frequenti. 
- Per raggiungere un pubblico più ampio, pubblicare la domanda nel [forum della community tecnica di Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Per richiedere funzionalità, inviare suggerimenti e idee al [sito User Voice per Azure Lab Services](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

