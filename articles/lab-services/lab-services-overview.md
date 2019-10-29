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
ms.openlocfilehash: 115320a8b4ee7afc6e420dcfa96612b91ea6d1a0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790776"
---
# <a name="an-introduction-to-azure-lab-services"></a>Introduzione ad Azure Lab Services
In Azure sono disponibili due servizi che consentono di configurare gli ambienti lab nel cloud. 

- **Azure DevTest Labs**: questo servizio consente di configurare rapidamente nel cloud un ambiente per il team, ad esempio un ambiente di sviluppo o un ambiente di test. Il proprietario di un lab crea un lab, esegue il provisioning delle macchine virtuali Windows o Linux, installa il software e gli strumenti necessari e li rende disponibili per gli utenti del lab. Gli utenti del lab si connettono alle macchine virtuali del lab e le usano per le attività giornaliere o per i progetti a breve termine. Quando gli utenti iniziano a usare le risorse nel lab, un amministratore del lab può analizzare il costo e l'utilizzo in più lab e impostare criteri omnicomprensivi per ottimizzare i costi del team o dell'organizzazione.
- **Azure Lab Services**: questo servizio consente di creare tipi di lab gestiti. Attualmente il lab per le classi è l'unico tipo di lab gestito che è supportato da Azure Lab Services. Il servizio controlla completamente la gestione dell'infrastruttura per un tipo di lab gestito, dall'attivazione delle macchine virtuali alla gestione degli errori, fino al ridimensionamento dell'infrastruttura. Dopo che un amministratore IT ha creato un account lab in Azure Lab Services, un insegnante può configurare rapidamente un lab per la propria classe, specificare il numero e il tipo di macchine virtuali necessarie per gli esercizi in classe e aggiungere utenti alla classe. Dopo la registrazione alla classe, l'utente può accedere alla macchina virtuale per eseguire gli esercizi per la classe.  

## <a name="key-capabilities"></a>Funzionalità principali

Questi servizi (Azure DevTest Labs e Azure Lab Services) supportano le caratteristiche e funzionalità principali seguenti:

- **Configurazione veloce e flessibile di un lab**. Tramite Azure Lab Services, i proprietari di lab possono configurare velocemente un lab per le loro esigenze. Il servizio offre la possibilità di controllare completamente la gestione dell'infrastruttura di Azure per i tipi di lab gestiti oppure di consentire ai proprietari di lab di gestire autonomamente e di personalizzare l'infrastruttura nella loro sottoscrizione. Il servizio offre scalabilità e resilienza dell'infrastruttura predefinite per i lab gestiti che il servizio gestisce automaticamente.
- **Esperienza semplificata per gli utenti dei lab**. In un tipo di lab gestito, ad esempio un lab per le classi, gli utenti del lab possono eseguire la registrazione a un lab con un codice di registrazione e accedere al lab in qualsiasi momento per usare le relative risorse. In un lab creato in DevTest Labs il proprietario del lab può concedere agli utenti del lab autorizzazioni per creare e accedere alle macchine virtuali, gestire e riutilizzare i dischi dati e configurare i segreti riutilizzabili.  
- **Analisi e ottimizzazione dei costi**. Il proprietario del lab può impostare pianificazioni del lab per arrestare e avviare automaticamente le macchine virtuali. Il proprietario del lab può impostare una pianificazione per specificare le fasce orarie in cui le macchine virtuali del lab sono accessibili agli utenti, impostare i criteri di utilizzo per ciascun utente o lab per ottimizzare i costi, nonché analizzare le tendenze di utilizzo e attività in un lab. Per i tipi di lab gestiti, ad esempio i lab per le classi, è attualmente disponibile un subset ridotto di opzioni per l'analisi e l'ottimizzazione dei costi.
- **Sicurezza incorporata**. Il proprietario di un lab può configurare una subnet e una rete virtuale privata per il lab e abilitare un indirizzo IP pubblico condiviso. Gli utenti del lab possono accedere in modo sicuro alle risorse tramite la rete virtuale configurata con ExpressRoute o con VPN da sito a sito. (attualmente disponibile solo in DevTest Labs)
- **Integrazione con gli strumenti e i flussi di lavoro**. Azure Lab Services consente di integrare i lab nei sistemi di gestione e nel sito Web dell'organizzazione. È possibile eseguire automaticamente il provisioning di ambienti dagli strumenti di integrazione continua e distribuzione continua (CI/CD). (attualmente disponibile solo in DevTest Labs)

