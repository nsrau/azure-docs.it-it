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
ms.sourcegitcommit: afd48a77faeeab6d5a8730934345cb7000a59831
ms.openlocfilehash: e72e49140aaad55a6e66a9f331ce7fde20b8577c
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Come trovare i report attività nel Portale di Azure

Con la migrazione dal Portale di Azure classico al Portale di Azure, presentiamo un nuovo look per i log attività di Azure Active Directory. Un paio di mesi fa, Microsoft ha rilasciato un [post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) che spiega come presentiamo i log attività nel contesto della risorsa a cui si sta lavorando. Questo articolo descrive come i report esistenti nel Portale di Azure classico sono stati convertiti nel nuovo mondo del Portale di Azure.

## <a name="what-is-new"></a>Novità

I report nel Portale di Azure classico sono suddivisi in diverse categorie:

1.    Report sulla sicurezza
2.    Report attività
3.    Report app integrate

### <a name="activity-and-integrated-app-reports"></a>Report attività e app integrate

Passando alla creazione di report basata sul contesto nel Portale di Azure, abbiamo unito i report esistenti in una singola visualizzazione con una singola API sottostante che fornisce i dati per la visualizzazione. 

È possibile trovare questa visualizzazione in **Log di controllo** nella sezione **Attività** del pannello **Azure Active Directory**.


![Log di controllo](./media/active-directory-reporting-migration/482.png "Log di controllo")








Di seguito sono indicati i report che sono stati riuniti in questa vista.

-    Report di controllo

-     Attività di reimpostazione password

-     Attività di registrazione reimpostazione password

-     Attività dei gruppi self-service

-     Modifiche del nome del gruppo di Office&365;

-     Attività di provisioning dell'account

-     Stato rollover della password
-     Errori di provisioning dell'account


Il report Utilizzo delle applicazioni è stato migliorato e incluso in una visualizzazione denominata **Accessi**. È possibile trovare questa visualizzazione nella sezione **Attività** del pannello **Azure Active Directory**.


![Log di controllo](./media/active-directory-reporting-migration/483.png "Log di controllo")

Questa visualizzazione include tutti gli accessi degli utenti, che possono essere a loro volta derivati per ottenere le informazioni sull'utilizzo dell'applicazione. Le informazioni sull'utilizzo dell'app sono disponibili anche tramite la panoramica **Applicazioni aziendali**.

![Log di controllo](./media/active-directory-reporting-migration/484.png "Log di controllo")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>Come accedere a un report specifico in questa vista singola?

### <a name="audit-logs"></a>Log di controllo

Una delle richieste chiave di molti clienti è stata la possibilità di avere più opzioni di filtro per accedere ai log attività all'interno di Azure AD. Invece, abbiamo proposto un meccanismo avanzato di applicazione filtri per filtrare i dati desiderati. Uno dei filtri forniti è denominato **Categoria attività** ed elenca i diversi tipi di log attività disponibili in Azure AD. Scegliendo la categoria preferita, è possibile limitare i risultati e visualizzare solo quelli necessari. 

Ad esempio, se si è interessati solo alle attività relative a **Reimpostazione password self-service**, è possibile scegliere la categoria **Self-service Password Management** (Gestione delle password self-service). Le categorie che è possibile visualizzare sono nel contesto della risorsa a cui si sta lavorando.  


![Log di controllo](./media/active-directory-reporting-migration/06.png "Log di controllo")

 
Le diverse categorie attuali includono:

- Directory principale

- Gestione delle password self-service

- Gestione gruppi self-service

- Provisioning degli account

### <a name="application-usage"></a>Utilizzo applicazioni

È possibile visualizzare l'utilizzo dell'applicazione per tutte le app o per una singola app attraverso la visualizzazione **Attività-> Accessi**. Come illustrato di seguito, questa vista è presente per tutte le applicazioni o per una singola applicazione. Per limitare i risultati, è possibile filtrare in base a **Nome utente** o **Nome applicazione**.
 

![Log di controllo](./media/active-directory-reporting-migration/07.png "Log di controllo")


### <a name="security-reports"></a>Report sulla sicurezza

I report sulla sicurezza sono stati consolidati in modo da fornire una panoramica completa di una visualizzazione di tutti gli eventi di rischio correlati alla sicurezza che Azure Active Directory può rilevare e segnalare. Per una panoramica completa, vedere [Eventi di rischio di Azure Active Directory](active-directory-identity-protection-risk-events.md).  
In questo argomento è disponibile una panoramica del modo in cui i report relativi alle attività anomale di Azure Active Directry sono mappati agli eventi di rischio in Azure AD nella sezione [Report relativi alle attività anomale di Azure AD](active-directory-identity-protection-risk-events.md#azure-ad-anomalous-activity-reports).

Nel portale di Azure è possibile accedere ai report sugli eventi di rischio rilevati nella sezione **Sicurezza** del pannello **Azure Active Directory**. Gli eventi di rischio rilevati vengono registrati nei report seguenti:   

- Utenti a rischio
- Accessi a rischio 


![Log di controllo](./media/active-directory-reporting-migration/04.png "Log di controllo")

Per altre informazioni su questi report, vedere:

- [Report di sicurezza per gli utenti a rischio nel portale di Azure Active Directory - Anteprima](active-directory-reporting-security-user-at-risk.md)
- [Report degli accessi a rischio nel portale di Azure Active Directory - Anteprima](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Report sulle attività nel Portale di Azure classico rispetto al Portale di Azure

Questa sezione elenca i report esistenti nel Portale di Azure classico e le modalità per ottenere le stesse informazioni nel Portale di Azure.


Il punto di ingresso a tutti i dati di controllo è **Log di controllo** nella sezione **Attività** del pannello **Azure Active Directory**.

![Log di controllo](./media/active-directory-reporting-migration/61.png "Log di controllo")


| portale di Azure classico                 | Procedure del portale di Azure                                                         |
| ---                                  | ---                                                                        |
| Log di controllo                           | Come **Categoria attività** selezionare **Core directory** (Directory principale).                       |
| Attività di reimpostazione password              | Come **Categoria attività** selezionare **Self service Password Management** (Gestione delle password self-service). | 
| Attività di registrazione reimpostazione password | Come **Categoria attività** selezionare **Self Service Password Management** (Gestione delle password self-service).     |
| Attività dei gruppi self-service         | Come **Categoria attività** selezionare **Gestione gruppi self-service**.        |
| Attività di provisioning dell'account        | Come **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account).         |
| Stato rollover della password             | Come **Categoria attività** selezionare **Automatic App Password Rollover** (Rollover password app automatico).      |
| Errori di provisioning dell'account          | Come **Categoria attività** selezionare **Account User Provisioning** (Provisioning dell'utente account).        |
| Modifiche del nome del gruppo di Office&365;         | Come **Categoria attività** selezionare **Self service Password Management** (Gestione delle password self-service), come **Tipo di risorsa attività** selezionare **Gruppo** e come **Origine attività** selezionare **Gruppi di Office&365;**.|

 

Il punto di ingresso per il report **Utilizzo delle applicazioni** è **Azure Active Directory > Applicazioni aziendali > Accessi**. 


![Log di controllo](./media/active-directory-reporting-migration/199.png "Log di controllo")


