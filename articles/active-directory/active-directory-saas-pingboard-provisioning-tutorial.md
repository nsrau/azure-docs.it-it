---
title: 'Esercitazione: Configurazione di Pingboard per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Pingboard.
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
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Esercitazione: Configurare Pingboard per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire per abilitare il provisioning e il deprovisioning automatici degli account utente da Azure Active Directory a Pingboard.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory
*   Un [account Pro](https://pingboard.com/pricing) del tenant Pingboard 
*   Account utente in Pingboard con autorizzazioni di amministratore 

> [!NOTE] 
> L'integrazione del provisioning di Azure Active Directory si basa sull'API Pingboard (`https://your_domain.pingboard.com/scim/v2`) disponibile per l'account in uso.

## <a name="assigning-users-to-pingboard"></a>Assegnazione di utenti a Pingboard

Per determinare gli utenti che dovranno ricevere l'accesso alle applicazioni selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti che sono stati "assegnati" a un'applicazione in Azure Active Directory. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti in Azure Active Directory rappresentano gli utenti che devono accedere all'app Pingboard. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app Pingboard seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Suggerimenti importanti per l'assegnazione di utenti a Pingboard

*   È consigliabile di assegnare un singolo utente di Azure Active Directory a Pingboard per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

## <a name="configuring-user-provisioning-to-pingboard"></a>Configurazione del provisioning utenti in Pingboard 

Questa sezione illustra la connessione di Azure Active Directory all'API per il provisioning degli account utente di Pingboard e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Pingboard in base all'assegnazione di utenti in Azure Active Directory.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Pingboard, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Per configurare il provisioning automatico degli account utente in Pingboard con Azure Active Directory:

1)  Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2) Se si è già configurato Pingboard per l'accesso Single Sign-On, cercare l'istanza di Pingboard usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Pingboard** nella raccolta di applicazioni. Selezionare **Pingboard** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3)  Selezionare l'istanza di Pingboard e quindi la scheda **Provisioning**.

4)  Impostare **Modalità di provisioning** su **Automatico**.

![Provisioning in Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) Nella sezione Credenziali amministratore, seguire questa procedura:

* Nella casella di testo **URL tenant** immettere `https://your_domain.pingboard.com/scim/v2` e sostituire your_domain con il dominio effettivo.
* Accedere a [Pingboard](https://pingboard.com/) usando l'account amministratore.
* Fare clic su Add-Ons (Componenti aggiuntivi) > Integrations (Integrazioni) > Azure Active Directory.
* Fare clic sulla scheda Configure (Configura) e selezionare **Enable user provisioning from Azure** (Abilita provisioning utenti da Azure).
* Copiare il valore del campo **OAuth Bearer Token** (Token di connessione OAuth) e immetterlo nella casella di testo **Secret Token** (Token segreto).

6) Nel portale di Azure fare clic su **Test connessione** per verificare che Azure Active Directory possa connettersi all'app Pingboard. Se la connessione non riesce, verificare che l'account di Pingboard disponga delle autorizzazioni di amministrazione e ripetere il passaggio **Test connessione**.

7) Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

8) Fare clic su **Save**. 

9) Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Pingboard** (Sincronizza utenti di Azure Active Directory in Pingboard).

10) Nella sezione **Mapping attributi** esaminare gli attributi utente che verranno sincronizzati da Azure Active Directory a Pingboard. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Pingboard per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md).

11) Per abilitare il servizio di provisioning di Azure Active Directory per Pingboard, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

12) Fare clic su **Salva** per avviare la sincronizzazione iniziale degli utenti assegnati a Pingboard.

La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning per l'app Pingboard.

Per altre informazioni sulla lettura dei log di provisioning di Azure Active Directory, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-pingboard-tutorial.md)
