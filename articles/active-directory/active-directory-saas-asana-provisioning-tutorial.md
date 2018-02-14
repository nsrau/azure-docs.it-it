---
title: 'Esercitazione: Configurare Asana per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Asana.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: d72b5aa352a9c6724e801c1741969525387f6b90
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Esercitazione: Configurare Asana per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Asana e Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD ad Asana.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Un tenant di Asana con un piano [Enterprise](https://www.asana.com/pricing) o superiore abilitato 
*   Un account utente in Asana con autorizzazioni di amministratore 

> [!NOTE] 
> L'integrazione del provisioning di Azure AD si basa sull'[API Asana](https://app.asana.com/api/1.0/scim/Users), disponibile in Asana.

## <a name="assign-users-to-asana"></a>Assegnare utenti ad Asana

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure AD usa un concetto detto "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti assegnati a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti in Azure AD devono accedere all'app Asana. È quindi possibile assegnare tali utenti all'app Asana seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Suggerimenti importanti per l'assegnazione di utenti ad Asana

È consigliabile assegnare un singolo utente di Azure AD ad Asana per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

## <a name="configure-user-provisioning-to-asana"></a>Configurare il provisioning utenti in Asana 

Questa sezione descrive la connessione di Azure AD all'API di provisioning degli account utente Asana. Il servizio di provisioning viene anche configurato per creare, aggiornare e disabilitare gli account utente assegnati in Asana in base alle assegnazioni utente in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Asana, seguire le istruzioni fornite nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Asana con Azure AD

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **App aziendali** > **Tutte le applicazioni**.

2. Se si è già configurato Asana per l'accesso Single Sign-On, cercare l'istanza di Asana usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Asana** nella raccolta di applicazioni. Selezionare **Asana** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Asana e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5. Nella sezione **Credenziali amministratore** seguire queste istruzioni per generare il token e immetterlo in **Token segreto**:

    a. Accedere ad [Asana](https://app.asana.com) usando l'account amministratore.

    b. Selezionare la foto del profilo nella barra superiore e selezionare le impostazioni per l'organizzazione corrente.

    c. Passare alla scheda **Service Accounts** (Account di servizio).

    d. Selezionare **Add Service Account** (Aggiungi account di servizio).

    e. Aggiornare i dati in **Name** (Nome) e **About** (Informazioni) e la foto del profilo, come necessario. Copiare il token in **Token** e selezionarlo in **Save Changes** (Salva modifiche).

6. Nel portale di Azure selezionare **Test connessione** per verificare che Azure AD possa connettersi all'app Asana. Se la connessione non riesce, verificare che l'account Asana abbia le autorizzazioni di amministrazione e ripetere il passaggio **Test connessione**.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Indirizzo di posta elettronica per le notifiche**. Selezionare la casella di controllo sottostante.

8. Selezionare **Salva**. 

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Asana** (Sincronizza utenti di Azure Active Directory in Asana).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente da sincronizzare da Azure AD ad Asana. Gli attributi selezionati come proprietà**corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Asana per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi del provisioning](active-directory-saas-customizing-attribute-mappings.md).

11. Per abilitare il servizio di provisioning di Azure AD per Asana, nella sezione **Impostazioni** impostare **Stato del provisioning** su **Sì**.

12. Selezionare **Salva**. 

Viene avviata la sincronizzazione iniziale di tutti gli utenti assegnati ad Asana nella sezione **Utenti**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. Usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning. I report descrivono tutte le azioni eseguite dal servizio di provisioning nell'app Asana.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-asana-tutorial.md)
