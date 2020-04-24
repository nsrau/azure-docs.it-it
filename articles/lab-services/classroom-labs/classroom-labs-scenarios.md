---
title: Usare i Lab della classe per i corsi di formazione-Azure Lab Services
description: Questo articolo descrive come usare Azure DevTest Labs per la creazione di Lab in Azure per gli scenari di training.
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
# <a name="use-classroom-labs-for-trainings"></a>Usare i Lab della classe per i corsi di formazione
È possibile configurare un Lab per il training. I Lab della classe Azure Lab Services consentono di creare un Lab per il training in cui ogni partecipante USA ambienti identici e isolati per il training. È possibile applicare criteri per verificare che per ogni partecipante siano disponibili ambienti di training solo quando sono necessari e contengano un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. 

![Lab in aula](../media/classroom-labs-scenarios/classroom.png)

I Lab in aula soddisfano i requisiti seguenti, necessari per condurre il training in qualsiasi ambiente virtuale: 

- I partecipanti possono effettuare rapidamente il provisioning degli ambienti di training
- Ogni computer di training deve essere identico
- I partecipanti non possono visualizzare le VM create da altri partecipanti
- Controllare il costo assicurandosi che i partecipanti non possano ottenere più VM di quelle necessarie per il training né arrestare le VM quando non le usano
- Condividere facilmente il lab di training con ogni partecipante
- Usare più volte il lab di training

Questo articolo illustra le diverse funzionalità di Azure Lab Services che possono essere usate per soddisfare i requisiti di formazione descritti in precedenza e i passaggi dettagliati che è possibile seguire per configurare un Lab per il training.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Creare l'account Lab come amministratore dell'account Lab
Il primo passaggio nell'uso di Azure Lab Services consiste nel creare un account Lab nel portale di Azure. Dopo che un amministratore dell'account Lab ha creato l'account Lab, l'amministratore aggiunge gli utenti che vogliono creare Lab per il ruolo di **autore del Lab** . I trainer creano laboratori con macchine virtuali che consentono agli studenti di eseguire esercitazioni per il corso di insegnamento. Per informazioni dettagliate, vedere [creare e gestire un account Lab](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Creare e gestire i lab per le classi
Un trainer, che è membro del ruolo di autore del Lab in un account Lab, può creare uno o più Lab nell'account Lab. Si crea e si configura una macchina virtuale modello con tutto il software necessario per eseguire esercitazioni nel corso. È possibile scegliere un'immagine già creata dalle immagini disponibili per la creazione di un Lab per la classe e quindi personalizzarla installando il software necessario per il Lab. Per informazioni dettagliate, vedere [creare e gestire lab in classe](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
L'autore del Lab può aggiungere o rimuovere gli utenti dal Lab, ottenere il collegamento di registrazione da inviare agli utenti del Lab, configurare criteri come l'impostazione di quote singole per ogni utente, aggiornare il numero di macchine virtuali disponibili nel Lab e altro ancora. Per informazioni dettagliate, vedere [configurare le impostazioni e i criteri di utilizzo](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Creare e gestire pianificazioni
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Per informazioni dettagliate, vedere [creare e gestire pianificazioni per laboratori in classe](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurare e pubblicare una macchina virtuale modello
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello in modo che sia configurata con esattamente ciò che si vuole fornire ai partecipanti alla formazione. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. Per informazioni dettagliate, vedere [configurare e pubblicare macchine virtuali modello](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usare macchine virtuali nel Lab della classe
Uno studente o un partecipante alla formazione si registra al Lab e si connette alla macchina virtuale per eseguire esercitazioni per il corso. Per informazioni dettagliate, vedere [come accedere a un Lab della classe](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Passaggi successivi
Iniziare con la creazione di un account Lab in aule Lab seguendo le istruzioni riportate nell'articolo: [esercitazione: configurare un account Lab con Azure Lab Services](tutorial-setup-lab-account.md).