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
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: 1c4fb6660f8eaebe2a65e3dba3f9adbb07f1eb22
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84882715"
---
# <a name="an-introduction-to-azure-lab-services"></a>Introduzione ad Azure Lab Services
**Azure Lab Services** consente di creare lab la cui infrastruttura viene gestita da Azure. Attualmente il lab per le classi è l'unico tipo di lab gestito supportato da Azure Lab Services. Il servizio controlla completamente la gestione dell'infrastruttura per un tipo di lab gestito, dall'attivazione delle macchine virtuali alla gestione degli errori, fino al dimensionamento dell'infrastruttura. Dopo che un amministratore IT ha creato un account lab in Azure Lab Services, un istruttore può configurare rapidamente un lab per la propria classe, specificare il numero e il tipo di macchine virtuali necessarie per gli esercizi e aggiungere utenti. Dopo la registrazione alla classe, l'utente può accedere alla macchina virtuale per eseguire gli esercizi per la classe.  

## <a name="key-capabilities"></a>Funzionalità principali
Azure Lab Services supporta le caratteristiche e funzionalità principali seguenti:

- **Configurazione veloce e flessibile di un lab**. Tramite Azure Lab Services, i proprietari di lab possono configurare velocemente un lab per le loro esigenze. Il servizio offre la possibilità di gestire tutte le attività dell'infrastruttura di Azure per i tipi di lab gestiti. Il servizio offre scalabilità e resilienza dell'infrastruttura predefinite per i lab gestiti che il servizio gestisce automaticamente.
- **Esperienza semplificata per gli utenti dei lab**. Gli utenti del lab possono registrarsi a un lab con un codice di registrazione e accedere al lab in qualsiasi momento per usare le relative risorse. 
- **Analisi e ottimizzazione dei costi**. Il proprietario del lab può impostare pianificazioni del lab per arrestare e avviare automaticamente le macchine virtuali. Il proprietario del lab può impostare una pianificazione per specificare le fasce orarie in cui le macchine virtuali del lab sono accessibili agli utenti e impostare i criteri di utilizzo per ciascun utente o lab per ottimizzare i costi. 

Se si vuole semplicemente indicare le caratteristiche richieste per il lab e consentire al servizio di configurare e gestire l'infrastruttura necessaria per il lab, scegliere uno dei **tipi di lab gestiti**. Attualmente il **lab per le classi** è l'unico tipo di lab gestito che è possibile creare con Azure Lab Services.

Le sezioni seguenti includono altre informazioni dettagliate su queste opzioni. 

## <a name="managed-lab-types"></a>Tipi di lab gestiti
Azure Lab Services consente di creare lab la cui infrastruttura viene gestita da Azure. In questo articolo questi lab vengono detti tipi di lab gestiti. I tipi di lab gestiti includono diversi tipi di lab per soddisfare esigenze specifiche. Attualmente l'unico tipo di lab gestito supportato è il **lab per le classi**. 

I tipi di lab gestiti consentono di iniziare subito, con una configurazione minima. Il servizio controlla completamente la gestione dell'infrastruttura per il lab, dall'attivazione delle macchine virtuali alla gestione degli errori e alla scalabilità dell'infrastruttura. Per creare un tipo lab gestito, ad esempio un lab per le classi, è necessario prima di tutto creare un account del lab per l'organizzazione. L'account del lab funge da account centrale in cui vengono gestiti tutti i lab dell'organizzazione. 

Quando si creano e si usano risorse di Azure in questi tipi di lab gestiti, il servizio crea e gestisce le risorse nelle sottoscrizioni interne di Microsoft. Le risorse non vengono create nella sottoscrizione di Azure. Il servizio tiene traccia dell'utilizzo di tali risorse nelle sottoscrizioni interne di Microsoft. Il costo di tale utilizzo viene addebitato alla sottoscrizione di Azure che contiene l'account del lab.   

Ecco alcuni **casi d'uso per i tipi di lab gestiti**: 

- Fornire agli studenti un lab di macchine virtuali configurate con tutto il necessario per una classe. Assegnare a ogni studente un numero limitato di ore per l'uso delle macchine virtuali per compiti scolastici o progetti personali.
- Configurare un pool di macchine virtuali di calcolo a prestazioni elevate per eseguire ricerche con elevato utilizzo di calcolo o di grafica. Eseguire le macchine virtuali in base alle esigenze e al termine dell'operazione pulire i computer. 
- Spostare il lab del computer fisico della scuola nel cloud. Ridimensionare automaticamente il numero di macchine virtuali in base all'utilizzo massimo e alla soglia di costo impostati nel lab.  
- Eseguire rapidamente il provisioning di un lab di macchine virtuali per ospitare un hackathon. Al termine dell'operazione, eliminare il lab con un solo clic. 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate su come creare un account lab e un lab per le classi, vedere le esercitazioni seguenti.

- [Esercitazione: Configurare un account lab](tutorial-setup-lab-account.md)
- [Esercitazione: Creare un lab per le classi](tutorial-setup-classroom-lab.md)
