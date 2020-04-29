---
title: Concetti relativi ai Lab della classe-Azure Lab Services | Microsoft Docs
description: Scopri i concetti di base di Lab Services e come può semplificare la creazione e la gestione dei Lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77526230"
---
# <a name="classroom-labs-concepts"></a>Concetti sui lab per le classi

L'elenco seguente contiene i concetti e le definizioni principali dei servizi Lab:

## <a name="quota"></a>Quota

Quota è il limite di tempo (in ore) che un insegnante può impostare affinché uno studente possa usare una macchina virtuale Lab. Può essere impostato su 0 o su un numero specifico di ore. Se la quota è impostata su 0, uno studente può utilizzare la macchina virtuale solo quando è in esecuzione una pianificazione o quando un insegnante accende manualmente la macchina virtuale per lo studente.  

Le ore di quota vengono conteggiate quando lo studente avvia la macchina virtuale del Lab.  Se un insegnante avvia manualmente la macchina virtuale del Lab per uno studente, le ore di quota non vengono usate per tale studente.

## <a name="schedules"></a>Pianificazioni

Le pianificazioni sono gli intervalli di tempo che un insegnante può creare per la classe, in modo che le macchine virtuali degli studenti siano disponibili per il tempo delle classi.  Le pianificazioni possono essere una sola volta o ricorrenti.  Le ore di quota non vengono utilizzate quando si esegue una pianificazione.

Sono disponibili tre tipi di pianificazioni: standard, solo di avvio e di arresto.

- **Standard**.  Questa pianificazione avvierà tutte le VM studente all'ora di inizio specificata e arresterà tutte le VM studente all'ora di arresto specificata.
- **Solo avvia**.   Questa pianificazione avvierà tutte le macchine virtuali degli studenti all'ora specificata.  Le macchine virtuali degli studenti non verranno arrestate fino a quando uno studente non arresta la macchina virtuale tramite il portale di Azure Lab Services o si verifica una pianificazione di solo arresto.
- **Interrompi solo**.  Questa pianificazione arresterà tutte le macchine virtuali degli studenti all'ora specificata.  

## <a name="template-virtual-machine"></a>Macchina virtuale modello

Una macchina virtuale modello in un Lab è un'immagine di macchina virtuale di base da cui vengono create tutte le macchine virtuali degli utenti. I docenti e i creatori di Lab configurano la macchina virtuale del modello e la configurano con il software che vogliono fornire ai partecipanti di formazione per eseguire laboratori. Quando si pubblica una macchina virtuale modello, Azure Lab Services crea o aggiorna le VM Lab basate sulla macchina virtuale modello.

## <a name="user-profiles"></a>Profili utente

Questo articolo descrive i diversi profili utente in Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietario dell'account del lab

L'amministratore IT delle risorse cloud aziendali che possiede la sottoscrizione di Azure in genere funge da proprietario dell'account del lab ed esegue le attività seguenti:

- Configura un account del lab per l'organizzazione.
- Gestisce e configura i criteri in tutti i lab.
- Concede le autorizzazioni agli utenti dell'organizzazione per la creazione di un lab all'interno dell'account del lab.

### <a name="professor"></a>Professor

In genere, gli utenti con il ruolo di docente o trainer online creano i lab per le classi con un account lab. Il docente esegue le attività seguenti:

- Crea un lab per le classi.
- Crea le macchine virtuali nel lab.
- Installa il software appropriato nelle macchine virtuali.
- Specifica chi può accedere al lab.
- Fornisce il collegamento di registrazione per il lab agli studenti.

### <a name="student"></a>Studente

Uno studente esegue le attività seguenti:

- Usa il collegamento di registrazione che l'utente del lab riceve da un creatore del lab per registrarsi nel lab.
- Si connette a una macchina virtuale nel lab e la usa per le attività di classe, i compiti e i progetti.

## <a name="next-steps"></a>Passaggi successivi

Leggere informazioni introduttive sulla configurazione di un account lab necessario per creare un lab per le classi con Azure Lab Services:

- [Configurare un account Lab](tutorial-setup-lab-account.md)
