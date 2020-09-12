---
title: API di accesso condizionale e PowerShell-Azure Active Directory
description: Uso delle API di accesso condizionale Azure AD e PowerShell per gestire i criteri come il codice
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f3766e02316eccb519bea15246541531750ed1
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90008027"
---
# <a name="conditional-access-programmatic-access"></a>Accesso condizionale: accesso a livello di codice

Molte organizzazioni hanno espresso la necessità di gestire la maggior parte degli ambienti, come il codice possibile. Utilizzando Microsoft Graph è possibile considerare i criteri di accesso condizionale come qualsiasi altra parte di codice nell'ambiente in uso.

Microsoft Graph fornisce un modello di programmabilità unificato che le organizzazioni possono usare per interagire con i dati in Microsoft 365, Windows 10 e Enterprise Mobility + Security. Per ulteriori informazioni su Microsoft Graph, vedere l'articolo [Panoramica delle Microsoft Graph](/graph/overview).

![Immagine che mostra le risorse e le relazioni primarie che fanno parte del grafico](./media/howto-conditional-access-apis/microsoft-graph.png)

Gli esempi seguenti sono forniti così come sono, senza supporto. È possibile utilizzare questi esempi come base per gli strumenti della propria organizzazione. 

Molti degli esempi seguenti usano strumenti come [identità gestite](../managed-identities-azure-resources/overview.md), app per la [logica](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Team](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)e [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Configurare

### <a name="powershell"></a>PowerShell

Per molti amministratori, PowerShell è già uno strumento di scripting noto. L'esempio seguente illustra come usare il [modulo Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureAD) per gestire i criteri di accesso condizionale.

- [Configurare i criteri di accesso condizionale con Azure AD comandi di PowerShell](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>API Graph

Questo esempio mostra le opzioni di base di creazione, lettura, aggiornamento ed eliminazione (CRUD) disponibili nelle API Graph di accesso condizionale. L'esempio include anche alcuni modelli JSON che è possibile usare per creare alcuni criteri di esempio.

- [Configurare i criteri di accesso condizionale con Microsoft Graph chiamate API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Configurare usando i modelli

Usare le API di accesso condizionale per distribuire i criteri di accesso condizionale nell'ambiente di pre-produzione usando un modello.

- [Configurare i criteri di accesso condizionale con Microsoft Graph modelli API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Test

Questo esempio modella le procedure di distribuzione più sicure con flussi di lavoro di approvazione che possono copiare i criteri di accesso condizionale da un ambiente, ad esempio pre-produzione, a un altro, come l'ambiente di produzione.

- [Innalzare di livello i criteri di accesso condizionale dagli ambienti](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Distribuire

Questo esempio fornisce un meccanismo per eseguire gradualmente i criteri di accesso condizionale per la distribuzione di gestione temporanea per la popolazione degli utenti, consentendo di gestire tempestivamente l'effetto del supporto e individuare i problemi.

- [Distribuire i criteri di accesso condizionale in ambienti di produzione con flussi di lavoro di approvazione](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitorare

Questo esempio fornisce un meccanismo per monitorare le modifiche ai criteri di accesso condizionale nel tempo e può attivare avvisi quando vengono modificati i criteri chiave.

- [Monitorare i criteri di accesso condizionale distribuiti per le modifiche e attivare gli avvisi](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Gestione

### <a name="backup-and-restore"></a>Backup e ripristino

Automatizzare il backup e il ripristino dei criteri di accesso condizionale con approvazioni nei team con questo esempio.

- [Gestire il processo di backup e ripristino dei criteri di accesso condizionale usando le chiamate API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Account di accesso di emergenza

Più amministratori possono creare criteri di accesso condizionale e potrebbero dimenticare di aggiungere gli [account di accesso di emergenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) come esclusione di tali criteri. Questo esempio garantisce che tutti i criteri vengano aggiornati in modo da includere gli account di accesso di emergenza designati.

- [Gestire l'assegnazione degli account di accesso di emergenza ai criteri di accesso condizionale usando le chiamate API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Piani di emergenza

In tal caso, è necessario un modo per tornare a uno stato in cui il lavoro può continuare. Nell'esempio seguente viene illustrato come ripristinare i criteri a un piano di emergenza valido noto e disabilitare altri criteri di accesso condizionale.

- [Gestire l'attivazione dei criteri di contingenza dell'accesso condizionale usando le chiamate API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Contributo della community

Questi esempi sono disponibili nel [repository GitHub](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Siamo lieti di supportare i contributi della community per i problemi di GitHub e le richieste pull.

## <a name="next-steps"></a>Passaggi successivi

- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](/graph/overview)

- [API di accesso condizionale](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [API località denominata](/graph/api/resources/namedlocation?view=graph-rest-1.0)
