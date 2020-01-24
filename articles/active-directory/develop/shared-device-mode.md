---
title: Modalità dispositivo condiviso per dispositivi Android | Azure
description: Informazioni sulla modalità dispositivo condiviso, che consente ai prima riga Worker di condividere un dispositivo Android
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701468"
---
# <a name="shared-device-mode-for-android-devices"></a>Modalità dispositivo condiviso per dispositivi Android

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I prima riga Worker, ad esempio i Retail Associates, i membri del team di volo e i lavoratori dei servizi Field, usano spesso un dispositivo mobile condiviso per svolgere il proprio lavoro. Questo diventa problematico quando iniziano a condividere le password o i numeri PIN per accedere ai dati aziendali e ai clienti sul dispositivo condiviso.

La modalità dispositivo condiviso consente di configurare un dispositivo Android in modo che possa essere condiviso facilmente da più dipendenti. I dipendenti possono accedere rapidamente alle informazioni dei clienti. Al termine del turno o dell'attività, è possibile disconnettersi dal dispositivo e sarà immediatamente pronto per l'uso da parte del dipendente successivo.

La modalità dispositivo condiviso fornisce anche la gestione di Microsoft Identity supportata dal dispositivo.

Per creare un'app in modalità dispositivo condivisa, gli sviluppatori e gli amministratori di dispositivi cloud interagiscono tra loro:

- Gli sviluppatori scrivono un'app con un solo account (le app con più account non sono supportate in modalità dispositivo condiviso), aggiungono `"shared_device_mode_supported": true` alla configurazione dell'app e scrivono il codice per gestire elementi come la disconnessione dei dispositivi condivisi.
- Gli amministratori di dispositivi preparano la condivisione del dispositivo mediante l'installazione dell'app Authenticator e l'impostazione del dispositivo sulla modalità condivisa tramite l'app Authenticator. Solo gli utenti che appartengono al ruolo di [amministratore del dispositivo cloud](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) possono impostare un dispositivo in modalità condivisa usando l' [app Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). È possibile configurare l'appartenenza dei ruoli aziendali nel portale di Azure tramite: **Azure Active Directory** > **ruoli e amministratori** > amministratore del **dispositivo cloud**.

 Questo articolo è incentrato principalmente sugli elementi da considerare per gli sviluppatori.

## <a name="single-vs-multiple-account-applications"></a>Applicazioni a singolo account e multi-account

Le applicazioni scritte utilizzando Microsoft Authentication Library SDK (MSAL) possono gestire un singolo account o più account. Per informazioni dettagliate, vedere [modalità account singolo o modalità account multipli](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Le funzionalità della piattaforma Microsoft Identity disponibili per l'app variano a seconda del fatto che l'applicazione sia in esecuzione in modalità account singolo o in modalità account multipli.

Le **app in modalità dispositivo condiviso funzionano solo in modalità account singolo**.

> [!IMPORTANT]
> Le applicazioni che supportano solo la modalità account multipli non possono essere eseguite in un dispositivo condiviso. Se un dipendente carica un'app che non supporta la modalità account singolo, non verrà eseguita sul dispositivo condiviso.
>
> Le app scritte prima dell'SDK di MSAL sono state rilasciate in modalità multi-account e devono essere aggiornate per supportare la modalità account singolo prima di poter essere eseguite in un dispositivo in modalità condivisa.

**Supporto sia per account singolo che per più account**

L'app può essere compilata per supportare l'esecuzione su dispositivi personali e dispositivi condivisi. Se l'app supporta attualmente più account e si vuole supportare la modalità dispositivo condiviso, aggiungere il supporto per la modalità account singolo.

È anche possibile che l'app modifichi il proprio comportamento a seconda del tipo di dispositivo in cui è in esecuzione. Utilizzare `ISingleAccountPublicClientApplication.isSharedDevice()` per determinare quando eseguire in modalità account singolo.

Sono disponibili due interfacce diverse che rappresentano il tipo di dispositivo in cui si trova l'applicazione. Quando si richiede un'istanza dell'applicazione dalla factory applicativa di MSAL, l'oggetto applicazione corretto viene fornito automaticamente.

Il modello a oggetti seguente illustra il tipo di oggetto che è possibile ricevere e il significato nel contesto di un dispositivo condiviso:

![modello di ereditarietà di applicazioni client pubbliche](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Quando si ottiene l'oggetto `PublicClientApplication`, è necessario eseguire un controllo del tipo ed eseguire il cast all'interfaccia appropriata. Il codice seguente consente di controllare la modalità più account o l'account singolo e di eseguire il cast dell'oggetto applicazione in modo appropriato:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Le differenze seguenti si applicano a seconda che l'app sia in esecuzione su un dispositivo condiviso o personale:

|  | Dispositivo in modalità condivisa  | Dispositivo personale |
|---------|---------|---------|
| **Accounts**     | Account singolo | Più account |
| **Accesso** | Globale | Globale |
| **Disconnessione** | Globale | Ogni applicazione può controllare se la disconnessione è locale per l'app o per la famiglia di applicazioni. |
| **Tipi di account supportati** | Solo account di lavoro | Account personali e di lavoro supportati  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Perché è consigliabile supportare solo la modalità account singolo

Se si sta scrivendo un'app che verrà usata solo per i prima riga Worker usando un dispositivo condiviso, è consigliabile scrivere l'applicazione in modo da supportare solo la modalità account singolo. Questo vale per la maggior parte delle applicazioni incentrate su attività, come le app medicali, le app di fatturazione e quasi tutte le app line-of-business. Supportare solo la modalità account singolo semplifica lo sviluppo perché non è necessario implementare le funzionalità aggiuntive che fanno parte di app con più account.

## <a name="what-happens-when-the-device-mode-changes"></a>Cosa accade quando cambia la modalità del dispositivo

Se l'applicazione è in esecuzione in modalità a più account e un amministratore inserisce il dispositivo in modalità dispositivo condiviso, tutti gli account del dispositivo vengono cancellati dall'applicazione e l'applicazione passa alla modalità account singolo.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Disconnessione del dispositivo condiviso e ciclo di vita dell'app complessiva

Quando un utente si disconnette, è necessario intervenire per proteggere la privacy e i dati dell'utente. Se, ad esempio, si sta creando un'app di registrazione medica, è necessario assicurarsi che, quando l'utente si disconnette, i record dei pazienti visualizzati in precedenza vengano cancellati. L'applicazione deve essere preparata e controllare ogni volta che entra in primo piano.

Quando l'app usa MSAL per disconnettersi dall'utente in un'app in esecuzione su un dispositivo in modalità condivisa, l'account connesso e i token memorizzati nella cache vengono rimossi sia dall'app che dal dispositivo.

Il diagramma seguente mostra il ciclo di vita dell'app e gli eventi comuni che possono verificarsi durante l'esecuzione dell'app. Il diagramma riguarda il momento in cui un'attività viene avviata, l'accesso e la disconnessione di un account e il modo in cui gli eventi come la sospensione, la ripresa e l'arresto dell'attività si adattano.

![Ciclo di vita dell'app per dispositivi condivisi](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione [usare la modalità dispositivo condiviso nell'applicazione Android](tutorial-v2-shared-device-mode.md) che Mostra come eseguire un'app prima riga worker in un dispositivo Android in modalità condivisa.
