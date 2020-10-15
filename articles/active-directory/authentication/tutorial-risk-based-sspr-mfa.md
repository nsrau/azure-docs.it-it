---
title: Protezione dell'accesso utente basata sul rischio in Azure Active Directory
description: Questa esercitazione illustra come abilitare Azure Identity Protection per proteggere gli utenti quando viene rilevato un comportamento di accesso a rischio nei rispettivi account.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d0fcd57a71baec54fbed2dd41a936895ad9a462
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966577"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>Esercitazione: Usare i rilevamenti dei rischi negli accessi utente per attivare Azure Multi-Factor Authentication o modifiche della password

Per proteggere gli utenti, è possibile configurare criteri basati sul rischio in Azure Active Directory (Azure AD) per rispondere automaticamente a comportamenti a rischio. I criteri di Azure AD Identity Protection possono bloccare automaticamente un tentativo di accesso o richiedere azioni aggiuntive, ad esempio la modifica della password o l'uso di Azure Multi-Factor Authentication. Questi criteri funzionano con quelli di accesso condizionale di Azure AD per offrire uno strato aggiuntivo di protezione per l'organizzazione. Gli utenti potrebbero non attivare mai un comportamento a rischio di uno di questi criteri, ma l'organizzazione è protetta nel caso in cui venga effettuato un tentativo di compromettere la sicurezza.

