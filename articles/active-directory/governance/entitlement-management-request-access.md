---
title: Richiedere l'accesso a un pacchetto di accesso nella gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come usare il portale di accesso personale per richiedere l'accesso a un pacchetto di accesso nella gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541556"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Richiedere l'accesso a un pacchetto di accesso nella gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Accedere al portale di accesso personale

Il primo passaggio è per accedere al portale di accesso personale in cui è possibile richiedere l'accesso a un pacchetto di accesso.

**Ruolo prerequisito:** Richiedente

1. Cercare un messaggio di posta elettronica o un messaggio dalla gestione del progetto o business che si sta lavorando. Messaggio di posta elettronica deve includere un collegamento al pacchetto di accesso a che è necessario accedere. Il collegamento verrà avviato con:

    `https://myaccess.microsoft.com`

1. Aprire il collegamento.

1. Accedere al portale di accesso personale.

    Assicurarsi di che usare l'account dell'organizzazione. Se si è certi, verificare con il responsabile di progetto o business.

## <a name="request-an-access-package"></a>Richiesta di un pacchetto di accesso

Dopo aver trovato il pacchetto di accesso nel portale di accesso personale, è possibile inviare una richiesta.

**Ruolo prerequisito:** Richiedente

1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

1. Se il **motivazione aziendale** viene visualizzata la finestra, digitare una giustificazione per che necessitano dell'accesso.

1. Se **richiesta per un periodo specifico?** è abilitato, selezionare **Yes** oppure **No**.

1. Se necessario, specificare la data di inizio e data di fine.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Al termine, fare clic su **Submit** per inviare la richiesta.

1. Fare clic su **cronologia delle richieste** per visualizzare un elenco di richieste e lo stato.

    Se il pacchetto di accesso richiede l'approvazione, la richiesta è ora in uno stato di approvazione in sospeso.

## <a name="cancel-a-request"></a>Annullare una richiesta

Se si invia una richiesta di accesso e la richiesta è ancora nel **in attesa di approvazione** lo stato, è possibile annullare la richiesta.

**Ruolo prerequisito:** Richiedente

1. Nel portale di accesso personale, a sinistra, fare clic su **cronologia delle richieste** per visualizzare un elenco di richieste e lo stato.

1. Fare clic sui **vista** collegamento per la richiesta di cui si desidera annullare.

1. Se la richiesta è ancora nel **in attesa di approvazione** lo stato, è possibile fare clic su **richiesta di annullamento** per annullare la richiesta.

    ![Il portale di accesso - richiesta di annullamento.](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Fare clic su **cronologia delle richieste** per confermare la richiesta è stata annullata.

## <a name="select-a-policy"></a>Selezionare un criterio

Se si richiede l'accesso a un pacchetto di accesso che ha più criteri applicabili, potrebbe essere richiesto di selezionare i criteri. Ad esempio, un package manager l'accesso potrebbe configurare un pacchetto di accesso con due criteri per i due gruppi di dipendenti interni. Il primo criterio potrebbe consentire l'accesso per 60 giorni e richiedere l'approvazione. Il secondo criterio potrebbe consentire l'accesso a 2 giorni e non richiedono l'approvazione. Se si verifica questo scenario, è necessario selezionare il criterio che si desidera utilizzare.

**Ruolo prerequisito:** Richiedente

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste di accesso](entitlement-management-request-approve.md)
- [Richiedere le notifiche di posta elettronica e di processo](entitlement-management-process.md)
