---
title: Panoramica di Enterprise State Roaming | Documentazione Microsoft
description: Fornisce informazioni sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows. Enterprise State Roaming offre agli utenti un&quot;esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo.
services: active-directory
keywords: informazioni su Enterprise State Roaming, sincronizzazione aziendale, cloud windows
documentationcenter: 
author: femila
manager: swadhwa
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: ce78b996a5f45b0b074563bc50af5aaab85f2667
ms.openlocfilehash: 56eacc48102302961132bba6d14838327a899c59


---
# <a name="enterprise-state-roaming-overview"></a>Panoramica di Enterprise State Roaming
Con Windows 10 gli utenti di [Azure Active Directory (Azure AD)](active-directory-whatis.md) ottengono la possibilità di sincronizzare in modo sicuro le proprie impostazioni utente e i dati delle impostazioni dell'applicazione sul cloud. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo. Enterprise State Roaming funziona in modo simile alla [sincronizzazione delle impostazioni degli utenti](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) standard introdotta per la prima volta in Windows 8. Il servizio Enterprise State Roaming offre anche i vantaggi seguenti:

* **Separazione di dati aziendali e dell'utente** : le organizzazioni mantengono i controlli dei propri dati e non esiste alcuna combinazione di dati aziendali in un account cloud di utenti o di dati utente in un account cloud aziendale.
* **Sicurezza migliorata** : i dati vengono crittografati automaticamente prima di lasciare il dispositivo Windows 10 dell'utente mediante Azure Rights Management (Azure RMS) e i dati inattivi rimangono crittografati sul cloud. Tutto il contenuto inattivo rimane crittografato sul cloud, ad eccezione degli spazi dei nomi, ad esempio i nomi delle impostazioni e i nomi delle app Windows.  
* **Servizi di gestione e monitoraggio migliorati** : garantiscono maggiore controllo e maggiore visibilità sugli utenti che sincronizzano le impostazioni nell’organizzazione e sui relativi dispositivi tramite l’integrazione del portale di Azure AD. 

Enterprise State Roaming è disponibile in più aree di Azure. L'elenco aggiornato delle aree disponibili è riportato nella pagina [Servizi di Azure in base all'area](https://azure.microsoft.com/regions/#services) in Azure Active Directory.

| Articolo | Descrizione |
| --- | --- |
| [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Il servizio Enterprise State Roaming è disponibile per qualsiasi organizzazione con una sottoscrizione Premium di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la pagina del [prodotto Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md) |Questo argomento fornisce le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app. |
| [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 offre impostazioni relative a Criteri di gruppo e ai criteri di gestione di dispositivi mobili per limitare la sincronizzazione delle impostazioni. |
| [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Di seguito è riportato un elenco completo di tutte le impostazioni di cui verrà effettuato il roaming e/o il backup in Windows 10. |
| [Risoluzione dei problemi](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Questo argomento illustra alcuni passaggi di base per la risoluzione dei problemi e contiene un elenco di problemi noti. |




<!--HONumber=Jan17_HO1-->