> [!NOTE]
> Attualmente Azure Lab Services supporta solo le macchine virtuali create da immagini del Marketplace di Azure. Se si vogliono usare immagini personalizzate o creare altre risorse PaaS in un ambiente lab, usare DevTest Labs. Per altre informazioni, vedere [Creare un'immagine personalizzata in DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) e [Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Scenari

Ecco alcuni degli scenari supportati da Azure DevTest Labs e Azure Lab Services:

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

## <a name="types-of-labs"></a>Tipi di lab
È possibile creare due tipi di lab: **tipi di lab gestiti** con Azure Lab Services e **lab** con Azure Lab Services. Se si vuole semplicemente indicare le caratteristiche richieste per il lab e consentire al servizio di configurare e gestire l'infrastruttura necessaria per il lab, scegliere uno dei **tipi di lab gestiti**. Attualmente il **lab per le classi** è l'unico tipo di lab gestito che è possibile creare con Azure Lab Services. Se si vuole gestire la propria infrastruttura, creare un lab tramite **Azure DevTest Labs**.

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


## <a name="devtest-labs"></a>DevTest Labs
In alcuni scenari è possibile gestire autonomamente l'infrastruttura e la configurazione, all'interno della propria sottoscrizione. A tale scopo, creare un lab in Azure DevTest Labs nel portale di Azure. Per questi lab non è necessario creare un account del lab. Tali lab non vengono visualizzati nell'account del lab, che è destinato ai tipi di lab gestiti.  

Ecco alcuni **casi d'uso per DevTest Labs**: 

- Eseguire rapidamente il provisioning di un lab di macchine virtuali per ospitare un hackathon o una sessione pratica durante una conferenza. Al termine dell'operazione, eliminare il lab con un solo clic. 
- Creare un pool di macchine virtuali configurate con l'applicazione e consentire al team di usare facilmente una macchina virtuale per i bug bash.  
- Garantire agli sviluppatori macchine virtuali configurate con tutti gli strumenti necessari. Pianificare l'avvio e l'arresto automatico per ridurre i costi. 
- Creare ripetutamente un lab di computer di test come parte della distribuzione. Testare i bit più recenti e pulire il computer di test al termine dell'operazione. 
- Impostare una serie di macchine virtuali configurate in modo diverso e più agenti di test per i test delle prestazioni e della scalabilità. 
- Offrire sessioni di training ai clienti usando un lab configurato con la versione più recente del prodotto. Assegnare a ogni cliente un numero limitato di ore per l'uso del lab. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Tipi di lab gestiti contro DevTest Labs
La tabella seguente confronta due tipi di lab supportati da Azure Lab Services: 

| Funzionalità | Tipi di lab gestiti | DevTest Labs |
| -------- | ----------------- | ---------- |
| Gestione dell'infrastruttura di Azure nel lab. |  Gestita automaticamente dal servizio | Gestita autonomamente  |
| Resilienza predefinita per i problemi dell'infrastruttura | Gestita automaticamente dal servizio | Gestita autonomamente  |
| Gestione sottoscrizioni | Il servizio gestisce l'allocazione delle risorse all'interno delle sottoscrizioni di Microsoft che supportano il servizio. La scalabilità viene gestita automaticamente dal servizio. | Gestita autonomamente nella propria sottoscrizione di Azure. Nessuna scalabilità automatica delle sottoscrizioni. |
| Distribuzione di Azure Resource Manager nel lab | Non disponibile | Disponibile |

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti: 

- [Informazioni sui lab per le classi](./classroom-labs/classroom-labs-overview.md)
- [Informazioni su DevTest Labs](devtest-lab-overview.md)
