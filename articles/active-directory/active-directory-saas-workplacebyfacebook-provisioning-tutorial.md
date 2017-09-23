---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
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
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9b22679c304248ed7ba7a6bd9eaf82b64f7143cf
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="tutorial-configuring-workplace-by-facebook-for-user-provisioning"></a>Esercitazione: Configurazione di Workplace by Facebook per il provisioning utenti

L'obiettivo di questa esercitazione è descrivere le procedure da eseguire in Workplace by Facebook e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Workplace by Facebook.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workplace by Facebook, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di Workplace by Facebook abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Assegnazione di utenti a Workplace by Facebook

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Workplace by Facebook. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Workplace by Facebook seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Suggerimenti importanti per l'assegnazione di utenti a Workplace by Facebook

*   È consigliabile assegnare un singolo utente di Azure AD a Workplace by Facebook per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Workplace by Facebook, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Workplace by Facebook e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Workplace by Facebook in base all'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Workplace by Facebook, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Per configurare l'account utente eseguendo il provisioning a Workplace by Facebook in Azure AD:

L'obiettivo di questa sezione è descrivere le modalità di abilitazione del provisioning utente per gli account utente di Active Directory in Workplace by Facebook.

Azure AD consente di sincronizzare automaticamente i dettagli dell'account degli utenti assegnati a Workplace by Facebook. La sincronizzazione automatica consente a Workplace by Facebook di ottenere i dati necessari per autorizzare gli utenti ad accedere, prima che questi tentino di eseguire l'accesso per la prima volta. Esegue anche il deprovisioning degli utenti da Workplace by Facebook una volta che l'accesso viene revocato in Azure AD.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **App aziendali** > **Tutte le applicazioni**.

2. Se si è già configurato Workplace by Facebook per l'accesso Single Sign-On, cercare l'istanza di Workplace by Facebook usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Workplace by Facebook** nella raccolta di applicazioni. Selezionare Workplace by Facebook nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Workplace by Facebook e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**. 

    ![provisioning](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Sotto la sezione **Credenziali dell’amministratore** inserire il token segreto e l'URL del tenant dell'amministratore di Workplace by Facebook.

6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Workplace by Facebook. Se la connessione non riesce, verificare che l'account Workplace by Facebook abbia le autorizzazioni di amministratore di team.

7. Immettere l'indirizzo e-mail di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Workplace by Facebook** (Sincronizza utenti di Azure Active Directory in Workplace by Facebook).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Workplace by Facebook. Gli attributi selezionati come proprietà **Corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Workplace by Facebook per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

11. Per abilitare il servizio di provisioning di Azure AD per Workplace by Facebook, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

12. Fare clic su **Salva**.

Per altre informazioni su come configurare il provisioning automatico, vedere [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

È ora possibile creare un account di test. Attendere 20 minuti per verificare che l'account sia stato sincronizzato con Workplace by Facebook.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-workplacebyfacebook-tutorial.md)


