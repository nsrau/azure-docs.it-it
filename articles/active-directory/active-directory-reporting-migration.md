---
title: "Come trovare report attività nel Portale di Azure | Microsoft Docs"
description: "Informazioni su come trovare i report attività nel Portale di Azure"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Come trovare i report attività nel Portale di Azure

Con la migrazione dal Portale di Azure classico al Portale di Azure, presentiamo un nuovo look per i log attività di Azure Active Directory. Un paio di mesi fa, Microsoft ha rilasciato un [post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) che spiega come presentiamo i log attività nel contesto della risorsa a cui si sta lavorando. Questo articolo descrive come i report esistenti nel Portale di Azure classico sono stati convertiti nel nuovo mondo del Portale di Azure.

## <a name="what-is-new"></a>Novità

I report nel Portale di Azure classico sono suddivisi in diverse categorie:

1.    Report sulla sicurezza
2.    Report attività
3.    Report app integrate

### <a name="activity-and-integrated-app-reports"></a>Report attività e app integrate

Passando alla creazione di report basata sul contesto nel Portale di Azure, abbiamo unito i report esistenti in una singola visualizzazione con una singola API sottostante che fornisce i dati per la visualizzazione. È possibile trovare questa visualizzazione in "*Attività*" > Log di controllo nel Portale di Azure (spostamento a sinistra). Di seguito sono indicati i report che sono stati riuniti in questa vista.

-    Report di controllo

-     Attività di reimpostazione password

-     Attività di registrazione reimpostazione password

-     Attività dei gruppi self-service

-     Modifiche del nome del gruppo di Office&365;

-     Attività di provisioning dell'account

-     Stato rollover della password
-     Errori di provisioning dell'account


Il report di utilizzo applicazioni è stato migliorato e incluso in una visualizzazione denominata "*Attività*"-> Accessi (spostamento a sinistra) e include gli accessi di tutti gli utenti, che, a loro volta possono essere derivati per ottenere le informazioni sull'utilizzo dell'applicazione. È possibile trovare informazioni sull'utilizzo di App tramite la schermata di panoramica "Applicazioni aziendali".

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>Come accedere a un report specifico in questa vista singola?

### <a name="audit-logs"></a>Log di controllo

Una delle richieste chiave di molti clienti è stata la possibilità di avere più opzioni di filtro per accedere ai log attività all'interno di Azure AD. Invece, abbiamo proposto un meccanismo avanzato di applicazione filtri per filtrare i dati desiderati. Uno dei filtri presentati è chiamato "*Categoria attività*" ed elenca i diversi tipi di registri di attività forniti da Azure AD. Selezionando la categoria desiderata, è possibile restringere i risultati dei log attività a quelli che si desidera visualizzare. 

Ad esempio, se si è interessati solo alle attività relative a **Reimpostazione password self-service**, è possibile scegliere la categoria **Self-service Password Management** (Gestione delle password self-service). Le categorie che è possibile visualizzare sono nel contesto della risorsa a cui si sta lavorando.  


![Log di controllo](./media/active-directory-reporting-migration/06.png "Log di controllo")

 
Le diverse categorie attuali includono:

- Directory principale

- Gestione delle password self-service

- Gestione gruppi self-service

- Provisioning degli account

### <a name="application-usage"></a>Utilizzo applicazioni

È possibile visualizzare l'utilizzo dell'applicazione per tutte le app o per una singola app attraverso la vista Attività-> Accessi. Come illustrato di seguito, questa vista è presente per tutte le applicazioni o per una singola applicazione. Se si desidera limitare i risultati, è possibile filtrare in base al nome utente o al nome dell'applicazione.
 

![Log di controllo](./media/active-directory-reporting-migration/07.png "Log di controllo")


### <a name="security-reports"></a>Report sulla sicurezza

I report di sicurezza sono stati consolidati per fornire una visualizzazione di tutti i segnali di rischio che Azure Active Directory può rilevare e segnalare.

Questa vista consolidata contiene dati relativi a:

- Utenti a rischio
- Accessi a rischio 


![Log di controllo](./media/active-directory-reporting-migration/04.png "Log di controllo")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Report sulle attività nel Portale di Azure classico rispetto al Portale di Azure

Questa sezione elenca i report esistenti nel Portale di Azure classico e le modalità per ottenere le stesse informazioni nel Portale di Azure.

**Log di controllo:**

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Core directory** (Directory principale) 

**Attività di reimpostazione password:**

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Self service Password Management** (Gestione delle password self-service) 

**Attività di registrazione reimpostazione password:**    

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service).

**Attività dei gruppi self-service:**    

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Gestione gruppi self-service**

**Modifiche del nome del gruppo di Office&365;:**

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service)
3. Come **Activity Resource Type** (Tipo di risorsa attività) selezionare **Gruppo** 
4. Come **Origine attività** selezionare **O365 groups** (Gruppi di O365) 

**Attività di provisioning dell'account:**    

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account)

**Stato rollover della password:**    

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Automatic App Password Rollover** (Rollover password app automatico)

**Errori di provisioning dell'account:**

1. nel riquadro di spostamento a sinistra fare clic su **Attività** e quindi su **Log di controllo**
2. Come **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account)

**Utilizzo applicazioni:**

- nel riquadro di spostamento a sinistra, fare clic su **Applicazioni aziendali**, quindi scegliere **Accessi**



