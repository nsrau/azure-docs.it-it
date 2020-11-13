---
title: Modalità dispositivo condiviso per dispositivi Android
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come abilitare la modalità dispositivo condiviso per consentire ai prima riga Worker di condividere un dispositivo Android
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: fc32b4f583aea2fa9a34ab8b235f3f99fe4def9d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562169"
---
# <a name="shared-device-mode-for-android-devices"></a>Modalità dispositivo condiviso per dispositivi Android

>[!IMPORTANT]
> Questa funzionalità [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

I ruoli di lavoro di prima riga, ad esempio i Retail Associates, i membri del team di volo e i lavoratori dei servizi Field spesso usano un dispositivo mobile condiviso per svolgere il proprio lavoro Questo diventa problematico quando iniziano a condividere le password o i numeri PIN per accedere ai dati aziendali e ai clienti sul dispositivo condiviso.

La modalità dispositivo condiviso consente di configurare un dispositivo Android in modo che possa essere condiviso facilmente da più dipendenti. I dipendenti possono accedere rapidamente alle informazioni dei clienti. Al termine del turno o dell'attività, è possibile disconnettersi dal dispositivo e sarà immediatamente pronto per l'uso da parte del dipendente successivo.

La modalità dispositivo condiviso fornisce anche la gestione di Microsoft Identity supportata dal dispositivo.

Per creare un'app in modalità dispositivo condivisa, gli sviluppatori e gli amministratori di dispositivi cloud interagiscono tra loro:

- Gli sviluppatori scrivono un'app con un solo account (le app con più account non sono supportate in modalità dispositivo condiviso), aggiungono `"shared_device_mode_supported": true` alla configurazione dell'app e scrivono il codice per gestire elementi come la disconnessione dei dispositivi condivisi.
- Gli amministratori di dispositivi preparano la condivisione del dispositivo mediante l'installazione dell'app Authenticator e l'impostazione del dispositivo sulla modalità condivisa tramite l'app Authenticator. Solo gli utenti che appartengono al ruolo di [amministratore del dispositivo cloud](../roles/permissions-reference.md#cloud-device-administrator-permissions) possono impostare un dispositivo in modalità condivisa usando l' [app Authenticator](../user-help/user-help-auth-app-overview.md). È possibile configurare l'appartenenza dei ruoli aziendali nel portale di Azure tramite: **Azure Active Directory**  >  **ruoli e amministratori** del  >  **dispositivo cloud amministratore**.

 Questo articolo è incentrato principalmente sugli elementi da considerare per gli sviluppatori.

## <a name="single-vs-multiple-account-applications"></a>Applicazioni a singolo account e multi-account

Le applicazioni scritte utilizzando Microsoft Authentication Library SDK (MSAL) possono gestire un singolo account o più account. Per informazioni dettagliate, vedere [modalità account singolo o modalità account multipli](single-multi-account.md). Le funzionalità della piattaforma Microsoft Identity disponibili per l'app variano a seconda del fatto che l'applicazione sia in esecuzione in modalità account singolo o in modalità account multipli.

Le **app in modalità dispositivo condiviso funzionano solo in modalità account singolo**.

> [!IMPORTANT]
> Le applicazioni che supportano solo la modalità account multipli non possono essere eseguite in un dispositivo condiviso. Se un dipendente carica un'app che non supporta la modalità account singolo, non verrà eseguita sul dispositivo condiviso.
>
> Le app scritte prima dell'SDK di MSAL sono state rilasciate in modalità multi-account e devono essere aggiornate per supportare la modalità account singolo prima di poter essere eseguite in un dispositivo in modalità condivisa.

**Supporto sia per account singolo che per più account**

L'app può essere compilata per supportare l'esecuzione su dispositivi personali e dispositivi condivisi. Se l'app supporta attualmente più account e si vuole supportare la modalità dispositivo condiviso, aggiungere il supporto per la modalità account singolo.

È anche possibile che l'app modifichi il proprio comportamento a seconda del tipo di dispositivo in cui è in esecuzione. Usare `ISingleAccountPublicClientApplication.isSharedDevice()` per determinare quando eseguire in modalità account singolo.

Sono disponibili due interfacce diverse che rappresentano il tipo di dispositivo in cui si trova l'applicazione. Quando si richiede un'istanza dell'applicazione dalla factory applicativa di MSAL, l'oggetto applicazione corretto viene fornito automaticamente.

Il modello a oggetti seguente illustra il tipo di oggetto che è possibile ricevere e il significato nel contesto di un dispositivo condiviso:

![modello di ereditarietà di applicazioni client pubbliche](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Quando si ottiene l'oggetto, è necessario eseguire un controllo del tipo ed eseguire il cast all'interfaccia appropriata `PublicClientApplication` . Il codice seguente consente di controllare la modalità più account o l'account singolo e di eseguire il cast dell'oggetto applicazione in modo appropriato:

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
| **Account**     | Account singolo | Più account |
| **Accesso** | Global | Global |
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
