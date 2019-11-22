---
title: Gestione del provisioning degli utenti per le app aziendali in Azure AD
description: Informazioni su come gestire il provisioning degli account utente per le app aziendali usando Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77cda523582b513669adcafd3a46b6ac02dd99db
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285612"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestione del provisioning degli account utente per le app aziendali nel portale di Azure

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per gestire il provisioning e il deprovisioning automatici degli account utente per le applicazioni che li supportano. Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale

Usare il portale di Azure Active Directory per visualizzare e gestire tutte le applicazioni configurate per Single Sign-On in una directory. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione. Per visualizzare e gestire le applicazioni aziendali, attenersi alla procedura seguente:

1. Aprire il [portale di Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il riquadro delle risorse, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare il **provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.

   ![Schermata di provisioning per gestire le impostazioni di provisioning degli account utente](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modalità di provisioning

Il riquadro **provisioning** inizia con un menu **modalità** , che mostra le modalità di provisioning supportate per un'applicazione aziendale e consente di configurarle. Le opzioni disponibili includono:

* **Automatico** : questa opzione viene visualizzata se Azure ad supporta il provisioning automatico basato su API o il deprovisioning degli account utente in questa applicazione. Selezionare questa modalità per visualizzare un'interfaccia che consente agli amministratori di:

  * Configurare Azure AD per la connessione all'API di gestione degli utenti dell'applicazione
  * Creare mapping di account e flussi di lavoro che definiscono la modalità di flusso dei dati degli account utente tra Azure AD e l'app
  * Gestire il servizio di provisioning Azure AD

* **Manuale** : questa opzione viene visualizzata se Azure ad non supporta il provisioning automatico degli account utente in questa applicazione. In questo caso, i record degli account utente archiviati nell'applicazione devono essere gestiti tramite un processo esterno, in base alle funzionalità di gestione e provisioning degli utenti fornite da tale applicazione (che possono includere il provisioning JIT just-in-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente

Selezionare l'opzione **automatica** per specificare le impostazioni per le credenziali di amministratore, i mapping, l'avvio e l'arresto e la sincronizzazione.

### <a name="admin-credentials"></a>Credenziali di amministratore

Espandere **credenziali amministratore** per immettere le credenziali necessarie per Azure ad connettersi all'API di gestione degli utenti dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](user-provisioning.md).

Selezionare **Test connessione** per testare le credenziali con Azure ad tentare di connettersi all'app di provisioning dell'app usando le credenziali fornite.

### <a name="mappings"></a>Mapping

Espandere **mapping** per visualizzare e modificare gli attributi utente che scorrono tra Azure ad e l'applicazione di destinazione quando viene eseguito il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di mapping tra Azure AD oggetti utente e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Selezionare un mapping nella tabella per aprire l'editor di mapping a destra, dove è possibile visualizzarlo e personalizzarlo.

![Mostra la schermata di mapping degli attributi](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Le personalizzazioni supportate includono:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.
* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtro delle azioni di provisioning eseguite Azure AD sull'applicazione di destinazione. Anziché Azure AD sincronizzare completamente gli oggetti, è possibile limitare l'esecuzione delle azioni.

  Ad esempio, solo selezionare **Aggiorna** e Azure ad Aggiorna solo gli account utente esistenti in un'applicazione, ma non ne crea di nuovi. Solo selezionare **Crea** e Azure crea solo nuovi account utente, ma non aggiorna quelli esistenti. Questa funzionalità consente agli amministratori di creare mapping diversi per i flussi di lavoro di creazione e aggiornamento degli account.

* Aggiunta di un nuovo mapping di attributo. Selezionare **Aggiungi nuovo mapping** nella parte inferiore del riquadro **mapping attributi** . Compilare il modulo **modifica attributo** e selezionare **OK** per aggiungere il nuovo mapping all'elenco.

### <a name="settings"></a>Settings

È possibile avviare e arrestare il servizio di provisioning Azure AD per l'applicazione selezionata nell'area **Impostazioni** della schermata di **provisioning** . È anche possibile scegliere di cancellare la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Questa modifica determina l'esecuzione di un ciclo iniziale da parte del servizio Azure AD provisioning. Vengono letti gli utenti assegnati nella sezione **utenti e gruppi** , viene eseguita una query sull'applicazione di destinazione, quindi vengono eseguite le azioni di provisioning definite nella sezione **mapping** Azure ad. Durante questo processo, il servizio di provisioning archivia i dati memorizzati nella cache sugli account utente che gestisce, quindi gli account non gestiti nelle applicazioni di destinazione che non si trovano mai nell'ambito dell'assegnazione non sono interessati dalle operazioni di deprovisioning. Dopo il ciclo iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo in un intervallo di 40 minuti.

Impostare **stato del provisioning** su **disattivato** per sospendere il servizio di provisioning. In questo stato, Azure non crea, aggiorna o rimuove gli oggetti utente o gruppo nell'app. Ripristinare lo stato **su on** e il servizio preleva il punto da cui è stato interrotto.

**Cancella lo stato corrente e riavvia la sincronizzazione** attiva un ciclo iniziale. Il servizio valuterà quindi tutti gli utenti nel sistema di origine e ne determinerà l'ambito per il provisioning. Questa operazione può essere utile quando l'applicazione è attualmente in quarantena o è necessario apportare una modifica ai mapping degli attributi. Questa operazione non deve essere utilizzata per attivare una richiesta DELETE o Disable perché questi eventi possono essere eliminati quando si attiva uno stato di cancellazione e si riavvia. Il ciclo iniziale richiede inoltre più tempo del ciclo incrementale tipico a causa del numero di oggetti che devono essere valutati. Per ulteriori informazioni sulle prestazioni dei cicli iniziali e incrementali, vedere [qui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 
