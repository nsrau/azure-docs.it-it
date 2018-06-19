---
title: Informazioni su Azure Lab Services | Microsoft Docs
description: Informazioni su come Lab Services può semplificare la creazione, la gestione e la protezione di lab con macchine virtuali che possono essere usate da sviluppatori, tester, educatori, studenti e altri utenti.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660530"
---
# <a name="introduction-to-classroom-labs"></a>Introduzione ai lab per le classi
Azure Lab Services consente di configurare rapidamente un ambiente di lab per le classi nel cloud. Un docente crea un lab per le classi, esegue il provisioning delle macchine virtuali Windows o Linux, installa il software e gli strumenti necessari e li rende disponibili per gli studenti. Gli studenti nella classe si connettono alle macchine virtuali del lab e le usano per i progetti, per i compiti o per gli esercizi in classe. 

I lab per le classi sono lab gestiti da Azure. Il servizio controlla completamente la gestione dell'infrastruttura per un lab gestito, dall'attivazione delle macchine virtuali alla gestione degli errori e alla scalabilità dell'infrastruttura. Specificare il tipo di infrastruttura necessario e installare qualsiasi strumento o software richiesto per la classe. I lab gestiti sono attualmente disponibili in anteprima.  

## <a name="scenarios"></a>Scenari
Ecco lo scenario principale supportato dai lab per le classi di Azure Lab Services: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurare un computer lab ridimensionabile nel cloud per la classe  

- Crea un lab per la classe È sufficiente indicare esattamente le caratteristiche richieste e il servizio crea e gestisce automaticamente l'infrastruttura del lab in modo che l'utente possa concentrarsi sull'insegnamento, senza doversi occupare dei dettagli tecnici del lab. 
- Fornire agli studenti un lab di macchine virtuali configurate con tutto il necessario per una classe. Assegnare a ogni studente un numero limitato di ore per l'uso delle macchine virtuali per i compiti scolastici.  
- Spostare il lab del computer fisico della scuola nel cloud. Ridimensionare automaticamente il numero di macchine virtuali in base all'utilizzo massimo e alla soglia di costo impostati nel lab. 
- Al termine dell'operazione, eliminare il lab con un solo clic. 

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
