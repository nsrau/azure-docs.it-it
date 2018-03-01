---
title: Azure AD Connect Health e il Regolamento generale sulla protezione dei dati | Microsoft Docs
description: "Questo documento descrive come ottenere la conformità al GDPR con Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>Conformità al GDPR e Azure AD Connect Health 

Il [Regolamento generale sulla protezione dei dati (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) è una legge sulla privacy e sulla protezione dei dati dell'Unione europea (UE). Il GDPR impone nuove regole per le aziende, gli enti pubblici, le organizzazioni no profit e altre organizzazioni che offrono beni e servizi a persone nell'UE o che raccolgono e analizzano dati collegati a persone residenti nell'UE. 

Sono oggi disponibili prodotti e servizi Microsoft che aiutano a soddisfare i requisiti del GDPR. Per altri dettagli sull'Informativa sulla privacy Microsoft, consultare il [Trust Center](https://www.microsoft.com/trustcenter).

Azure AD Connect Health monitora l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione. Fornisce anche informazioni dettagliate e aree di avvisi. Microsoft si impegna per la conformità dei servizi cloud al GDPR per la data di entrata in vigore della legge a maggio 2018 e delinea nei propri contratti le garanzie correlate al GDPR. 

>[!NOTE] 
> Questo articolo illustra la conformità al GDPR in Azure AD Connect Health. Per informazioni sulla conformità al GDPR in Azure AD Connect, vedere [Conformità al GDPR e Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>Classificazione del GDPR
Azure AD Connect Health rientra nella categoria di **elaboratore dati** della classificazione del GDPR. In quanto pipeline di elaborazione dati, Azure AD Connect Health fornisce servizi di elaborazione dati ai principali partner e utenti finali. Azure AD Connect Health non genera dati utente e non ha alcun controllo indipendente su quali dati personali vengono raccolti e su come vengono utilizzati. Il recupero, l'aggregazione, l'analisi e la creazione di report dei dati in Azure AD Connect Health sono basati sui dati locali esistenti. 

## <a name="data-retention-policy"></a>Criteri di conservazione dei dati
Azure AD Connect Health non generare report, esegue analisi o fornisce informazioni dettagliate dopo 30 giorni. Di conseguenza, Azure AD Connect Health non archivia, elabora o conserva i dati oltre i 30 giorni. Ciò è conforme al GDPR, all'Informativa sulla privacy Microsoft e ai criteri di conservazione dei dati di Azure AD. 
 
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

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Disabilitare la raccolta di dati e il monitoraggio per un server monitorato
Vedere [Per eliminare un server dal servizio Azure AD Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Disabilitare la raccolta di dati e il monitoraggio per un'istanza di un server monitorato
Vedere [Eliminare un'istanza del servizio dal servizio Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Riabilitare la raccolta di dati e il monitoraggio in Azure AD Connect Health
Per riabilitare il monitoraggio in Azure AD Connect Health per un servizio monitorato eliminato in precedenza, è necessario disinstallare e [reinstallare l'Agente integrità](active-directory-aadconnect-health-agent-install.md) in tutti i server.


## <a name="next-steps"></a>Passaggi successivi
* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect e il GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
