---
title: Informazioni sull'anteprima di Desktop virtuale Windows  - Azure
description: Panoramica dell'anteprima di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 58fb1d73509ab52551bead4526dfb47588cf1ec6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004585"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Informazioni sull'anteprima di Desktop virtuale Windows 

Ora disponibile in anteprima pubblica, l'anteprima di Desktop virtuale Windows è un servizio di virtualizzazione per desktop e app che viene eseguito sul cloud.

Di seguito è riportato cosa si può fare quando si esegue Desktop virtuale Windows in Azure:

* Configurare una distribuzione di Windows 10 multisessione che offre una versione completa Windows 10 con scalabilità
* Virtualizzare Office 365 ProPlus e ottimizzarlo per l'esecuzione in scenari virtuali multiutente
* Fornire ai desktop virtuali di Windows 7 aggiornamenti della sicurezza estesi gratuiti
* Portare i desktop e le app esistenti di Servizi Desktop remoto e Windows Server in qualsiasi computer
* Virtualizzare sia i desktop che le app
* Gestire i desktop e le app di Windows 10, Windows Server e Windows 7 con un'esperienza di gestione unificata

## <a name="key-capabilities"></a>Funzionalità principali

Con Desktop virtuale Windows è possibile configurare un ambiente scalabile e flessibile:

* Creare un ambiente di virtualizzazione desktop completo nella sottoscrizione di Azure senza dover eseguire server gateway aggiuntivi.
* Pubblicare tutti i pool di host necessari per gestire i diversi carichi di lavoro.
* Portare la propria immagine per i carichi di lavoro di produzione o sceglierne una di prova dalla Raccolta di Azure.
* Ridurre i costi grazie alle risorse in pool multisessione. Con la nuova funzionalità multisessione di Windows 10 Enterprise esclusiva per Desktop virtuale Windows e il ruolo Host sessione Desktop remoto in Windows Server, è possibile ridurre notevolmente il numero di macchine virtuali e il sovraccarico del sistema operativo, pur fornendo le stesse risorse agli utenti.
* Fornire la proprietà individuale tramite desktop personali (persistenti).

È possibile distribuire e gestire desktop virtuali:

* Usare le interfacce PowerShell e REST di Desktop virtuale Windows per configurare i pool di host, creare gruppi di app, assegnare utenti e pubblicare risorse.
* Pubblicare un desktop completo o singole app remote da un singolo pool di host, creare gruppi di app individuali per diversi set di utenti o persino assegnare gli utenti a più gruppi di app per ridurre il numero di immagini.
* Quando si gestisce l'ambiente, usare l'accesso delegato predefinito per assegnare i ruoli e raccogliere i dati di diagnostica per comprendere i vari errori di configurazione o gli errori degli utenti.
* Usare il nuovo servizio di diagnostica per risolvere gli errori.
* Gestire solo l'immagine e le macchine virtuali, non l'infrastruttura. Non è necessario gestire personalmente i ruoli di Desktop remoto come avviene con Servizi Desktop remoto, ma solo le macchine virtuali nella sottoscrizione di Azure.

È anche possibile assegnare e collegare gli utenti ai desktop virtuali:

* Dopo l'assegnazione gli utenti possono avviare qualsiasi client di Desktop virtuale Windows per collegare gli utenti ai relativi desktop e applicazioni Windows pubblicati. È possibile connettersi da qualsiasi dispositivo tramite un'applicazione nativa nel dispositivo o il client Web HTML5 di Desktop virtuale Windows.
* Consentire agli utenti di collegarsi al servizio in modo sicuro tramite connessioni invertite, in modo da non lasciare mai aperte le porte in ingresso.

## <a name="requirements"></a>Requisiti

Sono necessari alcuni requisiti per configurare Desktop virtuale Windows e collegare correttamente gli utenti ai relativi desktop e applicazioni Windows.

Desktop virtuale Windows supporta attualmente solo i due sistemi operativi seguenti:
- Windows 10 Enterprise multisessione
- Windows Server 2016

Si prevede l'aggiunta di supporto per i seguenti sistemi operativi, assicurarsi quindi di avere le [licenze appropriate](https://azure.microsoft.com/pricing/details/virtual-desktop/) per gli utenti in base al desktop e alle app che si prevede di distribuire:

|OS|Licenza richiesta|
|---|---|

|Windows 10 Enterprise multisessione o Windows 10 Enterprise|Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5| |Windows 7 Enterprise |Microsoft E3, E5, A3, A5, Business<br>Windows E3, E5, A3, A5| |Windows Server 2012 R2, 2016, 2019|Licenza CAL Servizi Desktop remoto con Software Assurance|

L'infrastruttura richiede quanto segue per supportare Desktop virtuale Windows:

* Un'istanza di [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Un'istanza di Windows Server Active Directory sincronizzata con Azure Active Directory. Ciò può essere abilitato tramite:
  * Azure AD Connect
  * Servizi di dominio Azure Active Directory
* Una sottoscrizione di Azure contenente una rete virtuale che contiene o è collegata all'istanza di Windows Server Active Directory
  
Le macchine virtuali Azure che create per Desktop virtuale Windows devono essere:

* [Aggiunte a un dominio standard](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) oppure [aggiunte ad AD ibrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Le macchine virtuali non possono essere aggiunte ad Azure AD.
* Eseguire una delle immagini del sistema operativo supportate seguenti:
  * Windows 10 Enterprise multisessione
  * Windows Server 2016

>[!NOTE]
>Se è necessaria una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/free/). Se si usa la versione di valutazione gratuita di Azure, è necessario usare Azure AD Domain Services per mantenere Windows Server Active Directory sincronizzato con Azure Active Directory.

Desktop virtuale Windows comprende i desktop e le app Windows distribuiti agli utenti e la soluzione di gestione ospitata come servizio in Azure da Microsoft. Durante l'anteprima pubblica, i desktop e le app possono essere distribuiti in macchine virtuali (VM) in qualsiasi area di Azure e la soluzione di gestione e i dati per queste macchine virtuali risiederanno negli Stati Uniti (area Stati Uniti orientali 2). Questo potrebbe comportare il trasferimento dei dati negli Stati Uniti mentre si testa il servizio durante l'anteprima pubblica. Si inizierà a scalare orizzontalmente la soluzione di gestione e la localizzazione dei dati in tutte le aree di Azure a partire dalla disponibilità generale.

Per prestazioni ottimali, assicurarsi che la rete soddisfi i requisiti seguenti:

* La latenza di round trip (RTT) dalla rete del client all'area di Azure in cui sono stati distribuiti i pool di host deve essere inferiore a 150 ms.
* Il flusso del traffico di rete può avvenire al di fuori dei confini nazionali quando le macchine virtuali che ospitano i desktop e le app si collegano al servizio di gestione.
* Per ottimizzare le prestazioni della rete, si consiglia di collocare le macchine virtuali dell'host della sessione nella stessa area di Azure del servizio di gestione.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community. Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, è necessario creare un tenant. Per altre informazioni su come creare un tenant, continuare con l'esercitazione sulla creazione di un tenant.

> [!div class="nextstepaction"]
> [Creare un tenant nell'anteprima di Desktop virtuale Windows](tenant-setup-azure-active-directory.md)
