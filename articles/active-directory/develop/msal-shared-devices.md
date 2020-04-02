---
title: Panoramica della modalità dispositivo condiviso
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sulla modalità dispositivo condiviso per abilitare la condivisione dei dispositivi per i ruoli di lavoro Firstline.Learn about shared device mode to enable device sharing for your Firstline Workers.
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
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550240"
---
# <a name="overview-of-shared-device-mode"></a>Panoramica della modalità dispositivo condiviso

La modalità dispositivo condiviso è una funzionalità di Azure Active Directory che consente di creare applicazioni che supportano I ruoli di lavoro Firstline e abilitare la modalità dispositivo condiviso nei dispositivi distribuiti.

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>Cosa sono i Firstline Worker?

Firstline Workers sono dipendenti al dettaglio, agenti di manutenzione e sul campo, personale medico e altri utenti che non sitrovano davanti a un computer o utilizzano la posta elettronica aziendale per la collaborazione. Nelle sezioni seguenti vengono presentati gli aspetti e le sfide del supporto di Firstline Worker, seguita da un'introduzione alle funzionalità fornite da Microsoft che consentono all'applicazione per l'utilizzo da parte di Firstline Workers di un'organizzazione.

### <a name="challenges-of-supporting-firstline-workers"></a>Sfide del supporto ai lavoratori firstline

L'abilitazione dei flussi di lavoro Firstline Worker include sfide solitamente non presentate dagli information worker tipici. Tali sfide possono includere un alto tasso di turnover e una minore familiarità con gli strumenti di produttività di base di un'organizzazione. Per potenziare i loro Firstline Workers, le organizzazioni stanno adottando strategie diverse. Alcuni stanno adottando una strategia BYOD (Bring Your Own Device) in cui i loro dipendenti utilizzano app aziendali sul proprio telefono personale, mentre altri forniscono ai propri dipendenti dispositivi condivisi come iPad o tablet Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Supporto di più utenti su dispositivi progettati per un utenteSupporting multiple users on devices designed for one user

Poiché i dispositivi mobili che eseguono iOS o Android sono stati progettati per singoli utenti, la maggior parte delle applicazioni ottimizza la propria esperienza per l'utilizzo da parte di un singolo utente. Parte di questa esperienza ottimizzata significa abilitare l'accesso Single Sign-On tra le applicazioni e mantenere gli utenti connessi al proprio dispositivo. Quando un utente rimuove il proprio account da un'applicazione, l'app in genere non lo considera un evento correlato alla sicurezza. Molte app mantengono anche le credenziali di un utente per l'accesso rapido. Potresti anche averlo sperimentato tu stesso quando hai eliminato un'applicazione dal tuo dispositivo mobile e poi reinstallata, solo per scoprire che hai ancora effettuato l'accesso.

### <a name="global-sign-in-and-sign-out-sso"></a>Accesso e disconnessione globali (SSO)

Per consentire ai dipendenti di un'organizzazione di usare le proprie app in un pool di dispositivi condivisi da tali dipendenti, gli sviluppatori devono abilitare l'esperienza opposta. I dipendenti dovrebbero essere in grado di scegliere un dispositivo dalla piscina ed eseguire un singolo gesto per "farlo loro" per la durata del loro turno. Alla fine del loro turno, dovrebbero essere in grado di eseguire un altro gesto per disconnettersi globalmente sul dispositivo, con tutte le informazioni personali e aziendali rimosse in modo da poterle restituire al pool di dispositivi. Inoltre, se un dipendente dimentica di disconnettersi, il dispositivo deve essere automaticamente disconnesso alla fine del turno e/o dopo un periodo di inattività.

Azure Active Directory abilita questi scenari con una funzionalità denominata **modalità dispositivo condiviso.**

## <a name="introducing-shared-device-mode"></a>Introduzione alla modalità dispositivo condiviso

Come accennato in precedenza, la modalità dispositivo condiviso è una funzionalità di Azure Active Directory che consente di:As mentioned, shared device mode is a feature of Azure Active Directory that enables you to:

* Creare applicazioni che supportano Firstline Worker
* Distribuire i dispositivi in Firstline Worker e attivare la modalità dispositivo condivisoDeploy devices to Firstline Workers and turn on shared device mode

