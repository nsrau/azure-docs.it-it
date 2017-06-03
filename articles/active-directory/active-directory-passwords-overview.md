---

title: 'Azure AD: panoramica per la reimpostazione password self-service | Documentazione Microsoft'
description: "Cosa può fare la Reimpostazione self-service delle password in Azure AD per l&quot;organizzazione?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: joflore
ms.editor: gahug
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 9f72a4900f20282827ff939aa41bc4f306e547a3
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017


---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Reimpostazione self-service delle password di Azure AD per i professionisti IT

"Self-service" è un termine attualmente in voga diffuso all'interno di molti reparti IT in tutto il mondo con vari significati. Nel mercato si sono diffusi prodotti che permettono di gestire gruppi locali, password o profili utente dal cloud o in locale.

La Reimpostazione self-service delle password (SSPR) di Azure Active Directory (Azure AD) imposta se stessa a distanza con facilità di uso e distribuzione. La Reimpostazione self-service delle password di Azure AD combina un set di funzionalità che:

* Consente agli utenti di gestire la propria password
  * Da qualsiasi dispositivo
  * In qualsiasi posizione
  * In qualsiasi momento
* Mantiene la conformità con i criteri che si definiscono in qualità di amministratore

Se si è pronti, è possibile iniziare a usare la SSPR di Azure AD usando la nostra [guida introduttiva di avvio rapido](active-directory-passwords-getting-started.md) e vedere come gli utenti reimpostano rapidamente le proprie password.

## <a name="what-is-possible"></a>Cosa è possibile

* **Modifica delle password self-service** consente agli utenti finali o agli amministratori di cambiare le password senza l'assistenza dell'amministratore
* Lo **sblocco dell'account self-service** consente agli utenti finali di sbloccare i propri account senza l'assistenza dell'amministratore
* **Reimpostazione delle password self-service** consente agli utenti finali o agli amministratori di reimpostare automaticamente le password senza l'assistenza dell'amministratore. La reimpostazione delle password self-service è disponibile solo in Azure AD Premium o Basic - [Edizioni di Azure Active Directory](active-directory-editions.md).
* La **Reimpostazione delle password avviata dall'amministratore** consente a un amministratore di reimpostare la password di un utente finale o di un altro amministratore dal [portale di Azure](https://docs.microsoft.com/azure/azure-portal-overview)
* I **rapporti sulle attività di gestione delle password** forniscono agli amministratori informazioni dettagliate sulle attività di reimpostazione delle password e di registrazione che si verificano all'interno dell'organizzazione - [Report di gestione](active-directory-passwords-reporting.md)
* **Writeback delle password** permette di gestire le password locali dal cloud, in modo che tutti gli scenari descritti in precedenza possano essere eseguiti da o per conto di utenti federati o con sincronizzazione tramite password. Per il writeback delle password è necessaria una licenza di [Azure AD Premium](active-directory-get-started-premium.md).

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Perché scegliere la Reimpostazione self-service della password di Azure AD

* **Ridurre i costi** : poiché il supporto tecnico e la reimpostazione delle password assistita rappresentano in genere il 20% della spesa IT di un'organizzazione
* **Migliorare le esperienze degli utenti finali** e **ridurre i volumi di supporto tecnico** fornendo agli utenti la possibilità di risolvere i problemi relativi alla propria password in una sola volta senza chiamare un supporto tecnico o aprire una richiesta di supporto.
* **Mobilità dell'unità** poiché gli utenti possono reimpostare le password da qualsiasi posizione.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilità della Reimpostazione self-service della password di Azure AD

La Reimpostazione self-service delle password di Azure AD è disponibile in tre livelli a seconda della sottoscrizione.

* **Azure AD Free** - Gli amministratori di reti solo cloud possono reimpostare la password personale
* **Azure AD Basic** o una **Sottoscrizione di Office365 a pagamento** - Gli amministratori e gli utenti di reti solo cloud possono reimpostare la password personale
* **Azure AD Premium** - qualsiasi utente o amministratore, inclusi gli utenti di reti solo cloud, federati o sincronizzati tramite password, può reimpostare la password personale. Le password locali richiedono che sia abilitato il writeback delle password.

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Reimpostazione self-service delle password di Azure AD, una somma delle parti

La Reimpostazione self-service delle password in Azure AD comprende le componenti seguenti:

* **Portale di configurazione per la gestione delle password** dove è possibile controllare le opzioni per la gestione delle password nel tenant tramite il portale di Azure
* **Portale di registrazione della reimpostazione delle password** dove gli utenti possono eseguire la registrazione automatica per la reimpostazione della password
* **Portale di reimpostazione delle password** dove gli utenti possono reimpostare la password usando le richieste definite dall'amministratore e le risposte fornite dagli utenti
* **Portale di modifica della password dell'utente** dove gli utenti possono modificare la propria password immettendo la vecchia password e fornendone una nuova
* **Report di gestione delle password** dove gli amministratori possono visualizzare e analizzare i rapporti sulle attività delle password per i tenant nel portale di Azure
* **Writeback delle password in locale con Azure AD Connect** consente di abilitare la gestione locale, federata, o gli utenti con password sincronizzate dal cloud

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Prezzi, contratto di servizio, aggiornamenti e guida di orientamento di Azure AD

Altri dettagli su questi argomenti sono disponibili nelle pagine seguenti

* [**Dettagli sui prezzi di Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Prezzi di Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contratti di servizio di Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contratto di servizio per Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Aggiornamenti di Azure**](https://azure.microsoft.com/updates/)
* [**Guida di orientamento di Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - Iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md): configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md): pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md): personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Reporting** ](active-directory-passwords-reporting.md): verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service


