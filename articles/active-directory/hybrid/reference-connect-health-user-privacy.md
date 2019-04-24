---
title: Azure AD Connect Health e privacy dell'utente | Microsoft Docs
description: Questo documento descrive la privacy dell'utente con Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27e5ae48a8194c05a19e5164ee2cc6be68967a62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386249"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacy dell'utente e Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Il presente articolo affronta Azure AD Connect Health e la privacy dell'utente.  Per informazioni su Azure AD Connect e la privacy dell'utente, vedere l'articolo [qui](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Classificazione della privacy dell'utente
Azure AD Connect Health rientra nella categoria di **elaboratore dati** della classificazione del GDPR. In quanto pipeline di elaborazione dati, Azure AD Connect Health fornisce servizi di elaborazione dati ai principali partner e utenti finali. Azure AD Connect Health non genera dati utente e non ha alcun controllo indipendente su quali dati personali vengono raccolti e su come vengono utilizzati. Il recupero, l'aggregazione, l'analisi e la creazione di report dei dati in Azure AD Connect Health sono basati sui dati locali esistenti. 

## <a name="data-retention-policy"></a>Criteri di conservazione dei dati
Azure AD Connect Health non generare report, esegue analisi o fornisce informazioni dettagliate dopo 30 giorni. Di conseguenza, Azure AD Connect Health non archivia, elabora o conserva i dati oltre i 30 giorni. Ciò è conforme al GDPR, all'Informativa sulla privacy Microsoft e ai criteri di conservazione dei dati di Azure AD. 

