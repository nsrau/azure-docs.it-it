---
title: MFA e reimpostazione della password self-service basate su rischi con Azure Identity Protection
description: In questa esercitazione si abiliteranno le integrazioni di Azure Identity Protection per Multi-Factor Authentication e la reimpostazione della password self-service, per ridurre i comportamenti rischiosi.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: df3344efadbc915bba0c863979cae8b8fdff99b0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512126"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Esercitazione: Usare eventi di rischio per attivare Multi-Factor Authentication e modifiche delle password

In questa esercitazione si abiliteranno funzionalità di Azure Active Directory (Azure AD) Identity Protection, una funzionalità di Azure AD Premium P2 che è molto di più di uno strumento monitoraggio e creazione di report. Per proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente a comportamenti rischiosi. Questi criteri possono attivare un blocco immediato oppure avviare una misura di correzione, tra cui la richiesta di modiche delle password e l'applicazione di Multi-Factor Authentication.

I criteri di Azure AD Identity Protection possono essere usati in aggiunta ai criteri di accesso condizionale esistenti, come ulteriore livello di protezione. Gli utenti potrebbero anche non attivare mai un comportamento rischioso che richiede l'applicazione di uno di questi criteri, ma in questo modo un amministratore può essere sicuro che siano protetti.

Alcune condizioni che possono attivare un evento di rischio includono:

* Utenti con credenziali perse
* Accessi da indirizzi IP anonimi
* Trasferimento impossibile a posizioni atipiche
* Accessi da dispositivi infetti
* Accessi da indirizzi IP con attività sospette
* Accessi da posizioni non note

Altre informazioni su Azure AD Identity Protection sono disponibili nell'articolo [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Abilitare la registrazione per Azure MFA
> * Abilitare le modifiche delle password in base ai rischi
> * Abilitare Multi-Factor Authentication in base ai rischi

## <a name="prerequisites"></a>Prerequisiti

* Accesso a un tenant funzionante di Azure AD, con almeno una licenza di valutazione di Azure AD Premium P2 assegnata.
* Un account con privilegi di amministratore globale nel tenant di Azure AD.
* Completamento delle esercitazioni precedenti sulla reimpostazione della password self-service e su Multi-Factor Authentication (MFA).

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Abilitare i criteri basati sul rischio per la reimpostazione della password self-service e MFA

Abilitare i criteri basati sul rischio è un processo semplice. La procedura seguente illustra il processo in modo guidato tramite una configurazione di esempio.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Abilitare gli utenti per la registrazione per Multi-Factor Authentication

Azure AD Identity Protection include un criterio predefinito che può essere utile per ottenere che gli utenti vengano registrati per Multi-Factor Authentication e per identificare facilmente lo stato corrente della registrazione. Con l'abilitazione di questo criterio non viene richiesto agli utenti di eseguire Multi-Factor Authentication, ma verrà richiesto loro di preregistrarsi.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **Registrazione MFA**.
1. Impostare Imponi criteri su **Sì**.
   1. Con l'impostazione di questo criterio, tutti gli utenti dovranno registrare i metodi per prepararsi all'uso di Multi-Factor Authentication.
1. Fare clic su **Save**.

   ![Richiedere agli utenti di registrarsi per MFA al momento dell'accesso tramite Azure AD Identity Protection](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Abilitare le modifiche delle password in base ai rischi

Microsoft collabora con ricercatori, forze dell'ordine, diversi team di sicurezza di Microsoft e altre fonti attendibili per trovare coppie di nome utente e password. Quando una di queste coppie corrisponde a un account in un ambiente specifico, è possibile attivare una modifica della password basata sul rischio tramite i criteri seguenti.

1. Fare clic su Criteri di rischio utente.
1. In **Condizioni** selezionare **Rischio utente** e quindi scegliere **Medio e superiore**.
1. Fare clic su "Seleziona" e quindi su "Fine".
1. In **Accesso** scegliere **Consenti l'accesso** e quindi selezionare **Richiedi modifica password**.
1. Fare clic su "Seleziona"
1. Impostare Imponi criteri su **Sì**.
1. Fare clic su **Save**

### <a name="enable-risk-based-multi-factor-authentication"></a>Abilitare Multi-Factor Authentication in base ai rischi

La maggior parte degli utenti ha un comportamento normale monitorabile. In condizioni che esulano dalla normalità, potrebbe essere rischioso consentire loro semplicemente di accedere. In questi casi è possibile bloccare l'utente o chiedergli semplicemente di usare Multi-Factor Authentication per dimostrare la sua effettiva identità. Per abilitare criteri che richiedono MFA quando viene rilevato un accesso rischioso, procedere come segue.

1. Fare clic su Criteri di rischio di accesso
1. In **Condizioni** selezionare **Rischio utente** e quindi scegliere **Medio e superiore**.
1. Fare clic su "Seleziona" e quindi su "Fine".
1. In **Accesso** scegliere **Consenti l'accesso** e quindi selezionare **Richiedi autenticazione a più fattori**.
1. Fare clic su "Seleziona"
1. Impostare Imponi criteri su **Sì**.
1. Fare clic su **Save**

## <a name="clean-up-resources"></a>Pulire le risorse

Se i test sono completi e non si vuole più mantenere abilitati i criteri basati sul rischio, tornare a ogni criterio da disabilitare e impostare **Imponi criteri** su **No**.
