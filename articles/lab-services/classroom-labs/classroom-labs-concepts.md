---
title: Concetti di lab in aula - Azure Lab Services | Microsoft Docs
description: Informazioni sui concetti di base di Lab Services e come può risultare facili da creare e gestire i lab.
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
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679087"
---
# <a name="classroom-labs-concepts"></a>Concetti sui lab per le classi
Nell'elenco seguente contiene le definizioni e concetti di Lab Services:

## <a name="quota"></a>Quota
La quota è il limite di tempo (in ore) che è possibile impostare un insegnante di uno studente a usare macchine virtuali. È possibile essere impostare su 0, illimitato, o un determinato numero di ore. Se la quota è impostata su 0, uno studente può usare solo la macchina virtuale durante l'esecuzione di una pianificazione o quando un insegnante accende manualmente la macchina virtuale per gli studenti.
 
## <a name="schedules"></a>Pianificazioni
Le pianificazioni sono gli slot di tempo (una sola volta o ricorrente) che un insegnante può creare per la classe. Tutte le macchine virtuali nel lab vengono avviate automaticamente all'inizio alla pianificazione e vengono arrestati alla fine della pianificazione. Ore associate alle quote non vengono utilizzate durante l'esecuzione di una pianificazione.

## <a name="template-virtual-machine"></a>Macchina virtuale di modello
Una macchina virtuale di modello in un lab è un'immagine di macchina virtuale di base da cui vengono create le macchine virtuali di tutti gli utenti. Gli autori di formatori/lab configurare la macchina virtuale di modello e configurarlo con il software che si desidera fornire ai partecipanti di training per eseguire operazioni lab. Quando si pubblica un modello di macchina virtuale, Azure Lab Services crea o aggiorna le macchine virtuali lab basate sul modello di macchina virtuale. 


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
