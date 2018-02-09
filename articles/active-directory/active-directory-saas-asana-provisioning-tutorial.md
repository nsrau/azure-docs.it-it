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
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Esercitazione: Configurare Asana per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in Asana e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD ad Asana.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory
*   Tenant di Asana con il [piano Enterprise](https://www.asana.com/pricing) o superiore abilitato 
*   Account utente in Asana con autorizzazioni di amministratore 

> [!NOTE] 
> L'integrazione del provisioning di Azure AD si basa sull'[API Asana](https://app.asana.com/api/1.0/scim/Users) disponibile in Asana.

## <a name="assigning-users-to-asana"></a>Assegnazione di utenti ad Asana

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti in Azure AD rappresentano gli utenti che devono accedere all'app Asana. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Asana seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Suggerimenti importanti per l'assegnazione di utenti ad Asana

*   È consigliabile assegnare un singolo utente di Azure AD ad Asana per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

## <a name="configuring-user-provisioning-to-asana"></a>Configurazione del provisioning utenti in Asana 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Asana e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Asana in base all'assegnazione di utenti in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Asana, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Asana con Azure AD:

1)  Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2) Se si è già configurato Asana per l'accesso Single Sign-On, cercare l'istanza di Asana usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Asana** nella raccolta di applicazioni. Selezionare **Asana** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3)  Selezionare l'istanza di Asana e quindi la scheda **Provisioning**.

4)  Impostare **Modalità di provisioning** su **Automatico**.

![Provisioning di Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) Nella sezione Credenziali amministratore attenersi alle istruzioni seguenti per generare il token e immetterlo nella casella di testo **Token segreto**.

* Accedere ad [Asana](https://app.asana.com) tramite l'account amministratore
* Fare clic sulla foto del profilo nella barra superiore e selezionare Impostazioni <nome organizzazione corrente>.
* Passare alla scheda Service Accounts (Account di servizio).
* Fare clic su Add Service Account (Aggiungi account di servizio).
* Aggiornare il nome, le informazioni personali e la foto di profilo, se necessario, copiare il valore nel campo **Token** e fare clic su Save Changes (Salva modifiche).

6) Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app Asana. Se la connessione non riesce, verificare che l'account di Asana disponga delle autorizzazioni di amministrazione e ripetere il passaggio **Test connessione**.

7) Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

8) Fare clic su **Save**. 

9) Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Asana** (Sincronizza utenti di Azure Active Directory in Asana).

10) Nella sezione **Mapping degli attributi** esaminare gli attributi utente che verranno sincronizzati da Azure AD ad Asana. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente in Asana per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche. Per altri dettagli, vedere [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

11) Per abilitare il servizio di provisioning di Azure AD per Asana, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**

12) Fare clic su **Save**. 

Viene avviata la sincronizzazione iniziale di tutti gli utenti assegnati ad Asana nella sezione Utenti. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning per l'app Asana.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-asana-tutorial.md)
