---
title: Usare Classroom Labs per i corsi di formazione - Azure Lab ServicesUse Classroom Labs for trainings - Azure Lab Services
description: Questo articolo descrive come usare Azure DevTest Labs per la creazione di laboratori in Azure per scenari di formazione.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717989"
---
# <a name="use-classroom-labs-for-trainings"></a>Utilizzare Classroom Labs per i corsi di formazione
È possibile impostare un laboratorio per gli allenamenti. Classroom Labs di Azure Lab Services consente di creare un lab per la formazione in cui ogni tirocinante usa ambienti identici e isolati per la formazione. È possibile applicare criteri per verificare che per ogni partecipante siano disponibili ambienti di training solo quando sono necessari e contengano un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. 

![Laboratorio di classroom](../media/classroom-labs-scenarios/classroom.png)

Classroom Labs soddisfa i seguenti requisiti necessari per condurre corsi di formazione in qualsiasi ambiente virtuale: 

- I partecipanti possono effettuare rapidamente il provisioning degli ambienti di training
- Ogni computer di training deve essere identico
- I partecipanti non possono visualizzare le VM create da altri partecipanti
- Controllare il costo assicurandosi che i partecipanti non possano ottenere più VM di quelle necessarie per il training né arrestare le VM quando non le usano
- Condividere facilmente il lab di training con ogni partecipante
- Usare più volte il lab di training

In questo articolo vengono fornite informazioni su varie funzionalità di Azure Lab Services che possono essere usate per soddisfare i requisiti di formazione descritti in precedenza e i passaggi dettagliati che è possibile seguire per configurare un lab per la formazione.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Creare l'account lab come amministratore dell'account labCreate the lab account as a lab account administrator
Il primo passaggio nell'uso di Azure Lab Services consiste nel creare un account lab nel portale di Azure.The first step in using Azure Lab Services is to create a lab account in the Azure portal. Dopo che un amministratore dell'account lab ha creato l'account lab, l'amministratore aggiunge gli utenti che desiderano creare lab al ruolo **Lab Creator.** I formatori creano laboratori con macchine virtuali per gli studenti per fare esercizi per il corso che stanno insegnando. Per informazioni dettagliate, vedere [Creare e gestire l'account lab.](how-to-manage-lab-accounts.md)

## <a name="create-and-manage-classroom-labs"></a>Creare e gestire i lab per le classi
Un formatore, membro del ruolo Creatore lab in un account lab, può creare uno o più lab nell'account lab. È possibile creare e configurare una macchina virtuale modello con tutto il software necessario per eseguire esercizi nel corso. È possibile scegliere un'immagine già pronta dalle immagini disponibili per la creazione di un lab della classe e quindi personalizzarla installando il software necessario per il lab. Per informazioni dettagliate, vedere [Creare e gestire i laboratori di classe.](how-to-manage-classroom-labs.md)

## <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
L'autore del lab può aggiungere o rimuovere utenti al lab, ottenere un collegamento di registrazione da inviare agli utenti del lab, impostare criteri quali l'impostazione di singole quote per utente, aggiornare il numero di macchine virtuali disponibili nel lab e altro ancora. Per informazioni [dettagliate,](how-to-configure-student-usage.md)vedere Configurare le impostazioni di utilizzo e i criteri .

## <a name="create-and-manage-schedules"></a>Creare e gestire pianificazioni
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Per informazioni dettagliate, vedere [Creare e gestire pianificazioni per i laboratori in aula.](how-to-create-schedules.md)

## <a name="set-up-and-publish-a-template-vm"></a>Configurare e pubblicare una macchina virtuale modelloSet up and publish a template VM
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale del modello in modo che sia configurata con esattamente ciò che si vuole fornire ai partecipanti alla formazione. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. Per informazioni dettagliate, vedere [Configurare e pubblicare macchine virtuali modello](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usare le macchine virtuali nel laboratorio della classeUse VMs in the classroom lab
Uno studente o un partecipante alla formazione si registra al laboratorio e si connette alla macchina virtuale per eseguire esercizi per il corso. Per informazioni [dettagliate,](how-to-use-classroom-lab.md)vedere Come accedere a un laboratorio di classroom .

## <a name="next-steps"></a>Passaggi successivi
Iniziare con la creazione di un account lab in Classroom Labs seguendo le istruzioni nell'articolo: [Esercitazione: Configurare un account lab con Azure Lab Services.](tutorial-setup-lab-account.md)