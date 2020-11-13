---
title: Panoramica della modalità dispositivo condiviso
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sulla modalità dispositivo condiviso per abilitare la condivisione dei dispositivi per i prima riga Worker.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3cd7074467332f89d4d6c60830be34f4e2a638c1
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562084"
---
# <a name="overview-of-shared-device-mode"></a>Panoramica della modalità dispositivo condiviso

La modalità dispositivo condiviso è una funzionalità di Azure Active Directory che consente di compilare applicazioni che supportano prima riga Worker e abilitano la modalità dispositivo condiviso nei dispositivi distribuiti.

>[!IMPORTANT]
> Questa funzionalità [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-firstline-workers"></a>Che cosa sono i prima riga Worker?

I prima riga worker sono dipendenti al dettaglio, agenti di manutenzione e campo, personale medico e altri utenti che non si trovano davanti a un computer o usano la posta elettronica aziendale per la collaborazione. Le sezioni seguenti introducono gli aspetti e le esigenze del supporto di prima riga Worker, seguiti da un'introduzione alle funzionalità fornite da Microsoft che consentono all'applicazione di essere utilizzate dai prima riga Worker di un'organizzazione.

### <a name="challenges-of-supporting-firstline-workers"></a>Problemi relativi al supporto di prima riga Worker

L'abilitazione dei flussi di lavoro del prima riga Worker include problemi che in genere non vengono presentati dagli Information Worker Tali problemi possono includere una velocità di rotazione elevata e una minore familiarità con gli strumenti di produttività principali di un'organizzazione. Per potenziare i ruoli di lavoro prima riga, le organizzazioni adottano strategie differenti. Alcune stanno adottando una strategia Bring-Your-Own-Device (BYOD) in cui i dipendenti usano app aziendali sul proprio telefono personale, mentre altri forniscono ai propri dipendenti dispositivi condivisi come iPad o tablet Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Supporto di più utenti nei dispositivi progettati per un utente

Poiché i dispositivi mobili che eseguono iOS o Android sono stati progettati per utenti singoli, la maggior parte delle applicazioni ottimizza la loro esperienza per l'uso da parte di un singolo utente. Parte di questa esperienza ottimizzata indica l'abilitazione Single Sign-On tra le applicazioni e l'accesso degli utenti al dispositivo. Quando un utente rimuove il proprio account da un'applicazione, l'app in genere non considera un evento correlato alla sicurezza. Molte app conservano anche le credenziali di un utente per l'accesso rapido. È possibile che l'utente abbia già sperimentato questa operazione quando è stata eliminata un'applicazione dal dispositivo mobile e quindi reinstallata, ma solo per scoprire che è ancora stato eseguito l'accesso.

### <a name="global-sign-in-and-sign-out-sso"></a>Accesso globale e disconnessione (SSO)

Per consentire ai dipendenti di un'organizzazione di usare le proprie app in un pool di dispositivi condivisi da tali dipendenti, gli sviluppatori devono abilitare l'esperienza contraria. I dipendenti devono poter selezionare un dispositivo dal pool ed eseguire un singolo gesto per "renderlo" per la durata del turno. Al termine del turno, dovrebbero essere in grado di eseguire un altro gesto per disconnettersi globalmente sul dispositivo, con tutte le informazioni personali e aziendali rimosse, in modo che possano restituirle al pool di dispositivi. Inoltre, se un dipendente dimentica di disconnettersi, il dispositivo deve essere disconnesso automaticamente alla fine del turno e/o dopo un periodo di inattività.

Azure Active Directory Abilita questi scenari con una funzionalità denominata **modalità dispositivo condiviso**.

## <a name="introducing-shared-device-mode"></a>Introduzione alla modalità dispositivo condiviso

Come indicato in precedenza, la modalità dispositivo condiviso è una funzionalità di Azure Active Directory che consente di:

* Compilazione di applicazioni che supportano prima riga Worker
* Distribuire i dispositivi ai prima riga Worker e attivare la modalità dispositivo condiviso

### <a name="build-applications-that-support-firstline-workers"></a>Compilazione di applicazioni che supportano prima riga Worker

È possibile supportare prima riga Worker nelle applicazioni usando Microsoft Authentication Library (MSAL) e [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md) per abilitare uno stato del dispositivo denominato *modalità dispositivo condiviso*. Quando un dispositivo è in modalità dispositivo condiviso, Microsoft fornisce all'applicazione informazioni che consentono di modificare il comportamento in base allo stato dell'utente nel dispositivo, proteggendo i dati dell'utente.

Le funzionalità supportate sono:

* **Accedere a un dispositivo utente a livello di dispositivo** tramite qualsiasi applicazione supportata.
* **Disconnettersi da un dispositivo utente a** qualsiasi applicazione supportata.
* **Eseguire una query sullo stato del dispositivo** per determinare se l'applicazione si trova in un dispositivo in modalità dispositivo condiviso.
* **Eseguire una query sullo stato del dispositivo dell'utente** sul dispositivo per determinare se sono state apportate modifiche dall'ultima volta in cui è stata usata l'applicazione.

Il supporto della modalità dispositivo condiviso deve essere considerato un miglioramento della sicurezza e un aggiornamento delle funzionalità per l'applicazione e può contribuire a migliorare l'adozione in ambienti altamente regolamentati come Healthcare e finanza.

Gli utenti dipendono dall'utente per assicurarsi che i dati non vengano divulgati a un altro utente. Condividi modalità dispositivo fornisce segnali utili per indicare all'applicazione che è stata eseguita una modifica che è necessario gestire. L'applicazione è responsabile del controllo dello stato dell'utente sul dispositivo ogni volta che viene usata l'app, cancellando i dati dell'utente precedente. Questo include se viene ricaricato da background in multitasking. In caso di modifica dell'utente, è necessario assicurarsi che i dati dell'utente precedente siano deselezionati e che i dati memorizzati nella cache visualizzati nell'applicazione vengano rimossi. È consigliabile eseguire sempre un processo di revisione della sicurezza completo dopo aver aggiunto la funzionalità della modalità dispositivo condiviso all'app.

Per informazioni dettagliate su come modificare le applicazioni per supportare la modalità dispositivo condiviso, vedere la sezione [passaggi successivi](#next-steps) alla fine di questo articolo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Distribuire i dispositivi ai prima riga Worker e attivare la modalità dispositivo condiviso

Quando le applicazioni supportano la modalità dispositivo condiviso e includono i dati e le modifiche di sicurezza necessari, è possibile annunciarli come utilizzabili dai prima riga Worker.

Gli amministratori di dispositivi di un'organizzazione sono in grado di distribuire i dispositivi e le applicazioni nei propri negozi e luoghi di lavoro tramite una soluzione di gestione di dispositivi mobili (MDM) come Microsoft Intune. Parte del processo di provisioning sta contrassegnando il dispositivo come *dispositivo condiviso*. Gli amministratori configurano la modalità dispositivo condiviso distribuendo l' [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e impostando la modalità dispositivo condiviso tramite i parametri di configurazione. Dopo aver eseguito questi passaggi, tutte le applicazioni che supportano la modalità dispositivo condiviso utilizzeranno l'applicazione Microsoft Authenticator per gestire il proprio stato utente e fornire funzionalità di sicurezza per il dispositivo e l'organizzazione.

## <a name="next-steps"></a>Passaggi successivi

Sono supportate le piattaforme iOS e Android per la modalità dispositivo condiviso. Per iniziare a supportare i prima riga Worker nelle applicazioni, vedere la documentazione seguente.

* [Supporto della modalità dispositivo condiviso per iOS](msal-ios-shared-devices.md)
* [Supporto della modalità dispositivo condiviso per Android](msal-android-shared-devices.md)
