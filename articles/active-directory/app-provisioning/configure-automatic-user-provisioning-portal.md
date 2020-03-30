---
title: Gestione del provisioning degli utenti per le app aziendali in Azure ADUser provisioning management for enterprise apps in Azure AD
description: Informazioni su come gestire il provisioning degli account utente per le app aziendali usando Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264128"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestione del provisioning degli account utente per le app aziendali nel portale di Azure

Questo articolo descrive i passaggi generali per la gestione del provisioning automatico degli account utente e del deprovisioning per le applicazioni che lo supportano. Il *provisioning degli account utente* è l'atto di creazione, aggiornamento e/o la disabilitazione di record di account utente nell'archivio di profili utente locale di un'applicazione. La maggior parte delle applicazioni cloud e SaaS archiviano il ruolo utente e le autorizzazioni nell'archivio dei profili utente locale dell'utente e la presenza di tale record utente nell'archivio locale dell'utente è *necessaria* per l'accesso Single Sign-On e l'accesso al lavoro. Per altre informazioni sul provisioning automatico degli account utente, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS con Azure Active Directory.](user-provisioning.md)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) include una raccolta che contiene migliaia di applicazioni preintegrate abilitate per il provisioning automatico con Azure AD. È consigliabile iniziare individuando l'esercitazione di configurazione del provisioning specifica per l'applicazione [nell'elenco delle esercitazioni su come integrare le app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). È probabile che siano disponibili istruzioni dettagliate per la configurazione dell'app e di Azure AD per la creazione della connessione di provisioning.

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale

Usare il portale di Azure Active Directory per visualizzare e gestire tutte le applicazioni configurate per l'accesso Single Sign-On in una directory. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione. Per visualizzare e gestire le applicazioni aziendali, attenersi alla seguente procedura:

1. Aprire il portale di [Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il relativo riquadro delle risorse, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare **Provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.

   ![Schermata di provisioning per gestire le impostazioni di provisioning degli account utente](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modalità di provisioning

Il riquadro **Provisioning** inizia con un menu **Modalità,** che mostra le modalità di provisioning supportate per un'applicazione aziendale e consente di configurarle. Le opzioni disponibili includono:

* **Automatico:** questa opzione viene visualizzata se Azure AD supporta il provisioning automatico basato su API o il deprovisioning degli account utente in questa applicazione. Selezionare questa modalità per visualizzare un'interfaccia che consente agli amministratori di:

  * Configurare Azure AD per la connessione all'API di gestione degli utenti dell'applicazioneConfigure Azure AD to connect to the application's user management API
  * Creare mapping di account e flussi di lavoro che definiscono il flusso dei dati dell'account utente tra Azure AD e l'appCreate account mappings and workflows that define how user account data should flow between Azure AD and the app
  * Gestire il servizio di provisioning di Azure ADManage the Azure AD provisioning service

* **Manuale:** questa opzione viene visualizzata se Azure AD non supporta il provisioning automatico degli account utente in questa applicazione. In questo caso, i record dell'account utente archiviati nell'applicazione devono essere gestiti tramite un processo esterno, in base alle funzionalità di gestione e provisioning degli utenti fornite da tale applicazione (che può includere il provisioning Just-In-Time SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente

Selezionare l'opzione **Automatico** per specificare le impostazioni per le credenziali di amministratore, i mapping, l'avvio, l'arresto e la sincronizzazione.

### <a name="admin-credentials"></a>Credenziali di amministratore

Espandere **Credenziali di amministratore** per immettere le credenziali necessarie per la connessione di Azure AD all'API di gestione degli utenti dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](user-provisioning.md).

Selezionare **Test connessione** per testare le credenziali facendo in modo che Azure AD tenti di connettersi all'app di provisioning dell'app usando le credenziali fornite.

### <a name="mappings"></a>Mapping

Espandere **Mapping** per visualizzare e modificare gli attributi utente che passano tra Azure AD e l'applicazione di destinazione quando viene eseguito il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di mapping tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Selezionare un mapping nella tabella per aprire l'editor di mapping a destra, in cui è possibile visualizzarli e personalizzarli.

![Mostra la schermata Mappatura attributi](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Le personalizzazioni supportate includono:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.
* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtraggio delle azioni di provisioning eseguite da Azure AD nell'applicazione di destinazione. Anziché fare in modo che Azure AD eseri completamente sincronizza gli oggetti, è possibile limitare l'esecuzione delle azioni.

  Ad esempio, selezionare Solo **Aggiorna** e Azure AD aggiorna solo gli account utente esistenti in un'applicazione, ma non ne crea di nuovi. Selezionare **Crea** solo e Azure crea solo nuovi account utente ma non aggiorna quelli esistenti. Questa funzionalità consente agli amministratori di creare mapping diversi per la creazione e l'aggiornamento dei flussi di lavoro degli account.

* Aggiunta di un nuovo mapping di attributi. Selezionare **Aggiungi nuovo mapping** nella parte inferiore del riquadro Mapping **attributi.** Compilare il modulo **Modifica attributo** e selezionare **OK** per aggiungere il nuovo mapping all'elenco.

### <a name="settings"></a>Impostazioni

È possibile avviare e arrestare il servizio di provisioning di Azure AD per l'applicazione selezionata nell'area **Impostazioni** della schermata **Provisioning.** È anche possibile scegliere di cancellare la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Questa modifica fa sì che il servizio di provisioning di Azure AD eserirà un ciclo iniziale. Legge gli utenti assegnati nella sezione **Utenti e gruppi,** esegue una query sull'applicazione di destinazione e quindi esegue le azioni di provisioning definite nella sezione **Mapping** di Azure AD. Durante questo processo, il servizio di provisioning archivia i dati memorizzati nella cache relativi agli account utente che gestisce, in modo che gli account non gestiti all'interno delle applicazioni di destinazione che non sono mai stati nell'ambito per l'assegnazione non sono interessati dalle operazioni di deprovisioning. Dopo il ciclo iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo in base a un intervallo di quaranta minuti.

Impostare **Stato provisioning su** **Disattivato** per sospendere il servizio di provisioning. In questo stato, Azure non crea, aggiorna o rimuove oggetti utente o gruppo nell'app. Impostare nuovamente lo stato su **Attivato** e il servizio riprende dal punto in cui era stato interrotto.

**Cancella stato corrente e la sincronizzazione del riavvio** attiva un ciclo iniziale. Il servizio valuterà nuovamente tutti gli utenti nel sistema di origine e determinerà se sono nell'ambito del provisioning. Ciò può essere utile quando l'applicazione è attualmente in quarantena o è necessario apportare una modifica ai mapping degli attributi. Si noti che il ciclo iniziale richiede più tempo rispetto al ciclo incrementale tipico a causa del numero di oggetti che devono essere valutati. Ulteriori informazioni sulle prestazioni dei cicli iniziali e incrementali [sono disponibili qui](application-provisioning-when-will-provisioning-finish-specific-user.md). 
