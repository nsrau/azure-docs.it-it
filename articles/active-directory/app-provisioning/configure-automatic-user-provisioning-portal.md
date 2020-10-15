---
title: Gestione del provisioning degli utenti per le app aziendali in Azure AD
description: Informazioni su come gestire il provisioning degli account utente per le app aziendali usando Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9c42a83b4f7f3c6b5ff501525a04ebd96c2a692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88234840"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestione del provisioning degli account utente per le app aziendali nel portale di Azure

Questo articolo descrive i passaggi generali per gestire il provisioning e il deprovisioning automatici degli account utente per le applicazioni che li supportano. Il *provisioning degli account utente* è l'atto di creazione, aggiornamento e/o la disabilitazione di record di account utente nell'archivio di profili utente locale di un'applicazione. La maggior parte delle applicazioni cloud e SaaS archivia i ruoli e le autorizzazioni utente nell'archivio di profili utente locale dell'utente e la presenza di tali record utente nell'archivio locale dell'utente è *necessaria* per il funzionamento degli accessi e del Single Sign-On. Per altre informazioni sul provisioning automatico degli account utente, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) offre una raccolta contenente migliaia di applicazioni preintegrate, abilitate per il provisioning automatico con Azure AD. Per iniziare, trovare l'esercitazione relativa alla configurazione del provisioning specifica della propria applicazione nell'[elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](../saas-apps/tutorial-list.md). È probabile che siano disponibili istruzioni dettagliate per la configurazione sia dell'app che di Azure AD per creare la connessione di provisioning.

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale

Usare il portale di Azure Active Directory per visualizzare e gestire tutte le applicazioni configurate per l'accesso Single Sign-On in una directory. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione. Per visualizzare e gestire le applicazioni aziendali, seguire questa procedura:

1. Aprire il [portale di Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Verrà visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il relativo riquadro delle risorse, in cui è possibile visualizzare i report e gestire le impostazioni delle app.
1. Selezionare **Provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.

   ![Schermata Provisioning per la gestione delle impostazioni di provisioning degli account utente](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modalità di provisioning

Il riquadro **Provisioning** inizia con un menu **Modalità**, che mostra le modalità di provisioning supportate per un'applicazione aziendale e ne consente la configurazione. Le opzioni disponibili includono:

* **Automatica**: questa opzione viene visualizzata se Azure AD supporta il provisioning o il deprovisioning automatico basato sulle API per gli account utente in questa applicazione. Selezionare questa modalità per visualizzare un'interfaccia che consenta agli amministratori di:

  * Configurare Azure AD per la connessione all'API di gestione degli utenti dell'applicazione
  * Creare mapping degli account e flussi di lavoro che definiscono la modalità di flusso dei dati degli account utente tra Azure AD e l'app
  * Gestire il servizio di provisioning di Azure AD

* **Manuale**: questa opzione viene visualizzata se Azure AD non supporta il provisioning automatico di account utente in questa applicazione. In questo caso, i record relativi agli account utente archiviati nell'applicazione devono essere gestiti usando un processo esterno, in base alle funzionalità di gestione e provisioning degli utenti fornite dall'applicazione, che possono includere il provisioning just-in-time SAML.

## <a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente

Selezionare l'opzione **Automatica** per specificare le impostazioni per le credenziali di amministratore, i mapping, l'avvio e l'arresto, nonché la sincronizzazione.

### <a name="admin-credentials"></a>Credenziali di amministratore

Espandere **Credenziali amministratore** per immettere le credenziali necessarie ad Azure AD per la connessione all'API di gestione degli utenti dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](user-provisioning.md).

Selezionare **Test connessione** per testare le credenziali mediante un tentativo di connessione di Azure AD all'app di provisioning dell'app con le credenziali fornite.

### <a name="mappings"></a>Mapping

Espandere **Mapping** per visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione durante il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di mapping tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Selezionare un mapping nella tabella per aprire l'editor di mapping a destra, dove è possibile visualizzarlo e personalizzarlo.

![Mostra la schermata Mapping attributi](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Le personalizzazioni supportate includono:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.
* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](customize-application-attributes.md#understanding-attribute-mapping-types).
* Applicazione di un filtro sulle azioni di provisioning che Azure AD esegue nell'applicazione di destinazione. Invece di fare in modo che Azure AD esegua la sincronizzazione completa degli oggetti, è possibile limitare le azioni eseguite.

  Selezionando ad esempio solo **Aggiorna**, Azure AD aggiornerà solo gli account utente esistenti in un'applicazione, senza crearne di nuovi. Selezionando solo **Crea**, Azure creerà solo nuovi account utente, senza aggiornare quelli esistenti. Questa funzionalità consente agli amministratori di creare diversi mapping per i flussi di lavoro di creazione e aggiornamento degli account.

* Aggiunta di un nuovo mapping di attributi. Selezionare **Aggiungi nuovo mapping** nella parte inferiore del riquadro **Mapping attributi**. Compilare il modulo **Modifica attributo** e selezionare **OK** per aggiungere il nuovo mapping all'elenco.

### <a name="settings"></a>Impostazioni

Nell'area **Impostazioni** della schermata **Provisioning** è possibile avviare e arrestare il servizio di provisioning di Azure AD per l'applicazione selezionata. È anche possibile scegliere di cancellare la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Questa modifica determina l'esecuzione di un ciclo iniziale da parte del servizio di provisioning di Azure AD. Il servizio legge gli utenti assegnati nella sezione **Utenti e gruppi**, esegue query nell'applicazione di destinazione per ricercare tali utenti e quindi esegue le azioni di provisioning definite nella sezione **Mapping** di Azure AD. Durante questo processo il servizio di provisioning archivia i dati memorizzati nella cache relativi agli account utente gestiti, in modo che gli account non gestiti all'interno delle applicazioni di destinazione non inclusi nell'ambito dell'assegnazione non siano interessati dalle operazioni di deprovisioning. Dopo il ciclo iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo a intervalli di quaranta minuti.

Impostare **Stato del provisioning** su **No** per sospendere il servizio di provisioning. In questo stato Azure non crea, aggiorna né rimuove oggetti utente o gruppo nell'app. Impostare lo stato di nuovo su **Sì** e il servizio ripartirà dal punto in cui si era interrotto.

**Cancella lo stato corrente e riavvia la sincronizzazione** attiva un ciclo iniziale. Il servizio valuterà quindi tutti gli utenti nel sistema di origine e determinerà se sono inclusi nell'ambito per il provisioning. Questa operazione può essere utile quando l'applicazione è attualmente in quarantena o è necessario apportare una modifica ai mapping degli attributi. Si noti che il completamento del ciclo iniziale richiede più tempo rispetto al ciclo incrementale tipico a causa del numero di oggetti che devono essere valutati. Altre informazioni sulle prestazioni dei cicli iniziali e incrementali sono disponibili [qui](application-provisioning-when-will-provisioning-finish-specific-user.md).