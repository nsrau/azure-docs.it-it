---
title: Usare i lab per le classi per la formazione - Azure Lab Services | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per gli scenari di training.
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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60695274"
---
# <a name="use-classroom-labs-for-trainings"></a>Usare i lab per le classi per la formazione
È possibile configurare un lab per corsi di formazione. Classroom lab di Azure Lab Services consentono di creare un lab per il training in cui ogni partecipante Usa ambienti identici e isolati per il training. È possibile applicare criteri per verificare che per ogni partecipante siano disponibili ambienti di training solo quando sono necessari e contengano un numero sufficiente di risorse, ad esempio macchine virtuali, per il training. 

![Lab per le classi](../media/classroom-labs-scenarios/classroom.png)

Labs in aula soddisfi i requisiti seguenti necessari per eseguire il training in un ambiente virtuale: 

- I partecipanti possono effettuare rapidamente il provisioning degli ambienti di training
- Ogni computer di training deve essere identico
- I partecipanti non possono visualizzare le VM create da altri partecipanti
- Controllare il costo assicurandosi che i partecipanti non possano ottenere più VM di quelle necessarie per il training né arrestare le VM quando non le usano
- Condividere facilmente il lab di training con ogni partecipante
- Usare più volte il lab di training

In questo articolo descrive le diverse funzionalità di Azure Lab Services che può essere utilizzata per soddisfare i requisiti di training descritti prima e la procedura dettagliata da seguire per configurare un lab per il training.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Creare l'account del lab come un amministratore dell'account lab
Il primo passaggio nell'uso di Azure Lab Services consiste nel creare un account del lab nel portale di Azure. Dopo che un amministratore dell'account lab ha creato l'account del lab, l'amministratore aggiunge gli utenti che desiderano creare lab per il **creatore di Lab** ruolo. Formatori creare lab con le macchine virtuali per eseguire gli esercizi del corso che si rivolge per studenti. Per informazioni dettagliate, vedere [creare e gestire account lab](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Creare e gestire i lab per le classi
Un istruttore, che è un membro del ruolo di creatore di Lab in un account del lab, è possibile creare uno o più laboratori nell'account di laboratorio. Creare e configurare un modello di macchina virtuale con tutto il software necessario per eseguire gli esercizi del corso. Si seleziona un'immagine predefinita dalle immagini disponibili per la creazione di un lab per le classi e quindi personalizzarla installando il software necessario per l'ambiente lab. Per informazioni dettagliate, vedere [creare e gestire i lab per le classi](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
Il creatore di lab possa aggiungere o rimuovere gli utenti del lab, ottenere il collegamento di iscrizione per l'invio agli utenti del lab, impostare i criteri, ad esempio l'impostazione delle quote singoli per ogni utente, aggiornare il numero di VM disponibili nel lab e altro ancora. Per informazioni dettagliate, vedere [configurare i criteri e impostazioni di utilizzo](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Creare e gestire pianificazioni
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Per informazioni dettagliate, vedere [creare e gestire le pianificazioni per labs in aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurare e pubblicare un modello di macchina virtuale
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare il modello di macchina virtuale in modo che venga configurato con esattamente ciò che si desidera fornire ai partecipanti corsi di formazione. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. Per informazioni dettagliate, vedere [Set up e pubblicare le macchine virtuali modello](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usare macchine virtuali nel lab in aula
Uno studente o un partecipante training registra nel lab e si connette alla macchina virtuale per eseguire gli esercizi del corso. Per informazioni dettagliate, vedere [come accedere a un lab per le classi](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Passaggi successivi
Iniziare con la creazione di un account del lab in Lab per le classi seguendo le istruzioni disponibili nell'articolo: [Esercitazione: Configurare un account di laboratorio con Azure Lab Services](tutorial-setup-lab-account.md).