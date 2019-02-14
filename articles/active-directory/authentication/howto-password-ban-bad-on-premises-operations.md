---
title: Operazioni e reporting della password di protezione di Azure AD in anteprima
description: Operazioni e reporting post-distribuzione della password di protezione di Azure AD in anteprima
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fda79f16560a5c96e1283f4d9d9f14dbe503d61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175243"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Anteprima: procedure operative della password di protezione di Azure AD

|     |
| --- |
| La password di protezione di Azure AD è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Dopo aver completato l'[installazione della password di protezione di Azure AD](howto-password-ban-bad-on-premises-deploy.md) in locale, è necessario configurare alcuni componenti nel portale di Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurare l'elenco personalizzato password escluse

Seguire le indicazioni fornite nell'articolo [Configurazione dell'elenco personalizzato password escluse](howto-password-ban-bad-configure.md) per la procedura di personalizzazione dell'elenco di password escluse per la propria organizzazione.

## <a name="enable-password-protection"></a>Abilitare la password di protezione

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory**, **Authentication methods** (Metodi di autenticazione) e quindi **Password protection (Preview)** (Password di protezione - Anteprima).
1. Impostare **Enable Password Protection on Windows Server Active Directory** (Abilita la password di protezione in Windows Server Active Directory) su **Sì**
1. Come indicato nella [Guida alla distribuzione](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), è consigliabile impostare inizialmente **Modalità** su **Controllo**
   * Dopo avere familiarizzato con la funzione, è possibile impostare **Modalità** su **Applicato**
1. Fare clic su **Save**

![Abilitazione dei componenti della password di protezione di Azure AD nel portale di Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modalità di controllo

La modalità di controllo è concepita come un modo per eseguire il software in modalità "what-if". Ogni servizio agente del controller di dominio valuta una password in ingresso in base ai criteri attualmente attivi. Se i criteri correnti sono configurati per essere in modalità di controllo, le password "non corrette" generano messaggi del log eventi, ma vengono accettate. Questa è l'unica differenza tra le modalità di controllo e la modalità di imposizione; tutte le altre operazioni vengono eseguite nello stesso modo.

> [!NOTE]
> Microsoft consiglia di avviare la distribuzione iniziale e i test sempre in modalità di controllo. È quindi opportuno controllare gli eventi nel log eventi per cercare di prevedere se eventuali processi operativi esistenti potrebbero essere ostacolati dopo l'attivazione della modalità di imposizione.

## <a name="enforce-mode"></a>Modalità di imposizione

La modalità di imposizione è concepita come configurazione finale. Come nella modalità di controllo riportata sopra, ogni servizio agente del controller di dominio valuta le password in ingresso in base ai criteri attualmente attivi. Se è abilitata la modalità di imposizione, tuttavia, la password considerata non sicura in base ai criteri viene rifiutata.

Quando una password viene rifiutata in modalità di imposizione dall'agente del controller di dominio della password di protezione di Azure AD, l'impatto visibile ricevuto dall'utente finale è identico a quello che riceverebbe se la password fosse stata rifiutata dall'applicazione tradizionale che gestisce la complessità delle password in locale. Ad esempio, un utente potrebbe ricevere il seguente messaggio di errore tradizionale nella schermata di accesso/modifica password di Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Questo messaggio è solo un esempio dei possibili risultati. Il messaggio di errore specifico può variare a seconda del software o dello scenario effettivo che sta tentando di impostare una password non sicura.

Gli utenti finali interessati potrebbero avere bisogno di collaborare con il proprio personale IT per comprendere i nuovi requisiti e scegliere password sicure.

## <a name="enable-mode"></a>Modalità di abilitazione

Questa impostazione deve essere lasciata in genere nello stato predefinito abilitato (Sì). Se si configura questa impostazione sullo stato disabilitato (No) tutti gli agenti del controller di dominio della password di protezione di Azure AD passeranno a una modalità inattiva in cui tutte le password vengono accettate così come sono, senza alcuna attività di convalida (ad esempio, non verranno generati nemmeno gli eventi di controllo).

## <a name="next-steps"></a>Passaggi successivi

[Monitoraggio per la password di protezione di Azure AD](howto-password-ban-bad-on-premises-monitor.md)
