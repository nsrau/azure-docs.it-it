---
title: Impedire gli attacchi usando il blocco intelligente - Azure Active DirectoryPreventing attacks using smart lockout - Azure Active Directory
description: La funzione di blocco intelligente di Azure Active Directory consente di proteggere le organizzazioni da eventuali attacchi di forza bruta costituiti da tentativi di indovinare le password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652575"
---
# <a name="azure-active-directory-smart-lockout"></a>Blocco intelligente di Azure Active Directory

Smart lockout aiuta a bloccare i cattivi attori che stanno cercando di indovinare le password degli utenti o utilizzare metodi di forza bruta per entrare. Questa intelligenza è in grado di riconoscere i tentativi di accesso eseguiti da utenti validi e di gestirli in modo diverso da quelli di utenti malintenzionati e di altre origini sconosciute. La funzione di blocco intelligente blocca gli utenti malintenzionati e al tempo stesso consente a quelli validi di accedere ai propri account senza effetti negativi sulla produttività.

Per impostazione predefinita, dopo dieci tentativi non riusciti la funzione di blocco intelligente blocca l'account da nuovi tentativi di accesso per un minuto. Dopo ogni successivo tentativo di accesso non riuscito, l'account viene nuovamente bloccato prima per un minuto e quindi per intervalli più lunghi.

Il blocco intelligente tiene traccia degli ultimi tre hash delle password non validi per evitare l'incremento del contatore dei blocchi per la stessa password. Se un utente immette la stessa password errata più volte, questo comportamento non comporterà il blocco dell'account.

 > [!NOTE]
 > La funzionalità di rilevamento hash non è disponibile per i clienti con autenticazione pass-through abilitata, poiché l'autenticazione avviene in locale e non nel cloud.

Le distribuzioni federate con AD FS 2016 ed AF FS 2019 possono abilitare vantaggi simili usando [AD FS Extranet Lockout e Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Il blocco intelligente è sempre attivo per tutti i clienti di Azure AD con le impostazioni predefinite che offrono la giusta combinazione di sicurezza e usabilità. La personalizzazione delle impostazioni di blocco intelligente, con valori specifici per l'organizzazione, richiede licenze di Azure AD a pagamento per gli utenti.

L'utilizzo del blocco intelligente non garantisce che un utente originale non venga mai bloccato. Quando il blocco intelligente blocca un account utente, facciamo del nostro meglio per non bloccare l'utente originale. Il servizio di blocco tenta di garantire che gli attori non riusciti a ottenere l'accesso a un account utente originale.  

* Ogni data center di Azure Active Directory tiene traccia del blocco in modo indipendente. Un utente disporrà di un numero di tentativi pari a (limite_soglia * conteggio_datacenter), se l'utente esegue l'accesso a ogni data center.
* Il blocco intelligente usa la differenziazione tra posizioni conosciute e sconosciute per individuare gli utenti originali e distinguerli da quelli non autorizzati. Le posizioni conosciute e quelle sconosciute disporranno entrambe di contatori di blocco distinti.

Il blocco intelligente può essere integrato con distribuzioni ibride, tramite la sincronizzazione degli hash delle password o l'autenticazione pass-through, per impedire il blocco degli account locali di Active Directory da parte di utenti malintenzionati. Impostando opportuni criteri di blocco intelligente in Azure AD, è possibile filtrare gli attacchi prima che raggiungano l'istanza locale di Active Directory.

Quando si usa l'[autenticazione pass-through](../hybrid/how-to-connect-pta.md), è necessario verificare quanto segue:

* La soglia di blocco di Azure AD è **inferiore** alla soglia di blocco dell'account Active Directory. Configurare i valori in modo che la soglia di blocco dell'account Active Directory sia almeno due o tre volte superiore rispetto alla soglia di blocco di Azure AD. 
* La durata del blocco di Azure AD deve essere impostata più a lungo del contatore di blocco dell'account di reimpostazione di Active Directory dopo la durata. Tenere presente che la durata di Azure AD è impostata in secondi, mentre la durata di Active Directory è impostata in minuti. 

Ad esempio, se si vuole che il contatore di Azure AD sia superiore a AD, Azure AD sarà 120 secondi (2 minuti) mentre l'AD locale è impostato su 1 minuto (60 secondi).

> [!IMPORTANT]
> Attualmente, un amministratore non può sbloccare gli account cloud degli utenti se sono stati bloccati dalla funzionalità di blocco intelligente. L'amministratore deve attendere la scadenza della durata del blocco. Tuttavia, l'utente può sbloccare utilizzando la reimpostazione della password self-service (SSPR) da un dispositivo attendibile o una posizione.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificare i criteri di blocco degli account locali

Per verificare i criteri di blocco degli account di Active Directory, usare le istruzioni seguenti:

1. Aprire lo strumento Gestione criteri di gruppo.
2. Modificare i criteri di gruppo che includono i criteri di blocco degli account dell'organizzazione, ad esempio i **criteri di dominio predefiniti**.
3. Passare a**Criteri** >  **di configurazione** > del computer**Impostazioni impostazioni** > **di protezione Impostazioni** > impostazioni**account** > **Criteri di blocco .**
4. Verificare la soglia di **blocco dell'account** e Reimpostare il **contatore** di blocco account dopo i valori.

![Modificare il criterio di blocco degli account di Active Directory locale](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gestire i valori del blocco intelligente di Azure AD

In base alle esigenze dell'organizzazione, può essere necessario personalizzare i valori del blocco intelligente. La personalizzazione delle impostazioni di blocco intelligente, con valori specifici per l'organizzazione, richiede licenze di Azure AD a pagamento per gli utenti.

Per verificare o modificare i valori del blocco intelligente per l'organizzazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare *Azure Active Directory*. Selezionare > Metodi**di autenticazione**di **sicurezza** > **Protezione con password**.
1. Impostare il valore di **Soglia di blocco**, in base al numero di accessi non riusciti consentiti per un account prima che venga applicato il primo blocco. Il valore predefinito è 10.
1. Impostare il valore di **Durata del blocco in secondi** sulla durata in secondi di ogni blocco. Il valore predefinito è 60 secondi (1 minuto).

> [!NOTE]
> Se anche il primo accesso dopo un blocco non riesce, l'account viene bloccato nuovamente. Se un account viene bloccato più volte, la durata del blocco aumenta.

![Personalizzare i criteri di blocco intelligente di Azure Active Directory nel portale di Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Come determinare se la funzione di blocco intelligente funziona o meno

Quando viene attivata la soglia di blocco intelligente, verrà visualizzato il seguente messaggio mentre l'account è bloccato:

**Il tuo account è temporaneamente bloccato per impedire l'uso non autorizzato. Riprova più tardi e, se il problema persiste, contatta l'amministratore.**

## <a name="next-steps"></a>Passaggi successivi

* [Leggere le informazioni su come escludere le password non valide tramite Azure AD](howto-password-ban-bad.md)
* [Configurare la reimpostazione di password self-service per consentire agli utenti di sbloccare i relativi account](quickstart-sspr.md)
