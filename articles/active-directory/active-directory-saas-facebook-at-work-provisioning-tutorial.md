---
title: 'Esercitazione: Configurare Workplace by Facebook per il provisioning degli utenti | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Esercitazione: Configurare Workplace by Facebook per il provisioning degli utenti

Questa esercitazione illustra i passaggi necessari per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure Active Directory (Azure AD) a Workplace by Facebook.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workplace by Facebook, sono necessari:

- Sottoscrizione di Azure AD.
- Una sottoscrizione a Workplace by Facebook abilitata per l'accesso Single Sign-On (SSO)

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di valutazione di Azure AD, è possibile ricevere un'[offerta di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Assegnare utenti a Workplace by Facebook

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure AD usa un concetto detto "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, stabilire quali utenti e gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Workplace by Facebook. Poi è possibile assegnare questi utenti all'app Workplace by Facebook seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Testare la configurazione del provisioning tramite l'assegnazione di un singolo utente di Azure AD a Workplace by Facebook. Assegnare utenti e gruppi aggiuntivi in un secondo momento.
>*   Quando si assegna un utente a Workplace by Facebook, è necessario selezionare un ruolo utente valido. Il ruolo di accesso predefinito non funziona per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione descrive la connessione di Azure AD all'API di provisioning dell'account utente di Workplace by Facebook. Inoltre fornisce informazioni su come configurare il servizio di provisioning per creare, aggiornare e disabilitare gli account utente assegnati in Workplace by Facebook. Questo è basato sull'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>È possibile anche scegliere di abilitare l'accesso SSO basato su SAML per Workplace by Facebook seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso SSO può essere configurato indipendentemente dal provisioning automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configurare l'account utente eseguendo il provisioning a Workplace by Facebook in Azure AD

Azure AD consente di sincronizzare automaticamente i dettagli dell'account degli utenti assegnati a Workplace by Facebook. La sincronizzazione automatica consente a Workplace by Facebook di ottenere i dati necessari per autorizzare gli utenti ad accedere, prima che questi tentino di eseguire l'accesso per la prima volta. Esegue anche il deprovisioning degli utenti da Workplace by Facebook una volta che l'accesso viene revocato in Azure AD.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** > **Enterprise Apps** (App aziendali)  > **Tutte le applicazioni** (Tutte le app).

2. Se si è già configurato Workplace by Facebook per l'accesso SSO, cercare l'istanza di Workplace by Facebook usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Workplace by Facebook** nella raccolta di applicazioni. Selezionare **Workplace by Facebook** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Workplace by Facebook e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![Screenshot delle opzioni di provisioning di Workplace by Facebook](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. Nella sezione **Credenziali amministratore** inserire il **token segreto** e l'**URL tenant** dell'amministratore di Workplace by Facebook.

6. Nel portale di Azure selezionare **Connessione di test** per verificare che Azure AD possa connettersi all'app Workplace by Facebook. Se la connessione ha esito negativo, verificare che l'account Workplace by Facebook disponga delle autorizzazioni di amministratore del team.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che dovrà ricevere le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

8. Selezionare **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Workplace by Facebook** (Sincronizza utenti di Azure Active Directory in Workplace by Facebook).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Workplace by Facebook. Gli attributi selezionati come proprietà **Corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Workplace by Facebook per le operazioni di aggiornamento. Per eseguire il commit di tutte le modifiche, selezionare **Salva**.

11. Per abilitare il servizio di provisioning di Azure AD per Workplace by Facebook, nella sezione **Impostazioni** modificare lo **stato del provisioning** su **On**.

12. Selezionare **Salva**.

Per altre informazioni su come configurare il provisioning automatico, vedere [la documentazione di Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Workplace by Facebook.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-facebook-at-work-tutorial.md)

