---
title: Eseguire la migrazione dei criteri di accesso condizionale - Azure Active Directory
description: Informazioni che è necessario conoscere per eseguire la migrazione dei criteri classici nel portale di Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185918"
---
# <a name="conditional-access-classic-policy-migration"></a>Migrazione dei criteri classici di Accesso condizionale

L'accesso condizionale è lo strumento usato da Azure Active Directory per raggruppare i segnali, consentendo di prendere decisioni e applicare i criteri dell'organizzazione. L'accesso condizionale è la base del nuovo piano di controllo basato su identità. Mentre lo scopo è sempre lo stesso, il rilascio del nuovo portale di Azure ha introdotto miglioramenti significativi al funzionamento di Accesso condizionale.

Valutare la possibilità di eseguire la migrazione dei criteri che non sono stati creati nel portale di Azure per i motivi seguenti:

- È ora possibile gestire scenari che in precedenza non era possibile gestire.
- Il consolidamento consente di ridurre il numero di criteri da gestire.
- È possibile gestire tutti i criteri di accesso condizionale in un'unica posizione centrale.
- Il portale di Azure classico verrà ritirato.

In questo articolo vengono illustrati gli elementi da sapere per eseguire la migrazione dei criteri di accesso condizionale esistenti al nuovo framework.

## <a name="classic-policies"></a>Criteri classici

Nel [portale di Azure](https://portal.azure.com)i criteri di accesso condizionale sono disponibili in**Security** > **Accesso condizionale**sicurezza di **Azure Active Directory** > . L'organizzazione potrebbe anche avere criteri di accesso condizionale meno recenti non creati utilizzando questa pagina. ovvero i *criteri classici*. I criteri classici sono criteri di accesso condizionale creati in:Classic policies are Conditional Access policies, you have created in:

- Portale di Azure classico
- Portale di Intune classico
- Portale di Protezione app di Intune

Nella pagina **Accesso condizionale** è possibile accedere ai criteri classici facendo clic su [**Criteri classici**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) nella sezione **Gestisci.** 

![Accesso condizionale in Azure AD con visualizzazione dei criteri classiciConditional Access in Azure AD showing classic policies view](./media/policy-migration/71.png)

La visualizzazione **Criteri classici** consente di eseguire queste operazioni:

- Filtrare i criteri classici.
- Disabilitare i criteri classici.
- Esaminare le impostazioni di un criterio classico e disabilitarlo.

   ![Dettagli classici dei criteri, inclusa la configurazione dei criteri esistenti](./media/policy-migration/74.png)

> [!WARNING]
> Una volta disabilitato, un criterio classico non può essere riabilitato.

La visualizzazione dei dettagli di un criterio classico consente di documentare le impostazioni, modificare i gruppi inclusi o esclusi e disabilitare il criterio.

![Dettagli criteri - Gruppi da includere o escludere](./media/policy-migration/75.png)

Modificando i gruppi selezionati o escludendo gruppi specifici, è possibile verificare l'effetto di un criterio classico disabilitato per alcuni utenti di prova prima di disabilitare il criterio per tutti gli utenti e i gruppi inclusi.
 
## <a name="migration-considerations"></a>Considerazioni sulla migrazione

In questo articolo, i criteri di accesso condizionale di Azure AD vengono detti anche *nuovi criteri.*
I criteri classici continuano a funzionare in parallelo con quelli nuovi finché non vengono disabilitati o eliminati. 

Nella prospettiva di un consolidamento dei criteri è importante considerare gli aspetti seguenti:

- Diversamente dai criteri classici, che sono legati a un'app cloud specifica, i criteri nuovi consentono di selezionare tutte le app cloud desiderate.
- I controlli applicati da criteri classici e criteri nuovi per un'app cloud devono risultare tutti soddisfatti (*AND*). 
- Nei criteri nuovi è possibile:
   - Combinare più condizioni, se necessarie per lo specifico scenario. 
   - Selezionare più requisiti di controllo di accesso e combinarli con un *OR* logico (per richiedere uno dei controlli selezionati) o con un *AND* logico (per richiedere tutti i controlli selezionati).

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Se si intende eseguire la migrazione di criteri classici per **Office 365 Exchange Online** che includono **Exchange Active Sync** come condizione per le app client, può non essere possibile consolidarli in un unico criterio nuovo. 

Ciò avviene, ad esempio, quando si vuole includere il supporto per tutti i tipi di app client. In un criterio nuovo con **Exchange Active Sync** come condizione per le app client, non è possibile selezionare altre app client.

![Accesso condizionale selezione delle app client](./media/policy-migration/64.png)

Il consolidamento in un criterio nuovo non è possibile nemmeno se i criteri classici contengono più condizioni. Un criterio nuovo con **Exchange Active Sync** configurato come condizione per le app client non supporta altre condizioni:   

![Exchange ActiveSync non supporta le condizioni selezionate](./media/policy-migration/08.png)

Se si ha un criterio nuovo con **Exchange Active Sync** configurato come condizione per le app client, è necessario assicurarsi che tutte le altre condizioni non siano configurate. 

![Condizioni di accesso condizionaleConditional Access conditions](./media/policy-migration/16.png)
 
I criteri classici basati sulle app per Office 365 Exchange Online che includono **Exchange Active Sync** come condizione per le app client consentono piattaforme di dispositivi **supportate** e **non supportate.** Anche se non si possono configurare singole piattaforme di dispositivo in un criterio nuovo correlato, è possibile limitare il supporto alle [piattaforme di dispositivo supportate](concept-conditional-access-conditions.md#device-platforms). 

![Accesso condizionale selezionare Exchange ActiveSync](./media/policy-migration/65.png)

È possibile consolidare più criteri classici che includono **Exchange Active Sync** come condizione per le app client se questi hanno:

- Solo **Exchange Active Sync** come condizione 
- Più requisiti configurati per concedere l'accesso

Uno scenario comune consiste nel consolidamento dei criteri seguenti:

- Un criterio classico basato su dispositivo configurato nel portale di Azure classico 
- Un criterio classico basato su app configurato nel portale di Protezione app di Intune 
 
In questo caso, è possibile consolidare i criteri classici in un unico criterio nuovo con entrambi i requisiti selezionati.

![Controlli delle concessioni di accesso condizionaleConditional Access grant controls](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Piattaforme del dispositivo

I criteri classici con controlli basati su app sono preconfigurati con iOS e Android come condizione per le piattaforme di dispositivo. 

In un criterio nuovo è necessario selezionare le [piattaforme di dispositivo](concept-conditional-access-conditions.md#device-platforms) da supportare singolarmente.

![Selezione delle piattaforme per dispositivi con accesso condizionaleConditional Access device platforms selection](./media/policy-migration/41.png)

## <a name="next-steps"></a>Passaggi successivi

- [Utilizzare la modalità solo report per l'accesso condizionale per determinare l'impatto delle nuove decisioni relative ai criteri.](concept-conditional-access-report-only.md)
- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere l'articolo [Procedura: pianificare la distribuzione](plan-conditional-access.md)di accesso condizionale in Azure Active Directory. 
