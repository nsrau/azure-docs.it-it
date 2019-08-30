---
title: Introduzione alla registrazione combinata per Azure AD SSPR e Multi-Factor Authentication (anteprima)-Azure Active Directory
description: Abilitare la registrazione Multi-Factor Authentication e la reimpostazione della password self-service Azure AD combinati (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55e3f073c1d22ef09784f699dc2decb0492d9edf
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162302"
---
# <a name="enable-combined-security-information-registration-preview"></a>Abilitare la registrazione delle informazioni di sicurezza combinate (anteprima)

Prima di abilitare la nuova esperienza, vedere l'articolo relativo alla [registrazione delle informazioni di sicurezza (anteprima)](concept-registration-mfa-sspr-combined.md) per assicurarsi di comprendere la funzionalità e gli effetti di questa funzionalità.

![Esperienza ottimizzata per la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| La registrazione delle informazioni di sicurezza combinate per la reimpostazione della password self-service di Azure Multi-Factor Authentication e Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure AD. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Le organizzazioni che hanno abilitato l'anteprima precedente per la registrazione e la gestione delle informazioni di sicurezza devono completare la procedura seguente per abilitare l'esperienza di anteprima avanzata. Per le organizzazioni che non eseguono il compartimento, il 25 settembre 2019, Microsoft cambierà gli utenti dell'anteprima precedente per la registrazione e la gestione delle informazioni di sicurezza in modo da migliorare l'esperienza. 
> 
> Se non è stata abilitata alcuna versione dell'anteprima, l'organizzazione non avrà alcun effetto.

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare questi passaggi per abilitare la registrazione combinata:

1. Accedere al portale di Azure come amministratore utente o amministratore globale.
2. Passare a **Azure Active Directory** > **Impostazioni** > utente**Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.
3. In **utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza-aggiornamento**, scegliere di abilitare per un gruppo **selezionato** di utenti o per **tutti** gli utenti.

   ![Abilitare l'esperienza combinata di anteprima delle informazioni di sicurezza per tutti gli utenti](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni per la telefonata non saranno disponibili per Multi-Factor Authentication e SSPR gli utenti in tenant Azure AD gratuiti/di valutazione. I messaggi SMS non sono interessati da questa modifica. Le opzioni per la telefonata saranno ancora disponibili per gli utenti in tenant Azure AD a pagamento.

> [!NOTE]
> Dopo aver abilitato la registrazione combinata, gli utenti che registrano o confermano il numero di telefono o l'app per dispositivi mobili tramite la nuova esperienza possono usarli per Multi-Factor Authentication e SSPR, se tali metodi sono abilitati nella Multi-Factor Authentication e SSPR criteri. Se si disabilita questa esperienza, gli utenti che accedono alla pagina di registrazione di SSPR `https://aka.ms/ssprsetup` precedente a dovranno eseguire l'autenticazione a più fattori prima di poter accedere alla pagina.

Se è stato configurato l'elenco di assegnazione da sito a zona in Internet Explorer, i siti seguenti devono trovarsi nella stessa zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinata

È ora possibile proteggere quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità di anteprima è disponibile per le organizzazioni che hanno abilitato l' [anteprima della registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità può essere abilitata nelle organizzazioni in cui si vuole che gli utenti si registrino per Multi-Factor Authentication di Azure e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane. Per ulteriori informazioni sulla creazione di percorsi attendibili nell'accesso condizionale, vedere l'articolo [Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibile

I criteri seguenti si applicano a tutti gli utenti selezionati, che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e bloccano l'accesso a meno che non si connettano da un percorso contrassegnato come rete attendibile.

![Creare un criterio di autorità di certificazione per controllare la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Nella **portale di Azure**individuare **Azure Active Directory** > **accesso condizionale**
1. Selezionare **Nuovi criteri**
1. In nome immettere un nome per il criterio. Ad esempio, la **registrazione delle informazioni di sicurezza combinata su reti attendibili**
1. In **assegnazioni**fare clic su **utenti e gruppi**e selezionare gli utenti e i gruppi a cui si desidera applicare questo criterio

   > [!WARNING]
   > Gli utenti devono essere abilitati per l' [anteprima della registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

1. In **azioni o app Cloud**selezionare **azioni utente**, selezionare **registra informazioni di sicurezza (anteprima)**
1. In**percorsi** **condizioni** > 
   1. Configura **Sì**
   1. Includi **qualsiasi posizione**
   1. Escludi **tutti i percorsi attendibili**
   1. Fare clic su **fine** nel pannello percorsi
   1. Fare clic su **fine** nel pannello condizioni
1. **Concessione** in **controlli** > di accesso
   1. Fare clic su **Blocca accesso**
   1. Quindi fare clic su **Seleziona**
1. Imposta **Abilita criterio** **su on**
1. Quindi fare clic su **Crea**

## <a name="next-steps"></a>Passaggi successivi

[Forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Metodi disponibili per Multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione self-service delle password](howto-sspr-deployment.md)

[Configurare Multi-Factor Authentication di Azure](howto-mfa-getstarted.md)

[Risoluzione dei problemi di registrazione delle informazioni di sicurezza combinate](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](../conditional-access/location-condition.md)
