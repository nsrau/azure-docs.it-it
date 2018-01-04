---
title: Abilitare Enterprise State Roaming in Azure Active Directory | Microsoft Docs
description: Domande frequenti sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo.
services: active-directory
keywords: enterprise state roaming, cloud windows, come abilitare il servizio enterprise state roaming
documentationcenter: 
author: tanning
manager: mtillman
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.openlocfilehash: 3a8714ddbda10d8e1b4a8de35711101f4c8a0106
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Abilitare Enterprise State Roaming in Azure Active Directory
Enterprise State Roaming è disponibile per qualsiasi organizzazione con una licenza Azure AD Premium o Enterprise Mobility + Security (EMS). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la [pagina del prodotto Azure AD](https://azure.microsoft.com/services/active-directory) .

Quando si abilita il servizio Enterprise State Roaming, all'organizzazione viene concessa automaticamente una licenza gratuita e a uso limitato di Azure Rights Management. Questa sottoscrizione gratuita è limitata a crittografia e decrittografia delle impostazioni aziendali e dei dati delle applicazioni sincronizzati da Enterprise State Roaming. È necessario avere una [sottoscrizione a pagamento](https://azure.microsoft.com/pricing/details/active-directory/) per usare le funzionalità complete di Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Per abilitare Enterprise State Roaming

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/).

2. Selezionare **Azure Active Directory** &gt; **Dispositivi** &gt; **Impostazioni del dispositivo**.

3. Selezionare **Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi**. Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Immagine dell'impostazione del dispositivo Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Per consentire a un dispositivo Windows 10 di usare il servizio Enterprise State Roaming, è necessario che il dispositivo effettui l'autenticazione con un'identità di Azure AD. Per i dispositivi aggiunti ad Azure AD, l'identità di accesso principale dell'utente è l'identità di Azure AD, quindi non sono necessarie operazioni aggiuntive di configurazione. Per i dispositivi che usano Active Directory in locale, l'amministratore IT deve [connettere i dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Archiviazione dei dati
I dati del servizio Enterprise State Roaming sono ospitati in una o più [aree di Azure](https://azure.microsoft.com/regions/) ottimali per il valore relativo al paese/all'area impostati nell'istanza di Azure Active Directory. I dati del servizio Enterprise State Roaming vengono partizionati in base alle tre principali aree geografiche: America del Nord, EMEA e Asia Pacifico. I dati del servizio Enterprise State Roaming per il tenant si trovano in locale con l'area geografica e non vengono replicati tra le aree.  Ad esempio:
Valore di paese/area | dati ospitati in
---------------------|-------------------------
Un paese dell'area EMEA, ad esempio "Francia" o "Zambia" | una delle aree di Azure in Europa 
Un paese dell'America del Nord, ad esempio "Stati Uniti" o "Canada" | una o più aree di Azure all'interno degli Stati Uniti
Un paese dell'area Asia Pacifico (APAC), ad esempio "Australia" o "Nuova Zelanda" | una o più aree di Azure in Asia
Aree America del Sud e Antartide | una o più aree di Azure all'interno degli Stati Uniti

Il valore relativo al paese/all'area viene impostato come parte del processo di creazione della directory Azure AD e non può essere modificato successivamente. Se sono necessarie altre informazioni sulla posizione di archiviazione dei dati, inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Visualizzare lo stato di sincronizzazione dei dispositivi per ogni utente
Seguire questi passaggi per visualizzare un report di stato della sincronizzazione dei dispositivi per ogni utente.

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/).

2. Selezionare **Azure Active Directory** &gt; **Utenti e gruppi** &gt; **Tutti gli utenti**.

3. Selezionare l'utente e quindi selezionare **Dispositivi**.

4. In **Mostra** selezionare **Impostazioni di sincronizzazione dispositivi e dati dell'app aziendali** per mostrare lo stato della sincronizzazione.
  
  ![Immagine dell'impostazione per la sincronizzazione di dispositivi e dati](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Se sono presenti dispositivi inclusi nella sincronizzazione per questo utente, tali dispositivi vengono visualizzati, come illustrato di seguito.
  
  ![Immagine dei dati a colonne relativi alla sincronizzazione dei dispositivi](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Conservazione dei dati
I dati sincronizzati in Azure con Enterprise State Roaming vengono mantenuti fino alla loro eliminazione manuale o fino a quando non viene determinato che non sono aggiornati. 

### <a name="explicit-deletion"></a>Eliminazione esplicita
I dati vengono eliminati in modo esplicito quando un amministratore di Azure elimina un utente o una directory oppure quando richiede esplicitamente l'eliminazione dei dati in altro modo.

* **Eliminazione dell'utente**: quando un utente viene eliminato in Azure AD, i dati mobili dell'account utente vengono eliminati dopo 90-180 giorni. 
* **Eliminazione della directory**: l'eliminazione di un'intera directory in Azure AD è un'operazione immediata. Tutti i dati delle impostazioni associati con quella directory vengono eliminati dopo 90-180 giorni. 
* **Eliminazione su richiesta**: se l'amministratore di Azure AD vuole eliminare manualmente i dati delle impostazioni o di un utente specifico, può inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminazione dei dati non aggiornati
I dati a cui non è stato eseguito l'accesso per un anno ("periodo di conservazione") verranno considerati non aggiornati e potrebbero essere eliminati da Azure. Il periodo di conservazione è soggetto a modifiche ma non sarà inferiore a 90 giorni. I dati obsoleti possono essere un set specifico di impostazioni di Windows/dell'applicazione o tutte le impostazioni per un utente. Ad esempio: 

* Se nessun dispositivo accede a una raccolta specifica di impostazioni, ad esempio se un'applicazione viene rimossa dal dispositivo o un gruppo di impostazioni come "Tema" viene disabilitato per tutti i dispositivi di un utente, la raccolta diventa obsoleta dopo il periodo di conservazione e potrebbe essere eliminata. 
* Se un utente ha disattivato la sincronizzazione delle impostazioni in tutti i propri dispositivi, non verrà effettuato l'accesso ad alcun dato delle impostazioni e tutti i dati delle impostazioni per tale utente diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 
* Se l'amministratore della directory di Azure AD disattiva il servizio Enterprise State Roaming per l'intera directory, tutti gli utenti in tale directory non sincronizzeranno più le impostazioni e tutti i dati delle impostazioni per tutti gli utenti diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 

### <a name="deleted-data-recovery"></a>Recupero di dati eliminati
Il criterio relativo alla conservazione dei dati non può essere configurato. I dati eliminati in modo definitivo non sono recuperabili. Tuttavia, i dati delle impostazioni vengono eliminati solo da Azure e non dal dispositivo dell'utente finale. Se un dispositivo si riconnette successivamente al servizio Enterprise State Roaming, le impostazioni vengono sincronizzate e archiviate di nuovo in Azure.

## <a name="related-topics"></a>Argomenti correlati
* [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Risoluzione dei problemi](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
