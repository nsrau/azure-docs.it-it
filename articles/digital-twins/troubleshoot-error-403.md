---
title: 'Richiesta di Azure Digital Twins non riuscita con stato: 403 (accesso negato)'
description: "Le cause e le risoluzioni per ' richiesta di servizio non sono riuscite. Stato: 403 (accesso negato) sui dispositivi gemelli digitali di Azure."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: a971291dd423894e4d04158abe873a7222f9802c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405594"
---
# <a name="service-request-failed-status-403-forbidden"></a>Richiesta di servizio non riuscita. Stato: 403 (accesso negato)

Questo articolo descrive le cause e i passaggi di risoluzione per la ricezione di un errore 403 dalle richieste di servizio a dispositivi gemelli digitali di Azure. 

## <a name="symptoms"></a>Sintomi

Questo errore può verificarsi in molti tipi di richieste di servizio che richiedono l'autenticazione. L'effetto è che la richiesta dell'API ha esito negativo, restituendo lo stato di errore `403 (Forbidden)` .

## <a name="causes"></a>Cause

### <a name="cause-1"></a>Causare #1

Generalmente, questo errore indica che le autorizzazioni di controllo degli accessi in base al ruolo (RBAC) per il servizio non sono configurate correttamente. Per molte azioni per un'istanza di Azure Digital Twins è necessario avere il ruolo *proprietario (anteprima) di Azure Digital Twins* nell' **istanza che si sta tentando di gestire**. 

### <a name="cause-2"></a>Causare #2

Se si usa un'app client per comunicare con i dispositivi gemelli digitali di Azure, questo errore può verificarsi perché la registrazione dell'app [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) non ha le autorizzazioni impostate per il servizio Azure Digital Twins.

La registrazione dell'app è necessaria per avere le autorizzazioni di accesso configurate per le API dei dispositivi gemelli digitali di Azure. Quindi, quando l'app client esegue l'autenticazione con la registrazione dell'app, verranno concesse le autorizzazioni configurate per la registrazione dell'app.

## <a name="solutions"></a>Soluzioni

### <a name="solution-1"></a>#1 soluzione

La prima soluzione consiste nel verificare che l'utente di Azure disponga del ruolo _**proprietario (anteprima) di Azure Digital Twins**_ nell'istanza che si sta tentando di gestire. Se non si dispone di questo ruolo, impostarlo.

Si noti che questo ruolo è diverso da...
* ruolo *proprietario* nell'intera sottoscrizione di Azure. Il *proprietario di Azure Digital gemelli (anteprima)* è un ruolo nei dispositivi gemelli digitali di Azure ed è limitato a questa singola istanza di Azure Digital gemelli.
* il ruolo di *proprietario* nei dispositivi gemelli digitali di Azure. Si tratta di due ruoli di gestione distinti di Azure Digital gemelli e il *proprietario di Azure Digital gemelli (anteprima)* è il ruolo da usare per la gestione durante l'anteprima.

#### <a name="check-current-setup"></a>Controllare l'installazione corrente

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Risolvere i problemi 

Se non si dispone di questa assegnazione di ruolo, un utente con un ruolo proprietario nella **sottoscrizione di Azure** deve eseguire il comando seguente per concedere all'utente di Azure il ruolo *proprietario (anteprima) di Azure Digital gemelli* nell'istanza di Azure **Digital**gemelli. 

Se si è un proprietario della sottoscrizione, è possibile eseguire questo comando manualmente. In caso contrario, contattare un proprietario per eseguire questo comando per conto dell'utente.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Per altri dettagli su questo requisito del ruolo e il processo di assegnazione, vedere la [sezione *configurare le autorizzazioni di accesso dell'utente* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) in *procedura: configurare un'istanza e l'autenticazione (CLI o portale)*.

Se questa assegnazione di ruolo è già presente e si verifica ancora il problema 403, passare alla soluzione successiva.

### <a name="solution-2"></a>#2 soluzione

La seconda soluzione consiste nel verificare che la registrazione dell'app Azure AD disponga di autorizzazioni configurate per il servizio Azure Digital Twins. Se questo non è configurato, configurarlo.

#### <a name="check-current-setup"></a>Controllare l'installazione corrente

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Prima di tutto, verificare che le impostazioni delle autorizzazioni di Azure Digital Twins siano state impostate correttamente nella registrazione. A tale scopo, selezionare *manifesto* dalla barra dei menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare questi campi sotto `requiredResourceAccess` . I valori devono corrispondere a quelli dello screenshot seguente:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Risolvere i problemi

Se una di queste impostazioni viene visualizzata in modo diverso rispetto a quanto descritto, seguire le istruzioni su come configurare la registrazione dell'app nella [sezione *configurare le autorizzazioni di accesso per le applicazioni client* ](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) di *procedura: configurare un'istanza e l'autenticazione (CLI o portale)*.

## <a name="next-steps"></a>Passaggi successivi

Leggere i passaggi di installazione per la creazione e l'autenticazione di una nuova istanza di Azure Digital Twins:
* [*Procedura: configurare un'istanza e l'autenticazione (CLI)*](how-to-set-up-instance-cli.md)

Scopri di più su sicurezza e autorizzazioni nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)