> [!IMPORTANT]
> Questa esercitazione contiene indicazioni su come un amministratore può abilitare Azure Multi-Factor Authentication.
>
> Se il team IT non ha consentito di usare Azure Multi-Factor Authentication o se si verificano problemi durante l'accesso, rivolgersi al supporto tecnico per ulteriore assistenza.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Comprendere i criteri disponibili per Azure AD Identity Protection
> * Abilitare la registrazione per Azure Multi-Factor Authentication
> * Abilitare le modifiche delle password in base ai rischi
> * Abilitare Multi-Factor Authentication in base ai rischi
> * Testare i criteri basati sul rischio per i tentativi di accesso utente

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant di Azure AD funzionante con almeno una licenza di valutazione o di Azure AD Premium P2 abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di *amministratore globale*.
* Azure AD, configurato per la reimpostazione della password self-service e Azure Multi-Factor Authentication.
    * Se necessario, [completare l'esercitazione per abilitare la reimpostazione della password self-service di Azure AD](tutorial-enable-sspr.md).
    * Se necessario, [completare l'esercitazione per abilitare Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Panoramica di Azure AD Identity Protection

Ogni giorno, Microsoft raccoglie e analizza trilioni di segnali anonimizzati nell'ambito dei tentativi di accesso utente. Questi segnali consentono di creare modelli del comportamento di accesso utente corretto e identificare i potenziali tentativi di accesso a rischio. Azure AD Identity Protection consente di esaminare i tentativi di accesso utente e intraprendere azioni aggiuntive in caso di comportamento sospetto.

Il rilevamento dei rischi di Azure AD Identity Protection può essere attivato da alcune delle circostanze seguenti:

* Utenti con credenziali perse
* Accessi da indirizzi IP anonimi
* Spostamento fisico impossibile a posizioni atipiche
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospetta
* Accessi da posizioni insolite

Per proteggere gli utenti e rispondere alle attività sospette, in Azure AD Identity Protection sono disponibili i tre criteri descritti di seguito. È possibile scegliere di attivare o disattivare l'applicazione dei criteri, selezionare gli utenti o i gruppi a cui applicare i criteri e decidere se bloccare l'accesso o richiedere un'azione aggiuntiva.

* Criteri di rischio utente
    * Identificano gli account utente le cui credenziali potrebbero essere compromesse e rispondono, ad esempio richiedendo all'utente di creare una nuova password.
* Criteri di rischio di accesso
    * Identificano i tentativi di accesso sospetti e rispondono, ad esempio richiedendo all'utente di fornire forme di verifica aggiuntive con Azure Multi-Factor Authentication.
* Criteri di registrazione MFA
    * Verificano che gli utenti siano registrati per Azure Multi-Factor Authentication. Se i criteri di rischio di accesso richiedono MFA, l'utente deve essere già registrato per Azure Multi-Factor Authentication.

Quando si abilitano i criteri di rischio utente o di accesso, si può anche scegliere la soglia per il livello di rischio: *basso e superiore*, *medio e superiore* o *alto*. Questa flessibilità consente di decidere con quale rigore si vogliono applicare i controlli per gli eventi di accesso sospetti.

Per altre informazioni su Azure AD Identity Protection, vedere [Informazioni su Azure AD Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="enable-mfa-registration-policy"></a>Abilitare i criteri di registrazione MFA

Azure AD Identity Protection include criteri predefiniti che consentono di ottenere la registrazione degli utenti per Azure Multi-Factor Authentication. Se si usano criteri aggiuntivi per proteggere gli eventi di accesso, gli utenti dovranno avere già effettuato la registrazione per MFA. Quando si abilitano questi criteri, non è necessario che gli utenti eseguano MFA a ogni evento di accesso. I criteri controllano solo lo stato di registrazione di un utente e chiedono di effettuare la registrazione preliminare se necessario.

È consigliabile abilitare i criteri di registrazione MFA per gli utenti che devono essere abilitati per i criteri aggiuntivi di Azure AD Identity Protection. Per abilitare questi criteri, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Cercare e selezionare **Azure Active Directory**, selezionare **Sicurezza** e quindi scegliere *Identity Protection* sotto l'intestazione di menu **Proteggi**.
1. Selezionare **Criteri di registrazione MFA** nel menu sul lato sinistro.
1. Per impostazione predefinita, i criteri si applicano a *Tutti gli utenti*. Se si vuole, selezionare **Assegnazioni** e quindi scegliere gli utenti o i gruppi a cui applicare i criteri.
1. In *Controlli* selezionare **Accesso**. Assicurarsi che l'opzione *Richiedi registrazione ad Azure MFA* sia selezionata e quindi scegliere **Seleziona**.
1. Impostare **Imponi criteri** su *Sì* e quindi selezionare **Salva**.

    ![Screenshot di come richiedere agli utenti di effettuare la registrazione per MFA nel portale di Azure](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Abilitare i criteri di rischio utente per la modifica della password

Microsoft collabora con ricercatori, forze dell'ordine, diversi team di sicurezza di Microsoft e altre fonti attendibili per trovare coppie di nome utente e password. Quando una di queste coppie corrisponde a un account nell'ambiente in uso, può essere richiesta una modifica della password basata sul rischio. Con questi criteri e questa azione, l'utente per poter accedere dovrà prima di tutto aggiornare la propria password affinché le credenziali precedentemente esposte non funzionino più.

Per abilitare questi criteri, seguire questa procedura:

1. Selezionare **Criteri di rischio utente** nel menu sul lato sinistro.
1. Per impostazione predefinita, i criteri si applicano a *Tutti gli utenti*. Se si vuole, selezionare **Assegnazioni** e quindi scegliere gli utenti o i gruppi a cui applicare i criteri.
1. In *Condizioni* scegliere **Selezionare le condizioni > Selezionare un livello di rischio** e quindi *Medio e superiore*.
1. Scegliere **Seleziona** e quindi **Fine**.
1. In *Accesso* selezionare **Accesso**. Verificare che le opzioni **Consenti l'accesso** e *Richiedi modifica password* siano selezionate e quindi scegliere **Seleziona**.
1. Impostare **Imponi criteri** su *Sì* e quindi selezionare **Salva**.

    ![Screenshot di come abilitare i criteri di rischio utente nel portale di Azure](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Abilitare i criteri di rischio di accesso per MFA

La maggior parte degli utenti ha un comportamento normale di cui è possibile tenere traccia. Quando gli utenti esulano da questa norma, potrebbe essere rischioso consentire il completamento dell'accesso. Al contrario, potrebbe essere opportuno bloccare l'utente o chiedere di eseguire un'autenticazione a più fattori. Se l'utente completa correttamente la richiesta di autenticazione MFA, è possibile considerare valido il tentativo di accesso e concedere l'accesso all'applicazione o al servizio.

Per abilitare questi criteri, seguire questa procedura:

1. Selezionare **Criteri di rischio di accesso** nel menu sul lato sinistro.
1. Per impostazione predefinita, i criteri si applicano a *Tutti gli utenti*. Se si vuole, selezionare **Assegnazioni** e quindi scegliere gli utenti o i gruppi a cui applicare i criteri.
1. In *Condizioni* scegliere **Selezionare le condizioni > Selezionare un livello di rischio** e quindi *Medio e superiore*.
1. Scegliere **Seleziona** e quindi **Fine**.
1. In *Accesso* scegliere **Selezionare un controllo**. Verificare che le opzioni **Consenti l'accesso** e *Richiedi autenticazione a più fattori* siano selezionate e quindi scegliere **Seleziona**.
1. Impostare **Imponi criteri** su *Sì* e quindi selezionare **Salva**.

    ![Screenshot di come abilitare i criteri di rischio di accesso nel portale di Azure](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Testare gli eventi di accesso a rischio

La maggior parte degli eventi di accesso utente non attiverà i criteri basati sul rischio configurati nei passaggi precedenti. A un utente potrebbe non essere mai visualizzata una richiesta di autenticazione MFA aggiuntiva o di reimpostazione della password. Se le credenziali rimangono sicure e il comportamento dell'utente è coerente, gli eventi di accesso avranno esito positivo.

Per testare i criteri di Azure AD Identity Protection creati nei passaggi precedenti, è necessario simulare un comportamento a rischio o potenziali attacchi. La procedura per eseguire questi test varia in base ai criteri di Azure AD Identity Protection da convalidare. Per altre informazioni sugli scenari e la procedura, vedere [Simulare i rilevamenti dei rischi in Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se dopo aver completato i test non si vogliono mantenere abilitati i criteri basati sul rischio, tornare a ogni criterio da disabilitare e impostare **Imponi criteri** su *No*.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si sono abilitati i criteri utente basati sul rischio per Azure AD Identity Protection. Si è appreso come:

> [!div class="checklist"]
> * Comprendere i criteri disponibili per Azure AD Identity Protection
> * Abilitare la registrazione per Azure Multi-Factor Authentication
> * Abilitare le modifiche delle password in base ai rischi
> * Abilitare Multi-Factor Authentication in base ai rischi
> * Testare i criteri basati sul rischio per i tentativi di accesso utente

> [!div class="nextstepaction"]
> [Altre informazioni su Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
