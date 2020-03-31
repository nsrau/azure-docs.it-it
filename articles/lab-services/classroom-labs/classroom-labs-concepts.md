---
title: Concetti di Classroom Labs - Servizi di laboratorio di Azure Documenti Microsoft
description: Scopri i concetti di base di Servizi di laboratorio e come può semplificare la creazione e la gestione dei lab.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526230"
---
# <a name="classroom-labs-concepts"></a>Concetti sui lab per le classi

L'elenco seguente contiene i concetti e le definizioni chiave di Lab Services:

## <a name="quota"></a>Quota

La quota è il limite di tempo (in ore) che un insegnante può impostare affinché uno studente usi una macchina virtuale del lab. Può essere impostato su 0 o su un numero specifico di ore. Se la quota è impostata su 0, uno studente può usare la macchina virtuale solo quando è in esecuzione una pianificazione o quando un insegnante attiva manualmente la macchina virtuale per lo studente.  

Le ore di quota vengono conteggiate quando lo studente avvia la macchina virtuale del laboratorio.  Se un insegnante avvia manualmente la macchina virtuale del laboratorio per uno studente, le ore di quota non vengono usate per lo studente.

## <a name="schedules"></a>Pianificazioni

Le pianificazioni sono le fasce orarie che un insegnante può creare per la classe in modo che le macchine virtuali degli studenti siano disponibili per il tempo della lezione.  Le pianificazioni possono essere una tantum o ricorrenti.  Le ore di quota non vengono utilizzate quando è in esecuzione una pianificazione.

Esistono tre tipi di pianificazioni: Standard, Solo avvio e Solo arresto.

- **Standard**.  Questa pianificazione avvierà tutte le macchine virtuali degli studenti all'ora di inizio specificata e arresterà tutte le macchine virtuali degli studenti all'ora di arresto specificata.
- **Avvia solo**.   Questa pianificazione avvierà tutte le macchine virtuali degli studenti all'ora specificata.  Le macchine virtuali degli studenti non verranno interrotte finché uno studente non arresta la macchina virtuale tramite il portale di Azure Lab Services o non si verifica una pianificazione di sola interruzione.
- **Interrompi solo**.  Questa pianificazione interromperà tutte le macchine virtuali degli studenti all'ora specificata.  

## <a name="template-virtual-machine"></a>Macchina virtuale modello

Una macchina virtuale modello in un lab è un'immagine di macchina virtuale di base da cui vengono create le macchine virtuali di tutti gli utenti. I creatori di laboratorio configurano la macchina virtuale modello e la configurano con il software che desiderano fornire ai partecipanti alla formazione per eseguire esercitazioni. Quando si pubblica una macchina virtuale modello, Azure Lab Services crea o aggiorna le macchine virtuali lab in base alla macchina virtuale modello.

## <a name="user-profiles"></a>Profili utente

Questo articolo descrive i diversi profili utente in Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietario dell'account del lab

L'amministratore IT delle risorse cloud aziendali che possiede la sottoscrizione di Azure in genere funge da proprietario dell'account del lab ed esegue le attività seguenti:

- Configura un account del lab per l'organizzazione.
- Gestisce e configura i criteri in tutti i lab.
- Concede le autorizzazioni agli utenti dell'organizzazione per la creazione di un lab all'interno dell'account del lab.

### <a name="professor"></a>Professore

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
