---
title: Gestione del provisioning di utenti per le app aziendali con Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 6bf265f51f2fea16f90dd0bcf2891bd9bed5cef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963583"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestione del provisioning degli account utente per le app aziendali nel portale di Azure

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per gestire il provisioning e il deprovisioning automatici degli account utente per le applicazioni che li supportano. Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Individuazione delle app nel portale

Usare il portale di Azure Active Directory per visualizzare e gestire tutte le applicazioni che sono configurate per single sign-on in una directory. Le app aziendali sono app distribuite e usate all'interno dell'organizzazione. Seguire questi passaggi per visualizzare e gestire le applicazioni aziendali:

1. Aprire il [portale di Azure Active Directory](https://aad.portal.azure.com).

1. Selezionare **applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app che sono state aggiunte dalla raccolta.

1. Selezionare un'app per caricare il riquadro di risorse, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.

1. Selezionare **Provisioning** per gestire le impostazioni per l'app selezionata di provisioning dell'account utente.

   ![Riquadro della risorsa dell'applicazione](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modalità di provisioning

Il **Provisioning** riquadro inizia con un **modalità** menu che mostra le modalità di provisioning supportate per un'applicazione aziendale e consente di configurarle. Le opzioni disponibili includono:

* **Automatica** -questa opzione viene visualizzata se Azure AD supporta automatico basato su API di provisioning o deprovisioning degli account utente per questa applicazione. Selezionare questa modalità per visualizzare un'interfaccia che consente agli amministratori di:

  * Configurare Azure AD per connettersi all'API di gestione di utenti dell'applicazione
  * Creare i mapping di account e i flussi di lavoro che definiscono come dati dell'account utente devono essere trasmessi tra Azure AD e l'app
  * Gestire il servizio di provisioning di Azure AD

* **Manual** -questa opzione viene visualizzata se Azure AD non supporta il provisioning automatico degli account utente per questa applicazione. In questo caso, l'account utente di record memorizzati nell'applicazione deve essere gestito tramite un processo esterno, basato sulle funzionalità di gestione e provisioning di utente fornita dall'applicazione (che può includere il provisioning JIT SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente

Selezionare il **automatica** opzione per specificare le impostazioni per la sincronizzazione, i mapping, avvio e arresto e le credenziali di amministratore.

### <a name="admin-credentials"></a>Credenziali di amministratore

Espandere **credenziali amministratore** per immettere le credenziali necessarie per Azure AD per la connessione all'API di gestione utente dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](user-provisioning.md).

Selezionare **Test connessione** per testare le credenziali facendo in modo che Azure si tenta di AD di connettersi all'app di provisioning dell'app usando le credenziali specificate.

### <a name="mappings"></a>Mapping

Espandere **mapping** per visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione quando gli account utente vengono effettuato il provisioning o aggiornati.

È presente un set preconfigurato di mapping tra gli oggetti utente di ogni app SaaS e gli oggetti utente di Azure AD. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Selezionare un mapping nella tabella per aprire l'editor di mapping a destra, in cui è possibile visualizzare e personalizzarle.

![Riquadro della risorsa dell'applicazione](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Le personalizzazioni supportate includono:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.
* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrare le azioni di provisioning che Azure AD esegue nell'applicazione di destinazione. Invece di avere Azure AD completamente sincronizzare gli oggetti, è possibile limitare le azioni di esecuzione. 

  Ad esempio, selezionare solo **Update** e Azure AD Aggiorna solo utente esistente, gli account in un'applicazione ma non crearne uno nuovo. Selezionare solo **Create** e Azure solo consente di creare nuovi account utente senza aggiorna quelli esistenti. Questa funzionalità consente agli amministratori di creare diversi mapping per la creazione dell'account e aggiornare i flussi di lavoro.

* Aggiunta di un nuovo mapping di attributo. Selezionare **Aggiungi nuovi Mapping** in fondo il **Mapping degli attributi** riquadro. Compilare il **Modifica attributo** formano e selezionare **Ok** per aggiungere il nuovo mapping all'elenco. 

### <a name="settings"></a>Impostazioni

È possibile avviare e arrestare il servizio per l'applicazione selezionata nel provisioning di Azure AD la **le impostazioni** area della **Provisioning** dello schermo. È anche possibile scegliere di cancellare la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Questa modifica fa sì che il provisioning di Azure AD del servizio eseguire una sincronizzazione iniziale. Legge gli utenti assegnati nel **utenti e gruppi** sezione, l'applicazione di destinazione per loro una query e quindi esegue le azioni di provisioning definite in Azure AD **mapping** sezione. Durante questo processo, il servizio di provisioning archivia i dati memorizzati nella cache relativi agli account utente gestiti, in modo che gli account non gestiti all'interno dell'applicazione di destinazione che non inclusi nell'ambito per l'assegnazione non sono interessati da operazioni di deprovisioning. Dopo la sincronizzazione iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo a intervalli di dieci minuti.

Modifica il **stato del Provisioning** al **Off** per sospendere il servizio di provisioning. In questo stato Azure non creare, aggiornare o rimuovere eventuali oggetti utente o gruppo nell'app. Modifica lo stato al **su** e il servizio rileva in cui è stata interrotta.

Selezionare il **Cancella lo stato corrente e riavviare la sincronizzazione** casella di controllo e selezionare **salvare** per:

* Arrestare il servizio di provisioning
* Eseguire il dump di dati memorizzati nella cache sugli account gestiti da Gestione di Azure AD
* Riavviare i servizi ed eseguire nuovamente la sincronizzazione iniziale

Questa opzione consente agli amministratori di avviare il processo di distribuzione provisioning soggiacente.

### <a name="synchronization-details"></a>Dettagli sincronizzazione

In questa sezione fornisce dettagli aggiuntivi sull'operazione del servizio di provisioning, tra cui i tempi e il cognome che è stato eseguito il servizio di provisioning per l'applicazione e quanti oggetti utente e gruppo che gestisce.

Viene fornito un collegamento per il **report sull'attività di Provisioning**, che fornisce un log di tutti gli utenti e gruppi creati, aggiornati e rimossi tra Azure AD e l'applicazione di destinazione. Viene inoltre fornito un collegamento per il **segnalazione errori di Provisioning**, che fornisce ulteriori messaggi di errore per oggetti utente e gruppo che non è riuscito a leggere, creato, aggiornato o rimosso.
