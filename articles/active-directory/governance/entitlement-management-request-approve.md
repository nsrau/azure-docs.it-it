---
title: Approvare o rifiutare le richieste di accesso nella gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come usare il portale di accesso personale per approvare o rifiutare le richieste a un pacchetto di accesso nella gestione dei diritti di Azure Active Directory (anteprima).
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541526"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Approvare o rifiutare le richieste di accesso nella gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione dei diritti di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con la gestione dei diritti di Azure AD, è possibile configurare i criteri per richiedere l'approvazione per i pacchetti di accesso e scegliere uno o più revisori. In questo articolo viene designate come responsabili approvazione può approvare o rifiutare le richieste per i pacchetti di accesso.

## <a name="open-request"></a>Richiesta di apertura

Il primo passaggio per approvare o rifiutare le richieste di accesso è per trovare e aprire la richiesta di accesso in attesa di approvazione. Esistono due modi per aprire la richiesta di accesso.

**Ruolo prerequisiti:** Responsabile approvazione

1. Cercare un messaggio di posta elettronica da Microsoft Azure in cui viene chiesto di approvare o rifiutare una richiesta. Di seguito è riportato un messaggio di posta elettronica di esempio:

    ![Approva la richiesta per accedere alla posta elettronica di pacchetto](./media/entitlement-management-shared/email-approve-request.png)

1. Fare clic sui **approva o Rifiuta richiesta** collegamento per aprire la richiesta di accesso.

1. Accedere al portale di accesso personale.

Se non si ha l'indirizzo di posta elettronica, è possibile trovare le richieste di accesso in attesa di approvazione seguendo questa procedura.

1. Accedere al portale di accesso personali all'indirizzo [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. Nel menu a sinistra, fare clic su **approvazioni** per visualizzare un elenco di richieste di accesso in attesa di approvazione.

1. Nel **in sospeso** scheda, trovare la richiesta.

## <a name="approve-or-deny-request"></a>Approva o Rifiuta richieste

Dopo avere aperto una richiesta di accesso in attesa di approvazione, è possibile visualizzare i dettagli che consentono di rendere un'approvazione o negare delle decisioni.

**Ruolo prerequisiti:** Responsabile approvazione

1. Scegliere il **vista** collegamento per aprire il riquadro di richiesta di accesso.

1. Fare clic su **dettagli** per visualizzarne i dettagli della richiesta di accesso.

    I dettagli includono il nome dell'utente, organizzazione, accedere a data di inizio e fine, se specificato, una motivazione aziendale, quando è stata inviata la richiesta e quando la richiesta scadrà.

1. Fare clic su **approvare** oppure **negare**.

1. Se necessario, immettere un motivo.

    ![Il portale di accesso - richiesta di accesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Fare clic su **Submit** per inviare la decisione.

    Se un criterio è configurato con più responsabili approvazione, solo un responsabile approvazione deve prendere una decisione sull'approvazione in sospeso. Dopo che un responsabile approvazione ha inviato la propria decisione alla richiesta di accesso, la richiesta viene completata e non è più disponibile per altri responsabili dell'approvazione di approvare o negare la richiesta. Altri responsabili approvazione è possono visualizzare la decisione di richiesta e l'elemento decisivo nel portale di accesso personale. A questo punto, è supportata solo l'approvazione fase singola.

    Se nessuno dei responsabili dell'approvazione configurati è in grado di approvare o rifiutare la richiesta di accesso, la richiesta scade al termine della durata configurato per la richiesta. L'utente riceve la notifica che la richiesta di accesso è scaduto e che è necessario inviare di nuovo la richiesta di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Richiedere le notifiche di posta elettronica e di processo](entitlement-management-process.md)