### <a name="build-applications-that-support-firstline-workers"></a>Creare applicazioni che supportano Firstline Worker

È possibile supportare Firstline Workers nelle applicazioni utilizzando Microsoft Authentication Library (MSAL) e [l'app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) per abilitare uno stato del dispositivo denominato *modalità dispositivo condiviso*. Quando un dispositivo è in modalità dispositivo condiviso, Microsoft fornisce all'applicazione informazioni che consentono di modificarne il comportamento in base allo stato dell'utente nel dispositivo, proteggendo i dati utente.

Le funzionalità supportate sono:

* **Accedere a un dispositivo utente** a livello di dispositivo tramite qualsiasi applicazione supportata.
* **Disconnettersi da un dispositivo utente** tramite qualsiasi applicazione supportata.
* **Eseguire una query sullo stato del dispositivo** per determinare se l'applicazione si trova in un dispositivo in modalità dispositivo condiviso.
* **Eseguire una query sullo stato del dispositivo dell'utente** per determinare se è stato modificato qualcosa dall'ultimo utilizzo dell'applicazione.

Il supporto della modalità per dispositivi condivisi deve essere considerato un miglioramento della sicurezza e un aggiornamento delle funzionalità per l'applicazione e può contribuire ad aumentarne l'adozione in ambienti altamente regolamentati come l'assistenza sanitaria e la finanza.

Gli utenti dipendono da te per garantire che i loro dati non siano trapelati a un altro utente. La modalità dispositivo di condivisione fornisce segnali utili per indicare all'applicazione che si è verificata una modifica da gestire. L'applicazione è responsabile del controllo dello stato dell'utente sul dispositivo ogni volta che viene utilizzata l'app, cancellando i dati dell'utente precedente. Ciò include se viene ricaricato dallo sfondo in multi-tasking. In caso di modifica dell'utente, è necessario assicurarsi che i dati dell'utente precedente vengano cancellati e che tutti i dati memorizzati nella cache visualizzati nell'applicazione vengano rimossi. Ti consigliamo di eseguire sempre un processo di revisione della sicurezza approfondito dopo aver aggiunto la funzionalità della modalità dispositivo condiviso alla tua app.

Per informazioni dettagliate su come modificare le applicazioni per supportare la modalità di dispositivo condiviso, vedere la sezione [Passaggi successivi](#next-steps) alla fine di questo articolo.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Distribuire i dispositivi in Firstline Worker e attivare la modalità dispositivo condivisoDeploy devices to Firstline Workers and turn on shared device mode

Una volta che le applicazioni supportano la modalità dispositivo condiviso e includono i dati necessari e le modifiche di sicurezza, è possibile annunciarli come utilizzabili da Firstline Workers.

Gli amministratori dei dispositivi di un'organizzazione sono in grado di distribuire i propri dispositivi e le applicazioni negli archivi e negli ambienti di lavoro tramite una soluzione di gestione dei dispositivi mobili (MDM) come Microsoft Intune.An organization's device administrators are able to deploy their devices and your applications to their stores and workplaces through a mobile device management (MDM) solution like Microsoft Intune. Parte del processo di provisioning contrassegna il dispositivo come *dispositivo condiviso.* Gli amministratori configurano la modalità dispositivo condivisa distribuendo [l'app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e impostando la modalità del dispositivo condiviso tramite i parametri di configurazione. Dopo aver eseguito questi passaggi, tutte le applicazioni che supportano la modalità dispositivo condiviso utilizzeranno l'applicazione Microsoft Authenticator per gestire lo stato utente e fornire funzionalità di sicurezza per il dispositivo e l'organizzazione.

## <a name="next-steps"></a>Passaggi successivi

Supportiamo le piattaforme iOS e Android per la modalità dispositivo condiviso. Consultare la documentazione riportata di seguito per la piattaforma per iniziare a supportare Firstline Workers nelle applicazioni.

* [Supporto della modalità dispositivo condiviso per iOSSupporting shared device mode for iOS](msal-ios-shared-devices.md)
* [Supporto della modalità dispositivo condiviso per AndroidSupporting shared device mode for Android](msal-android-shared-devices.md)
