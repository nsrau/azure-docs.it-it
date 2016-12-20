---
title: Abilitare Enterprise State Roaming in Azure Active Directory | Microsoft Docs
description: Domande frequenti sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows. Enterprise State Roaming offre agli utenti un&quot;esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo.
services: active-directory
keywords: enterprise state roaming, cloud windows, come abilitare il servizio enterprise state roaming
documentationcenter: 
author: femila
manager: swadhwa
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7b7cc0fda8ee2acb417052b501afb0e6e3d0a199


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Abilitare Enterprise State Roaming in Azure Active Directory
Il servizio Enterprise State Roaming è disponibile per qualsiasi organizzazione con una sottoscrizione Premium di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la [pagina del prodotto Azure AD](https://azure.microsoft.com/services/active-directory).

Quando si abilita il servizio Enterprise State Roaming, all'organizzazione verranno concesse automaticamente licenze per una sottoscrizione gratuita e a uso limitato di Azure Rights Management. Questa sottoscrizione gratuita è limitata alla crittografia e decrittografia dei dati delle applicazioni e delle impostazioni aziendali sincronizzati dal servizio Enterprise State Roaming. Per usare le funzionalità complete di Azure Rights Management, è necessario avere una sottoscrizione a pagamento.

Dopo avere ottenuto una sottoscrizione Premium di Azure AD, seguire questa procedura per abilitare il servizio Enterprise State Roaming:

1. Accedere al portale di Azure classico.
2. Nella parte sinistra selezionare **ACTIVE DIRECTORY**, quindi selezionare la directory per cui si vuole abilitare il servizio Enterprise State Roaming.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Passare alla scheda **CONFIGURA** nella parte superiore.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. Scorrere verso il basso e selezionare **GLI UTENTI POSSONO SINCRONIZZARE LE IMPOSTAZIONI E I DATI DELLE APP AZIENDALI**, quindi fare clic su **SALVA**.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Per consentire a un dispositivo Windows 10 di eseguire il roaming delle impostazioni con il servizio Enterprise State Roaming, è necessario che il dispositivo effettui l'autenticazione con un'identità di Azure AD. Per i dispositivi aggiunti ad Azure AD, l'accesso primario dell'utente è l'identità di Azure AD, quindi non sono necessarie operazioni aggiuntive di configurazione. Per i dispositivi che usano Active Directory locale tradizionale, l'amministratore IT deve [connettere i dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Archiviazione dei dati di sincronizzazione
I dati del servizio Enterprise State Roaming sono ospitati in una o più [aree di Azure](https://azure.microsoft.com/regions/) ottimali per il valore relativo al paese/all'area impostati nell'istanza di Azure Active Directory. I dati del servizio Enterprise State Roaming vengono partizionati in base alle tre principali aree geografiche: America del Nord, EMEA e Asia Pacifico. I dati del servizio Enterprise State Roaming per il tenant si trovano in locale con l'area geografica e non vengono replicati tra le aree.  Ad esempio, i dati dei clienti che hanno impostato il valore del paese/area su uno dei paesi EMEA come "Francia" o "Zambia", saranno ospitati in una o più aree di Azure in Europa.  I dati dei clienti che hanno impostato il valore del paese/area in Azure AD su uno dei paesi dell'America del Nord, come "Stati Uniti" o "Canada", saranno ospitati in una o più aree di Azure negli Stati Uniti.  I dati dei clienti che hanno impostato il valore del paese/area in Azure AD su uno dei paesi dell'Asia Pacifico, come "Australia" o "Nuova Zelanda", saranno ospitati in una o più aree di Azure in Asia.  I dati dei paesi dell'America del Sud e dell'Antartide saranno ospitati in una o più aree di Azure negli Stati Uniti.  Il valore relativo al paese/all'area viene impostato come parte del processo di creazione della directory Azure AD e non può essere modificato successivamente. 

Se sono necessarie altre informazioni sulla posizione di archiviazione dei dati, inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Gestire il servizio Enterprise State Roaming
Gli amministratori globali di Azure AD possono abilitare e disabilitare il servizio Enterprise State Roaming nel portale di Azure classico.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Gli amministratori globali possono limitare la sincronizzazione delle impostazioni a gruppi di sicurezza specifici.

Gli amministratori globali possono anche visualizzare un report sullo stato della sincronizzazione dei dispositivi per utente, selezionando un determinato utente nell'elenco delle istanze di Active Directory **UTENTI**, facendo clic sulla scheda **DISPOSITIVI** e selezionando la vista **Impostazioni di sincronizzazione dispositivi e dati dell'app aziendali**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>Conservazione dei dati
I dati sincronizzati in Azure tramite il servizio Enterprise State Roaming verranno mantenuti per un periodo illimitato, a meno che non venga eseguita un'operazione di eliminazione manuale o che si determini che i dati specifici sono obsoleti. 

**Eliminazione esplicita:** i dati vengono eliminati quando un amministratore di Azure elimina un utente o una directory oppure quando un amministratore richiede in modo esplicito l'eliminazione dei dati.

* **Eliminazione dell'utente**: quando un utente viene eliminato in Azure AD, i dati mobili dell'account utente saranno contrassegnati per l'eliminazione ed eliminati entro 90-180 giorni. 
* **Eliminazione della directory**: l'eliminazione di un'intera directory in Azure AD è un'operazione immediata. Tutti i dati delle impostazioni associati alla directory saranno contrassegnati per l'eliminazione ed eliminati entro 90-180 giorni. 
* **Eliminazione su richiesta**: se l'amministratore di Azure AD vuole eliminare manualmente i dati delle impostazioni o di un utente specifico, può inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/). 

**Eliminazione dei dati obsoleti**: i dati a cui non è stato eseguito l'accesso per un anno ("periodo di conservazione") verranno considerati obsoleti e potranno essere eliminati da Azure. Il periodo di conservazione è soggetto a modifiche ma non sarà inferiore a 90 giorni. I dati obsoleti possono essere un set specifico di impostazioni di Windows/dell'applicazione o tutte le impostazioni per un utente. ad esempio:

* Se nessun dispositivo accede a una raccolta specifica di impostazioni, ad esempio se un'applicazione viene rimossa dal dispositivo o un gruppo di impostazioni come "Tema" viene disabilitato per tutti i dispositivi di un utente, la raccolta diventerà obsoleta dopo il periodo di conservazione e potrà essere eliminata. 
* Se un utente ha disattivato la sincronizzazione delle impostazioni in tutti i propri dispositivi, non verrà effettuato l'accesso ad alcun dato delle impostazioni e tutti i dati delle impostazioni per tale utente diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 
* Se l'amministratore della directory di Azure AD disattiva il servizio Enterprise State Roaming per l'intera directory, tutti gli utenti in tale directory non sincronizzeranno più le impostazioni e tutti i dati delle impostazioni per tutti gli utenti diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 

**Recupero dei dati eliminati**: il criterio relativo alla conservazione dei dati non può essere configurato. Dopo l'eliminazione definitiva, non sarà possibile recuperare i dati. È tuttavia importante notare che i dati delle impostazioni verranno eliminati solo da Azure, non dal dispositivo dell'utente finale. Se un dispositivo si riconnette successivamente al servizio Enterprise State Roaming, le impostazioni verranno sincronizzate e archiviate di nuovo in Azure.

## <a name="related-topics"></a>Argomenti correlati
* [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)




<!--HONumber=Nov16_HO3-->


