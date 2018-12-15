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
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 2f4bc3bc00d1a803ed678e49df23a78e6b063e50
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957794"
---
# <a name="an-introduction-to-azure-lab-services"></a>Introduzione ad Azure Lab Services

Azure Lab Services consente di configurare rapidamente nel cloud un ambiente per il team, ad esempio un ambiente di sviluppo, un ambiente di test o un ambiente di lab per le classi. Il proprietario di un lab crea un lab, esegue il provisioning delle macchine virtuali Windows o Linux, installa il software e gli strumenti necessari e li rende disponibili per gli utenti del lab. Gli utenti del lab si connettono alle macchine virtuali del lab e le usano per le attività giornaliere, per i progetti a breve termine o per svolgere i compiti scolastici. Quando gli utenti iniziano a usare le risorse nel lab, un amministratore del lab può analizzare il costo e l'utilizzo in più lab e impostare criteri omnicomprensivi per ottimizzare i costi del team o dell'organizzazione.

> [!IMPORTANT]
> **Azure DevTest Labs** è in fase di ampliamento con nuovi tipi di lab (Azure Lab Services).
>  
> Azure Lab Services consente di creare lab gestiti, ad esempio lab per le classi. Il servizio controlla completamente la gestione dell'infrastruttura per un lab gestito, dall'attivazione delle macchine virtuali alla gestione degli errori e alla scalabilità dell'infrastruttura. I lab gestiti sono attualmente disponibili in anteprima. Al termine della fase di anteprima, i nuovi tipi di lab e i DevTest Labs esistenti saranno tutti identificati con il nuovo nome Azure Lab Services, in cui tutti i tipi di lab continueranno a evolversi.

## <a name="key-capabilities"></a>Funzionalità principali

Azure Lab Services supporta le caratteristiche e funzionalità principali seguenti:

- **Configurazione veloce e flessibile di un lab**. Tramite Azure Lab Services, i proprietari di lab possono configurare velocemente un lab per le loro esigenze. Il servizio offre la possibilità di controllare completamente la gestione dell'infrastruttura di Azure per i lab gestiti oppure di consentire ai proprietari di lab di gestire autonomamente e di personalizzare l'infrastruttura nella sottoscrizione del proprietario del lab. Il servizio offre scalabilità e resilienza dell'infrastruttura predefinite per i lab gestiti che il servizio gestisce automaticamente.
- **Esperienza semplificata per gli utenti dei lab**. In un lab gestito, ad esempio un lab per le classi, gli utenti del lab possono eseguire la registrazione a un lab con un codice di registrazione e accedervi in qualsiasi momento per usare le risorse del lab. In un lab creato in DevTest Labs il proprietario del lab può concedere agli utenti del lab autorizzazioni per creare e accedere alle macchine virtuali, gestire e riutilizzare i dischi dati e configurare i segreti riutilizzabili.  
- **Analisi e ottimizzazione dei costi**. Il proprietario del lab può impostare pianificazioni del lab per arrestare e avviare automaticamente le macchine virtuali. Il proprietario del lab può impostare una pianificazione per specificare le fasce orarie in cui le macchine virtuali del lab sono accessibili agli utenti, impostare i criteri di utilizzo per ciascun utente o lab per ottimizzare i costi, nonché analizzare le tendenze di utilizzo e attività in un lab. Per i lab gestiti, ad esempio i lab per le classi, è attualmente disponibile un subset ridotto di opzioni per l'ottimizzazione e l'analisi dei costi.
- **Sicurezza incorporata**. Il proprietario di un lab può configurare una subnet e una rete virtuale privata per il lab e abilitare un indirizzo IP pubblico condiviso. Gli utenti del lab possono accedere in modo sicuro alle risorse tramite la rete virtuale configurata con ExpressRoute o con VPN da sito a sito. (attualmente disponibile solo in DevTest Labs)
- **Integrazione con gli strumenti e i flussi di lavoro**. Azure Lab Services consente di integrare i lab nei sistemi di gestione e nel sito Web dell'organizzazione. È possibile eseguire automaticamente il provisioning di ambienti dagli strumenti di integrazione continua e distribuzione continua (CI/CD). (attualmente disponibile solo in DevTest Labs)

