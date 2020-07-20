---
title: Informazioni su Desktop virtuale Windows - Azure
description: Panoramica di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 473e3d52b1757faebd60c14966b425e9390a2685
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248612"
---
# <a name="what-is-windows-virtual-desktop"></a>Informazioni su Desktop virtuale Windows 

Desktop virtuale Windows è un servizio di virtualizzazione per desktop e app eseguito sul cloud.

Ecco cosa si può fare quando si esegue Desktop virtuale Windows in Azure:

* Configurare una distribuzione di Windows 10 multisessione che offre una versione completa Windows 10 con scalabilità
* Virtualizzare Microsoft 365 Apps for enterprise e ottimizzarlo per l'esecuzione in scenari virtuali multiutente
* Fornire ai desktop virtuali di Windows 7 aggiornamenti della sicurezza estesi gratuiti
* Portare i desktop e le app esistenti di Servizi Desktop remoto e Windows Server in qualsiasi computer
* Virtualizzare sia i desktop che le app
* Gestire i desktop e le app di Windows 10, Windows Server e Windows 7 con un'esperienza di gestione unificata

## <a name="introductory-video"></a>Video introduttivo

Per informazioni su Desktop virtuale Windows, sui motivi della sua unicità e sulle novità, guardare questo video:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Per altri video su Desktop virtuale Windows, vedere la [playlist](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

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

Si prevede l'aggiunta di supporto per i seguenti sistemi operativi, assicurarsi quindi di avere le [licenze appropriate](https://azure.microsoft.com/pricing/details/virtual-desktop/) per gli utenti in base al desktop e alle app che si prevede di distribuire:

|OS|Licenza richiesta|
|---|---|
|Windows 10 Enterprise multisessione o Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licenza CAL Servizi Desktop remoto con Software Assurance|

L'infrastruttura richiede quanto segue per supportare Desktop virtuale Windows:

* Un'istanza di [Azure Active Directory](/azure/active-directory/)
* Un'istanza di Windows Server Active Directory sincronizzata con Azure Active Directory. È possibile eseguire la configurazione con uno degli elementi seguenti:
  * Azure AD Connect (per le organizzazioni ibride)
  * Azure AD Domain Services (per le organizzazioni ibride o cloud)
* Una sottoscrizione di Azure contenente una rete virtuale che contiene o è connessa all'istanza di Windows Server Active Directory
  
Le macchine virtuali Azure che create per Desktop virtuale Windows devono essere:

* [Aggiunte a un dominio standard](../active-directory-domain-services/active-directory-ds-comparison.md) oppure [aggiunte ad AD ibrido](../active-directory/devices/hybrid-azuread-join-plan.md). Le macchine virtuali non possono essere aggiunte ad Azure AD.
* In esecuzione su una delle [immagini del sistema operativo supportate](#supported-virtual-machine-os-images) seguenti.

>[!NOTE]
>Se è necessaria una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/free/). Se si usa la versione di valutazione gratuita di Azure, è necessario usare Azure AD Domain Services per mantenere Windows Server Active Directory sincronizzato con Azure Active Directory.

Per un elenco degli URL che è necessario sbloccare per la corretta distribuzione di Desktop virtuale Windows, vedere l'[elenco di URL sicuri](safe-url-list.md).

Desktop virtuale Windows comprende i desktop e le app Windows distribuiti agli utenti e la soluzione di gestione ospitata come servizio in Azure da Microsoft. I desktop e le app possono essere distribuiti in macchine virtuali (VM) in qualsiasi area di Azure e la soluzione di gestione e i dati per queste macchine virtuali risiederanno negli Stati Uniti. Questo potrebbe comportare il trasferimento dei dati negli Stati Uniti.

Per prestazioni ottimali, assicurarsi che la rete soddisfi i requisiti seguenti:

* La latenza di round trip (RTT) dalla rete del client all'area di Azure in cui sono stati distribuiti i pool di host deve essere inferiore a 150 ms.
* Il flusso del traffico di rete può avvenire al di fuori dei confini del paese o dell'area geografica quando le macchine virtuali che ospitano i desktop e le app si collegano al servizio di gestione.
* Per ottimizzare le prestazioni della rete, si consiglia di collocare le macchine virtuali dell'host della sessione nella stessa area di Azure del servizio di gestione.

## <a name="supported-remote-desktop-clients"></a>Client Desktop remoto supportati

I seguenti client Desktop remoto supportano Desktop virtuale Windows:

* [Desktop di Windows](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (anteprima)](connect-android.md)

> [!IMPORTANT]
> Desktop virtuale Windows non supporta il client RADC (RemoteApp and Desktop Connections) o il client Connessione Desktop remoto.

> [!IMPORTANT]
> Desktop virtuale Windows non supporta attualmente il client Desktop remoto di Windows Store. Il supporto per questo client verrà aggiunto in una versione futura.

Per altre informazioni sugli URL che è necessario sbloccare per usare i client remoti, vedere l'[elenco di URL sicuri](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Immagini di sistema operativo supportate per le macchine virtuali

Desktop virtuale Windows supporta le immagini di sistema operativo x64 seguenti:

* Windows 10 Enterprise multisessione 1809 o versioni successive
* Windows 10 Enterprise 1809 o versioni successive
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Desktop virtuale Windows non supporta le immagini di sistemi operativi x86 (32 bit), Windows 10 Enterprise N o Windows 10 Enterprise KN. Windows 7 non supporta inoltre soluzioni di profili basati su VHD o VHDX ospitate in Archiviazione di Azure gestita a causa di una limitazione delle dimensioni dei settori.

Le opzioni di automazione e distribuzione disponibili variano a seconda del sistema operativo e della versione scelta, come illustrato nella tabella seguente: 

|Sistema operativo|Raccolta immagini di Azure|Distribuzione manuale della macchina virtuale|Integrazione del modello di Azure Resource Manager|Effettuare il provisioning dei pool di host in Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 multisessione, versione 1903|Sì|Sì|Sì|Sì|
|Windows 10 multisessione, versione 1809|Sì|Sì|No|No|
|Windows 10 Enterprise, versione 1903|Sì|Sì|Sì|Sì|
|Windows 10 Enterprise, versione 1809|Sì|Sì|No|No|
|Windows 7 Enterprise|Sì|Sì|No|No|
|Windows Server 2019|Sì|Sì|No|No|
|Windows Server 2016|Sì|Sì|Sì|Sì|
|Windows Server 2012 R2|Sì|Sì|No|No|

## <a name="next-steps"></a>Passaggi successivi

Se si usa la versione Autunno 2019 di Desktop virtuale Windows, è possibile iniziare con l'esercitazione in [Creare un tenant in Desktop virtuale Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Se si usa la versione Primavera 2020 di Desktop virtuale Windows, è necessario creare un pool di host. Passare all'esercitazione seguente per iniziare.

> [!div class="nextstepaction"]
> [Creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md)
