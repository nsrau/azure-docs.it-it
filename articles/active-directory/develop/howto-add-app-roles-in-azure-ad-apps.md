---
title: Aggiungere ruoli dell'app e riceverli da un token | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come aggiungere i ruoli dell'app a un'applicazione registrata in Azure Active Directory, assegnare utenti e gruppi a questi ruoli e riceverli nell'attestazione "Roles" nel token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706014"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Procedura: Aggiungere ruoli dell'app in un'applicazione e riceverli nel token

Il controllo degli accessi in base al ruolo è uno dei metodi più comuni per introdurre il processo di autorizzazione nelle applicazioni. Quando si usa il controllo degli accessi in base al ruolo, l'amministratore concede le autorizzazioni ai ruoli e non a singoli utenti o gruppi. L'amministratore può quindi assegnare i ruoli a utenti e gruppi per determinare chi può accedere a specifici contenuti e funzionalità.

Usando il controllo degli accessi in base al ruolo con ruoli applicazione e attestazioni di ruolo, gli sviluppatori possono applicare in modo sicuro l'autorizzazione nelle app con minore impegno.

Un altro approccio consiste nell'usare gruppi di Azure AD e attestazioni di gruppo, come illustrato nell'esempio di codice [webapp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) su GitHub. I gruppi di Azure AD e i ruoli applicazione non si escludono a vicenda. possono essere usati in tandem per fornire un controllo di accesso con granularità maggiore.

## <a name="declare-roles-for-an-application"></a>Dichiarare i ruoli per un'applicazione

I ruoli applicazione sono definiti nel [portale di Azure](https://portal.azure.com).  Quando un utente accede all'applicazione, Azure AD emette un'attestazione `roles` per ogni ruolo che viene concesso a un utente individualmente o tramite la relativa appartenenza a un gruppo.  Le assegnazioni di utenti e gruppi ai ruoli possono essere eseguite tramite l'interfaccia utente del portale o a livello di codice usando [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Per creare un ruolo app:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi scegliere il tenant Azure Active Directory che contiene la registrazione dell'app a cui si vuole aggiungere un ruolo app.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci**selezionare **registrazioni app**e quindi selezionare l'applicazione in cui si desidera definire i ruoli dell'app.
1. Selezionare i **ruoli dell'app | Anteprima**e quindi selezionare **Crea ruolo app**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Riquadro dei ruoli dell'app per la registrazione di un'app nel portale di Azure":::
1. Nel riquadro **Crea ruolo app** immettere le impostazioni per il ruolo. La tabella che segue l'immagine descrive ogni impostazione e i relativi parametri.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Il riquadro del contesto di creazione dei ruoli dell'app per la registrazione dell'app nel portale di Azure":::

    | Campo | Descrizione | Esempio |
    |-------|-------------|---------|
    | **Nome visualizzato** | Nome visualizzato per il ruolo dell'app che viene visualizzato nelle esperienze di consenso dell'amministratore e di assegnazione di app. Questo valore può contenere spazi.  | `Survey Writer` |
    | **Tipi di membri consentiti** | Specifica se il ruolo app può essere assegnato a utenti, applicazioni o entrambi. | `Users/Groups` |
    | **Valore** | Specifica il valore dell'attestazione dei ruoli che l'applicazione deve aspettarsi nel token. Il valore deve corrispondere esattamente alla stringa a cui si fa riferimento nel codice dell'applicazione. Il valore non può contenere spazi. | `Survey.Create` |
    | **Descrizione** | Descrizione più dettagliata del ruolo dell'app visualizzato durante l'assegnazione dell'app di amministrazione e l'esperienza di consenso. | `Writers can create surveys.` |
    | **Abilitare questo ruolo app?** | Indica se il ruolo app è abilitato. Per eliminare un ruolo app, deselezionare questa casella di controllo e applicare la modifica prima di provare a eseguire l'operazione di eliminazione. | *Selezionato* |

1. Fare clic su **Applica** per salvare le modifiche.

> [!NOTE]
> Il numero di ruoli da aggiungere viene conteggiato ai limiti definiti per un manifesto dell'applicazione. Per informazioni su questi limiti, vedere la sezione  [limiti del manifesto](./reference-app-manifest.md#manifest-limits) di [Azure Active Directory riferimento al manifesto dell'applicazione](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Assegnare i ruoli a utenti e gruppi

Una volta aggiunti i ruoli dell'app nell'applicazione, è possibile assegnare utenti e gruppi ai ruoli.

1. In **Azure Active Directory** nella portale di Azure selezionare **applicazioni aziendali** nel menu di spostamento a sinistra.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

     Se l'applicazione non viene visualizzata nell'elenco, usare i filtri nella parte superiore dell'elenco **tutte le applicazioni** per limitare l'elenco o scorrere verso il basso nell'elenco per individuare l'applicazione.

1. Selezionare l'applicazione in cui si vuole assegnare ruoli a utenti o gruppi di sicurezza.
1. In **Gestisci**selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** per aprire il riquadro **Aggiungi assegnazione** .
1. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

     Viene visualizzato un elenco di utenti e gruppi di sicurezza. È possibile cercare un determinato utente o gruppo, nonché selezionare più utenti e gruppi visualizzati nell'elenco.

1. Dopo aver selezionato utenti e gruppi, selezionare il pulsante **Seleziona** per continuare.
1. Selezionare **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** . Vengono visualizzati tutti i ruoli definiti per l'applicazione.
1. Scegliere un ruolo e selezionare il pulsante **Seleziona** .
1. Selezionare il pulsante **assign (assegna** ) per completare l'assegnazione di utenti e gruppi all'app.
1. Verificare che gli utenti e i gruppi aggiunti siano visualizzati nell'elenco **utenti e gruppi** .

## <a name="receive-roles-in-tokens"></a>Ricevere i ruoli nei token

Quando gli utenti assegnati ai vari ruoli dell'app possono accedere all'applicazione, i relativi token avranno i rispettivi ruoli assegnati nell' `roles` attestazione.

## <a name="web-api-application-permissions"></a>Autorizzazioni dell'applicazione API Web

È possibile definire i ruoli dell'app destinati a **utenti/gruppi**, **applicazioni**o **entrambi**. Quando si selezionano **le applicazioni** o **entrambe**, il ruolo app viene visualizzato come autorizzazione per l'applicazione in un'app client le **autorizzazioni dell'API**.

Per selezionare l'autorizzazione dell'applicazione in una registrazione dell'app client dopo aver definito un ruolo che ha come destinazione **le applicazioni** o **entrambi**:

1. In **Azure Active Directory** portale di Azure selezionare **registrazioni app**, quindi selezionare la registrazione dell'app client.
1. In **Gestisci** selezionare **Autorizzazioni API**.
1. Selezionare **Aggiungi un'autorizzazione**  >  **API personali**.
1. Selezionare l'app a cui è stato aggiunto il ruolo applicazione e quindi selezionare **Autorizzazioni applicazione**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui ruoli dell'app con le risorse seguenti:

- Esempio di codice: [aggiungere l'autorizzazione usando i ruoli dell'app & attestazioni dei ruoli a un'app Web di ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Video: [implementare l'autorizzazione nelle applicazioni con la piattaforma di identità Microsoft ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifesto dell'app Azure Active Directory](./reference-app-manifest.md)
- [Token di accesso Azure AD](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