> [!NOTE]
> Attualmente Azure Lab Services supporta solo le macchine virtuali create da immagini del Marketplace di Azure. Se si vogliono usare immagini personalizzate o creare altre risorse PaaS in un ambiente lab, usare DevTest Labs. Per altre informazioni, vedere [Creare un'immagine personalizzata in DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) e [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Scenari

Ecco alcuni degli scenari supportati da Azure Lab Services:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurare un computer lab ridimensionabile nel cloud per la classe  

- Crea un lab per la classe È sufficiente indicare esattamente le caratteristiche richieste e il servizio creerà e gestirà automaticamente l'infrastruttura del lab in modo che l'utente possa concentrarsi sull'insegnamento, senza doversi occupare dei dettagli tecnici del lab.
- Fornire agli studenti un lab di macchine virtuali configurate con tutto il necessario per una classe. Assegnare a ogni studente un numero limitato di ore per l'uso delle macchine virtuali per i compiti scolastici.  
- Spostare il lab del computer fisico della scuola nel cloud. Ridimensionare automaticamente il numero di macchine virtuali in base all'utilizzo massimo e alla soglia di costo impostati nel lab.
- Al termine dell'operazione, eliminare il lab con un solo clic.

### <a name="use-devtest-labs-for-development-environments"></a>Usare DevTest Labs per gli ambienti di sviluppo

Azure DevTest Labs può essere usato per implementare molti scenari chiave. Uno dei più importanti prevede l'uso di DevTest Labs per ospitare computer di sviluppo per gli sviluppatori. In questo scenario DevTest Labs offre i vantaggi seguenti:

- Consentire agli sviluppatori di eseguire rapidamente il provisioning dei computer di sviluppo on demand.
- Eseguire il provisioning degli ambienti Windows e Linux tramite elementi e modelli riutilizzabili.
- Gli sviluppatori possono personalizzare facilmente i computer di sviluppo quando è necessario.
- Gli amministratori possono controllare i costi assicurandosi che gli sviluppatori non possano ottenere più macchine virtuali di quelle necessarie per lo sviluppo e che le macchine virtuali vengano arrestate quando non sono in uso.

Per altre informazioni, vedere [Usare Azure DevTest Labs per sviluppatori](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Usare DevTest Labs per gli ambienti di test

Azure DevTest Labs può essere usato per implementare molti scenari chiave. Uno dei più importanti prevede l'uso di DevTest Labs per ospitare computer per i tester. In questo scenario DevTest Labs offre i vantaggi seguenti:

- I tester possono provare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux tramite modelli ed elementi riutilizzabili.
- Possono anche aumentare i test di carico eseguendo il provisioning di più agenti di test.
- Gli amministratori possono controllare i costi assicurandosi che i tester non possano ottenere più macchine virtuali di quelle necessarie per i test e che le macchine virtuali vengano arrestate quando non sono in uso.

Per altre informazioni, vedere [Usare Azure DevTest Labs per sviluppatori](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Profili utente

Questo articolo descrive i diversi profili utente in Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietario dell'account del lab

L'amministratore IT delle risorse cloud aziendali che possiede la sottoscrizione di Azure in genere funge da proprietario dell'account del lab ed esegue le attività seguenti:

- Configura un account del lab per l'organizzazione.
- Gestisce e configura i criteri in tutti i lab.
- Concede le autorizzazioni agli utenti dell'organizzazione per la creazione di un lab all'interno dell'account del lab.

### <a name="lab-creator"></a>Creatore di lab

In genere un utente come un coordinatore o un responsabile dello sviluppo, un insegnante, un host hackathon o un trainer online crea i lab all'interno dell'account del lab. Il creatore di lab esegue le attività seguenti:

- Crea un lab.
- Crea le macchine virtuali nel lab.
- Installa il software appropriato nelle macchine virtuali.
- Specifica chi può accedere al lab.
- Fornisce agli utenti del lab un collegamento al lab.

### <a name="lab-user"></a>Utente del lab

Un utente del lab esegue le attività seguenti:

- Usa il collegamento di registrazione che l'utente del lab riceve da un creatore del lab per registrarsi nel lab.
- Si connette a una macchina virtuale nel lab e la usa per sviluppo, test o per eseguire compiti scolastici.

## <a name="next-steps"></a>Passaggi successivi

Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](classroom-labs/tutorial-setup-classroom-lab.md)
- [Configurare un lab](tutorial-create-custom-lab.md)
