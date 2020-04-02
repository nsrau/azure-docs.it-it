---
title: Modalità dispositivo condiviso per dispositivi Android
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come abilitare la modalità dispositivo condiviso per consentire a Firstline Worker di condividere un dispositivo Android
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
ms.openlocfilehash: d9874e27c21906512c2f6c841767b4d6591dbeaf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550266"
---
# <a name="shared-device-mode-for-android-devices"></a>Modalità dispositivo condiviso per dispositivi Android

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I lavoratori di prima linea, come i dipendenti al dettaglio, i membri dell'equipaggio di volo e i lavoratori dei servizi sul campo, spesso utilizzano un dispositivo mobile condiviso per svolgere il proprio lavoro. Ciò diventa problematico quando iniziano a condividere le password o a aggiungere numeri per accedere ai dati dei clienti e aziendali sul dispositivo condiviso.

La modalità dispositivo condiviso consente di configurare un dispositivo Android in modo che possa essere facilmente condiviso da più dipendenti. I dipendenti possono accedere e accedere rapidamente alle informazioni sui clienti. Quando avranno finito con il loro turno o attività, possono disconnettersi dal dispositivo e sarà immediatamente pronto per il prossimo dipendente da utilizzare.

La modalità dispositivo condiviso fornisce anche la gestione supportata dall'identità Microsoft del dispositivo.

Per creare un'app in modalità dispositivo condivisa, gli sviluppatori e gli amministratori dei dispositivi cloud collaborano:To create a shared device mode app, developers and cloud device admins work together:

- Gli sviluppatori scrivono un'app con un solo account (le `"shared_device_mode_supported": true` app con più account non sono supportate in modalità dispositivo condiviso), aggiungono informazioni alla configurazione dell'app e scrivono codice per gestire elementi come la disconnessione del dispositivo condiviso.
- Gli amministratori dei dispositivi preparano il dispositivo da condividere installando l'app di autenticazione e impostando il dispositivo in modalità condivisa tramite l'app di autenticazione. Solo gli utenti che hanno il ruolo [Cloud Device Administrator](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) possono mettere un dispositivo in modalità condivisa utilizzando [l'app Authenticator](../user-help/user-help-auth-app-overview.md). È possibile configurare l'appartenenza dei ruoli organizzativi nel portale di Azure tramite: Ruoli di **Azure Active Directory** > e**Amministratore dispositivo cloud****amministratori.** > 

 Questo articolo si concentra principalmente ciò che gli sviluppatori dovrebbero pensare.

## <a name="single-vs-multiple-account-applications"></a>Applicazioni monoaccount e multiple

Le applicazioni scritte utilizzando Microsoft Authentication Library SDK (MSAL) possono gestire un singolo account o più account. Per informazioni dettagliate, vedere [Modalità account singolo o modalità account multipla](single-multi-account.md). Le funzionalità della piattaforma di identità Microsoft disponibili per l'app variano a seconda che l'applicazione sia in esecuzione in modalità account singolo o con più account.

**Le app in modalità dispositivo condiviso funzionano solo in modalità account singolo.**

> [!IMPORTANT]
> Le applicazioni che supportano solo la modalità con più account non possono essere eseguite in un dispositivo condiviso. Se un dipendente carica un'app che non supporta la modalità conto singolo, non verrà eseguita nel dispositivo condiviso.
>
> Le app scritte prima del rilascio di MSAL SDK vengono eseguite in modalità con più account e devono essere aggiornate per supportare la modalità con account singolo prima di poter essere eseguite in un dispositivo in modalità condivisa.

**Supporto sia di account singolo che di account multipli**

La tua app può essere compilata per supportare l'esecuzione sia su dispositivi personali che su dispositivi condivisi. Se l'app supporta attualmente più account e vuoi supportare la modalità dispositivo condiviso, aggiungi il supporto per la modalità singolo account.

Puoi anche che la tua app ne modifichi il comportamento a seconda del tipo di dispositivo su cui è in esecuzione. Utilizzare `ISingleAccountPublicClientApplication.isSharedDevice()` per determinare quando eseguire in modalità account singolo.

Esistono due interfacce diverse che rappresentano il tipo di dispositivo su cui si trova l'applicazione. Quando si richiede un'istanza dell'applicazione dalla factory dell'applicazione di MSAL, viene fornito automaticamente l'oggetto applicazione corretto.

Il modello a oggetti seguente illustra il tipo di oggetto che è possibile ricevere e il relativo significato nel contesto di un dispositivo condiviso:The following object model illustrates the type of object you may receive and what it means in the context of a shared device:

![modello di ereditarietà dell'applicazione client pubblica](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

È necessario eseguire un controllo del tipo ed eseguire il `PublicClientApplication` cast all'interfaccia appropriata quando si ottiene l'oggetto. Il codice seguente verifica la presenza di più modalità account o di una modalità account singolo ed esegue il cast dell'oggetto applicazione in modo appropriato:

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
| **Account**     | Conto singolo | Account multipli |
| **Accesso** | Global | Global |
| **Disconnessione** | Global | Ogni applicazione può controllare se la disconnessione è locale per l'app o per la famiglia di applicazioni. |
| **Tipi di account supportati** | Solo account di lavoro | Account personali e di lavoro supportati  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Perché è possibile che si desideri supportare la modalità con to-single-account

Se stai scrivendo un'app che verrà usata solo per i lavoratori di prima linea che utilizzano un dispositivo condiviso, ti consigliamo di scrivere l'applicazione solo per supportare la modalità con account singolo. Questo vale per la maggior parte delle applicazioni incentrate su attività, come le app medicali, le app di fatturazione e quasi tutte le app line-of-business. Il supporto della modalità single-account semplifica lo sviluppo solo perché non è necessario implementare le funzionalità aggiuntive che fanno parte di app con più account.

## <a name="what-happens-when-the-device-mode-changes"></a>Cosa succede quando cambia la modalità del dispositivo

Se l'applicazione è in esecuzione in modalità con più account e un amministratore attiva la modalità dispositivo condiviso, tutti gli account nel dispositivo vengono cancellati dall'applicazione e l'applicazione passa alla modalità con account singolo.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Disconnessione del dispositivo condiviso e ciclo di vita complessivo dell'app

Quando un utente si disconnette, è necessario intervenire per proteggere la privacy e i dati dell'utente. Ad esempio, se stai creando un'app di cartelle cliniche, assicurati che quando l'utente si disconnette i record dei pazienti visualizzati in precedenza vengano cancellati. L'applicazione deve essere preparata e controllare ogni volta che entra in primo piano.

Quando l'app usa MSAL per disconnettere l'utente in un'app in esecuzione su un dispositivo in modalità condivisa, l'account di accesso e i token memorizzati nella cache vengono rimossi sia dall'app che dal dispositivo.

Il diagramma seguente mostra il ciclo di vita complessivo dell'app e gli eventi comuni che possono verificarsi durante l'esecuzione dell'app. Il diagramma illustra dal momento in cui un'attività viene avviata, l'accesso e la disconnessione di un account e il modo in cui eventi come la sospensione, la ripresa e l'arresto dell'attività rientrano.

![Ciclo di vita dell'app per dispositivi condivisi](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione Usare la [modalità dispositivo condiviso nell'applicazione Android](tutorial-v2-shared-device-mode.md) che mostra come eseguire un'app di lavoro di prima linea in un dispositivo Android in modalità condivisa.