I server con avvisi di **errore** **I dati del Servizio integrità non sono aggiornati** attivi per oltre 30 giorni consecutivi indicano che nessun dato ha raggiunto Connect Health durante tale intervallo di tempo. Questi server verranno disabilitati e non visualizzati nel portale di Connect Health. Per abilitare nuovamente i server, è necessario disinstallare e [reinstallare l'agente integrità](how-to-connect-health-agent-install.md). Si noti che questa procedura non è applicabile agli **avvisi** con lo stesso tipo. Gli avvisi indicano che mancano dati parziali nel server per il quale sono stati ricevuti gli avvisi. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Disabilitare la raccolta di dati e il monitoraggio in Azure AD Connect Health
Azure AD Connect Health consente di arrestare la raccolta di dati per ciascun server monitorato o per un'istanza di un servizio monitorato. Ad esempio, è possibile interrompere la raccolta di dati per i singoli server ADFS (Active Directory Federation Services) che vengono monitorati tramite Azure AD Connect Health. È anche possibile arrestare la raccolta di dati per l'intera istanza ADFS che si desidera monitorare con Azure AD Connect Health. Quando si sceglie di eseguire questa operazione, i server corrispondenti vengono eliminati dal portale di Azure AD Connect Health, dopo l'arresto della raccolta di dati. 

>[!IMPORTANT]
> Sono necessari i privilegi di Amministratore globale di Azure AD o il ruolo di Collaboratore in Controllo degli accessi in base al ruolo per eliminare i server monitorati da Azure AD Connect Health.
>
> La rimozione di un server o un'istanza del servizio da Azure AD Connect Health non è un'operazione reversibile. 

### <a name="what-to-expect"></a>Cosa aspettarsi?
Se si arresta la raccolta di dati e il monitoraggio per un singolo server di monitoraggio o un'istanza di un servizio monitorato, tenere presente quanto segue:

- Quando si elimina un'istanza di un servizio monitorato, viene rimossa l'istanza dall'elenco del servizio di monitoraggio di Azure AD Connect Health nel portale. 
- Quando si elimina un server monitorato o un'istanza di un servizio monitorato, l'Agente integrità NON viene disinstallato o rimosso dai server. L'Agente integrità viene configurato per non inviare dati ad Azure AD Connect Health. È necessario disinstallare manualmente l'Agente integrità sui server monitorati in precedenza.
- Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore su uno o più server correlati all'agente per l'integrità.
- Tutti i dati appartenenti all'istanza del servizio monitorato vengono eliminati in base ai criteri di conservazione dati di Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Disabilitare la raccolta di dati e il monitoraggio per un'istanza di un server monitorato
Vedere [Eliminare un'istanza del servizio dal servizio Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Disabilitare la raccolta di dati e il monitoraggio per un server monitorato
Vedere [Per eliminare un server dal servizio Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Disabilitare la raccolta di dati e il monitoraggio per tutti i servizi monitorati in Azure AD Connect Health
Azure AD Connect Health consente anche di arrestare la raccolta di dati di **tutti** i servizi registrati nel tenant. È consigliabile un'analisi attenta e il riconoscimento completo di tutti gli amministratori globali prima di eseguire l'azione. Dopo che il processo è iniziato, il servizio Connect Health arresterà la ricezione, l'elaborazione e la creazione di report per i dati di tutti i servizi. I dati esistenti nel servizio Connect Health verranno conservati per non più di 30 giorni.
Per arrestare la raccolta di dati di un server specifico, eseguire i passaggi durante l'eliminazione dei server specifici. Per arrestare la raccolta di dati a livello di tenant, seguire questa procedura per arrestare la raccolta di dati ed eliminare tutti i servizi del tenant.

1. Fare clic su **Impostazioni generali** sotto la configurazione nel pannello principale. 
2. Fare clic sul pulsante **Interrompi la raccolta di dati** nella parte superiore del pannello. Le altre opzioni delle impostazioni di configurazione del tenant verranno disabilitate dopo l'avvio del processo.  
 
   ![Arrestare la raccolta di dati](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Verificare l'elenco di servizi caricati che sono interessati dall'arresto delle raccolte di dati. 
4. Immettere il nome esatto del tenant per abilitare il pulsante dell'azione **Elimina**
5. Fare clic su **Elimina** per attivare l'eliminazione di tutti i servizi. Connect Health arresterà la ricezione, l'elaborazione e la creazione di report per tutti i dati inviati dai servizi caricati. L'intero processo può richiedere fino a 24 ore. Si noti che questo passaggio non è reversibile. 
6. Dopo avere completato il processo, i servizi registrati non verranno più visualizzati in Connect Health. 

   ![Dopo l'arresto della raccolta dati](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Riabilitare la raccolta di dati e il monitoraggio in Azure AD Connect Health
Per riabilitare il monitoraggio in Azure AD Connect Health per un servizio monitorato eliminato in precedenza, è necessario disinstallare e [reinstallare l'Agente integrità](how-to-connect-health-agent-install.md) in tutti i server.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Abilitare di nuovo la raccolta di dati e il monitoraggio per tutti i servizi monitorati

È possibile riprendere la raccolta di dati a livello di tenant in Azure AD Connect Health. È consigliabile un'analisi attenta e il riconoscimento completo di tutti gli amministratori globali prima di eseguire l'azione.

>[!IMPORTANT]
> La procedura seguente sarà disponibile dopo 24 ore dall'azione di disabilitazione.
> Dopo l'abilitazione della raccolta di dati, le informazioni presentate e i dati di monitoraggio in Connect Health non mostreranno più i dati legacy raccolti prima. 

1. Fare clic su **Impostazioni generali** sotto la configurazione nel pannello principale. 
2. Fare clic sul pulsante **Abilita la raccolta di dati** nella parte superiore del pannello. 
 
   ![Abilitare la raccolta di dati](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Immettere il nome esatto del tenant per attivare il pulsante **Attiva**.
4. Fare clic sul pulsante **Attiva** per concedere l'autorizzazione per la raccolta di dati nel servizio Connect Health. La modifica verrà applicata a breve. 
5. Seguire il [processo di installazione](how-to-connect-health-agent-install.md) per reinstallare l'agente nei server da monitorare. I servizi saranno in questo modo presenti nel portale.  


## <a name="next-steps"></a>Passaggi successivi
* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect e privacy dell'utente](reference-connect-user-privacy.md)

