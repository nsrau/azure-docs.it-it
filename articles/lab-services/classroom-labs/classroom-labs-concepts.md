---
title: Concetti sui lab per le classi - Azure Lab Services | Microsoft Docs
description: Informazioni sui concetti di base di Lab Services e su come può semplificare la creazione e la gestione dei lab.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 548cacfb76aba9093a59a5c87525d038558bf353
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592628"
---
# <a name="classroom-labs-concepts"></a>Concetti sui lab per le classi

L'elenco seguente contiene le definizioni e i concetti chiave di Lab Services:

## <a name="quota"></a>Quota

La quota è il limite di tempo (in ore) che un docente può impostare per l'utilizzo di una macchina virtuale del lab da parte di uno studente. Può essere impostato su 0 o su un numero specifico di ore. Se la quota è impostata su 0, uno studente può usare la macchina virtuale solo quando è in esecuzione una pianificazione o quando un docente attiva manualmente la macchina virtuale per lo studente.  

Le ore quota vengono conteggiate quando lo studente avvia la macchina virtuale del lab.  Se un docente avvia manualmente la macchina virtuale del lab per uno studente, le ore quota non vengono usate per tale studente.

## <a name="schedules"></a>Pianificazioni

Le pianificazioni sono le fasce orarie che possono essere create da un docente per la classe in modo che le macchine virtuali degli studenti siano disponibili per l'orario delle lezioni.  Le pianificazioni possono essere una tantum o ricorrenti.  Le ore quota non vengono usate quando è in esecuzione una pianificazione.

Sono disponibili tre tipi di pianificazioni: Standard, Solo avvio e Solo arresto.

- **Standard**.  Questa pianificazione avvierà tutte le macchine virtuali degli studenti all'ora di inizio specificata e le arresterà all'ora di arresto specificata.
- **Solo avvio**.   Questa pianificazione avvierà tutte le macchine virtuali degli studenti all'ora specificata.  Le macchine virtuali degli studenti non verranno arrestate fino a quando uno studente non arresta la propria macchina virtuale tramite il portale di Azure Lab Services o si verifica una pianificazione Solo arresto.
- **Solo arresto**.  Questa pianificazione arresterà tutte le macchine virtuali degli studenti all'ora specificata.  

## <a name="template-virtual-machine"></a>Macchina virtuale modello

Una macchina virtuale modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. I formatori e gli autori di lab configurano la macchina virtuale modello con il software che vogliono fornire ai partecipanti al training per eseguire i lab. Quando si pubblica una macchina virtuale modello, Azure Lab Services crea o aggiorna le macchine virtuali del lab in base alla macchina virtuale modello.

## <a name="user-profiles"></a>Profili utente

Questo articolo descrive i diversi profili utente in Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietario dell'account del lab

L'amministratore IT delle risorse cloud aziendali che possiede la sottoscrizione di Azure in genere funge da proprietario dell'account del lab ed esegue le attività seguenti:

- Configura un account del lab per l'organizzazione.
- Gestisce e configura i criteri in tutti i lab.
- Concede le autorizzazioni agli utenti dell'organizzazione per la creazione di un lab all'interno dell'account del lab.

### <a name="educator"></a>Docente

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
