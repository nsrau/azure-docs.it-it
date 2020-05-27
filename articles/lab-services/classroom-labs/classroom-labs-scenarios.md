---
title: Usare i lab per le classi per i training - Azure Lab Services
description: Questo articolo descrive come usare Azure DevTest Labs per la creazione di lab su Azure per gli scenari di training.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 26caebafc7c147452decbb28e313513072d7511b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592050"
---
# <a name="use-classroom-labs-for-trainings"></a>Usare i lab per le classi per i training
Azure Lab Services consente ai docenti (insegnanti, professori, istruttori, assistenti docenti e così via) di creare in modo rapido e semplice un lab online per eseguire il provisioning di ambienti di apprendimento preconfigurati per i partecipanti. Ogni partecipante può usare ambienti identici e isolati per il training. È possibile applicare criteri per verificare che gli ambienti di training siano disponibili per ogni partecipante solo quando sono necessari e contengono un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. 

![Lab per le classi](../media/classroom-labs-scenarios/classroom.png)

I lab per le classi soddisfano i requisiti seguenti, necessari per eseguire il training in un ambiente virtuale: 

- I partecipanti possono effettuare rapidamente il provisioning degli ambienti di training
- Ogni computer di training deve essere identico
- I partecipanti non possono visualizzare le VM create da altri partecipanti
- Controllare il costo assicurandosi che i partecipanti non possano ottenere più VM di quelle necessarie per il training né arrestare le VM quando non le usano
- Condividere facilmente il lab di training con ogni partecipante
- Usare più volte il lab di training

Questo articolo illustra le diverse funzionalità di Azure Lab Services che possono essere usate per soddisfare i requisiti di training sopra descritti, oltre ai passaggi dettagliati che è possibile seguire per configurare un lab per un training.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Creare l'account lab come amministratore dell'account lab
Il primo passaggio nell'uso di Azure Lab Services consiste nel creare un account lab nel portale di Azure. Dopo la creazione dell'account lab da parte di un amministratore, quest'ultimo assegna il ruolo **Autore di lab** agli utenti che desiderano creare lab. I docenti creano lab tramite macchine virtuali, consentendo agli studenti di eseguire esercitazioni per il corso che gestiscono. Per informazioni dettagliate, vedere [Creare e gestire account lab](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Creare e gestire i lab per le classi
Un docente con ruolo di autore di lab in un account lab può creare uno o più lab. Si crea e si configura una macchina virtuale modello con tutto il software necessario per eseguire le esercitazioni del corso. È possibile scegliere un'immagine già pronta tra le immagini disponibili per la creazione di un lab per la classe, quindi personalizzarla installando il software necessario per il lab. Per informazioni dettagliate, vedere [Creare e gestire lab per le classi](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
L'autore del lab può aggiungere o rimuovere utenti dal lab, ottenere il collegamento di registrazione da inviare agli utenti, configurare criteri come l'impostazione di quote singole per ogni utente, aggiornare il numero di macchine virtuali disponibili nel lab e altro ancora. Per informazioni dettagliate, vedere [Configurare le impostazioni e i criteri di utilizzo](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Creare e gestire pianificazioni
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Per informazioni dettagliate, vedere [Creare e gestire pianificazioni di lab per le classi](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurare e pubblicare una macchina virtuale modello
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vogliono fornire ai partecipanti del training. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. Per informazioni dettagliate, vedere [Configurare e pubblicare macchine virtuali modello](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usare macchine virtuali nel lab per le classi
Uno studente o partecipante del training si registra al lab e si connette alla macchina virtuale per eseguire le esercitazioni del corso. Per informazioni dettagliate, vedere [Come accedere a un lab per le classi](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Passaggi successivi
Iniziare con la creazione di un account lab nei lab per le classi seguendo le istruzioni riportate nell'articolo: [Esercitazione: Configurare un account lab con Azure Lab Services](tutorial-setup-lab-account.md).