---
title: Visualizzare in anteprima i ruoli di amministratore di Azure con autorizzazioni personalizzabili-Azure Active Directory | Microsoft Docs
description: Visualizzare in anteprima i ruoli di Azure AD personalizzati per delegare la gestione delle identità. Gestire i ruoli di Azure in portale di Azure, PowerShell o API Graph.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 546abdae5d7c03bb0d4b49f9485fe06b521fdc1d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722203"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Ruoli di amministratore personalizzato in Azure Active Directory (anteprima)

Questo articolo descrive come comprendere il nuovo controllo degli accessi in base al ruolo (controllo degli accessi in base al ruolo) e gli ambiti delle risorse in Azure Active Directory (Azure AD). I ruoli RBAC personalizzati consentono di visualizzare le autorizzazioni sottostanti dei [ruoli predefiniti, in](directory-assign-admin-roles.md) modo da poter creare e organizzare ruoli personalizzati. Gli ambiti delle risorse consentono di assegnare il ruolo personalizzato per gestire alcune risorse (ad esempio, un'applicazione) senza concedere l'accesso a tutte le risorse (tutte le applicazioni).

La concessione di autorizzazioni tramite ruoli di controllo degli accessi in base al ruolo è un processo in due fasi. Innanzitutto, è necessario creare una definizione di ruolo personalizzata e aggiungervi le autorizzazioni dall'elenco dei set di impostazioni. Si tratta delle stesse autorizzazioni utilizzate nei ruoli predefiniti. Dopo aver creato il ruolo, assegnarlo a un utente creando un'assegnazione di ruolo. Questo processo in due passaggi consente di creare un ruolo e assegnarlo molte volte in ambiti diversi. Un ruolo personalizzato può essere assegnato all'ambito della directory oppure può essere assegnato a un ambito dell'oggetto. Un esempio di ambito di un oggetto è costituito da una singola applicazione. In questo modo, lo stesso ruolo può essere assegnato a Sally su tutte le applicazioni nella directory e quindi su un solo l'app Contoso Expense Reports.

Questa prima versione dei ruoli personalizzati di controllo degli accessi in base al ruolo include la possibilità di creare un ruolo per assegnare le autorizzazioni per la gestione delle registrazioni dell'app Nel corso del tempo verranno aggiunte ulteriori autorizzazioni per le risorse dell'organizzazione, come applicazioni aziendali, utenti e dispositivi.

Funzionalità di anteprima:

- Aggiornamenti dell'interfaccia utente del portale per la creazione e la gestione di ruoli personalizzati e l'assegnazione degli utenti a un ambito a livello di organizzazione
- Un modulo di PowerShell di anteprima con nuovi cmdlet per:
  - Creare e gestire ruoli personalizzati
  - Assegnare ruoli personalizzati con ambito di registrazione a livello di organizzazione o per app
  - Assegnare ruoli predefiniti a livello di organizzazione (parità con i cmdlet GA)
  - Supporto di Azure AD API Graph

Azure AD controllo degli accessi in base al ruolo è una funzionalità di anteprima pubblica di Azure AD ed è disponibile con qualsiasi piano di licenza Azure AD a pagamento. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Informazioni sul controllo degli accessi in base al ruolo Azure AD

Azure AD controllo degli accessi in base al ruolo consente di assegnare ruoli personalizzati per consentire le azioni consentite solo su un singolo tipo di risorsa Azure AD. Azure AD l'accesso basato sui ruoli opera su concetti simili al controllo degli accessi in base al ruolo di Azure ([RBAC](../../role-based-access-control/overview.md) di Azure per l'accesso alle risorse di Azure, ma Azure ad il controllo degli accessi in base al ruolo si basa su Microsoft Graph e Azure Resource Manager il controllo degli accessi in base al ruolo di Azure Tuttavia, entrambi i sistemi basano le proprie funzioni sulle assegnazioni di ruolo.

### <a name="role-assignments"></a>Assegnazioni di ruolo

Il modo in cui si controlla l'accesso tramite Azure AD controllo degli accessi in base al ruolo consiste nel creare assegnazioni di **ruolo**, utilizzate per applicare le autorizzazioni. Un'assegnazione di ruolo è costituita da tre elementi:

- Entità di sicurezza
- Definizione di ruolo
- Ambito delle risorse

L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo. È possibile [creare assegnazioni di ruolo](roles-create-custom.md) usando il portale di Azure, Azure ad PowerShell e API Graph. È possibile [visualizzare separatamente le assegnazioni per un ruolo personalizzato](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio Chris Green ha assegnato il ruolo di [amministratore dell'applicazione](directory-assign-admin-roles.md#application-administrator) nell'ambito dell'applicazione Salesforce. Chris non ha accesso alla gestione di altre applicazioni, a meno che non facciano parte di un'assegnazione di ruolo diversa.

![L'assegnazione di ruolo è il modo in cui vengono applicate le autorizzazioni e include tre parti](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entità di sicurezza

Un'entità di sicurezza rappresenta l'utente o l'entità servizio a cui deve essere assegnato l'accesso alle risorse Azure AD. Un *utente* è una persona che ha un profilo utente in Azure Active Directory. Un' *entità servizio* è un'identità di sicurezza usata da applicazioni o servizi per accedere a risorse Azure ad specifiche.

Un'entità di sicurezza è simile a un'identità utente in quanto rappresenta un nome utente e una password o un certificato, ma per un'applicazione o un servizio anziché un utente.

### <a name="role"></a>Role

Una definizione di ruolo, o Role, è una raccolta di autorizzazioni. In una definizione di ruolo sono elencate le operazioni che possono essere eseguite sulle risorse Azure AD, ad esempio creazione, lettura, aggiornamento ed eliminazione. Esistono due tipi di ruoli in Azure AD:

- Ruoli predefiniti creati da Microsoft che non possono essere modificati. Il ruolo predefinito amministratore globale dispone di tutte le autorizzazioni per tutte le risorse Azure AD.
- Ruoli personalizzati creati e gestiti dall'organizzazione.

### <a name="scope"></a>Ambito

Un ambito è la restrizione delle azioni consentite su una determinata risorsa Azure AD. Quando si assegna un ruolo, è possibile personalizzare il ruolo per limitare le azioni consentite dell'amministratore definendo un ambito di azione. Se, ad esempio, gli sviluppatori non devono gestire completamente tutte le applicazioni, è possibile usare Azure AD ruoli personalizzati per consentire loro di gestire solo le registrazioni dell'app.

## <a name="required-license-plan"></a>Piano di licenza obbligatorio

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare assegnazioni di ruolo personalizzate usando [il portale di Azure, Azure ad PowerShell e API Graph](roles-create-custom.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
