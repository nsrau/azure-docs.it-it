---
title: Approva o rifiuta le richieste di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come usare il portale di accesso personale per approvare o negare le richieste a un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf254f06c14afa7ee32f27d6632ab33665cf31c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173468"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Approva o rifiuta le richieste di accesso in Azure AD gestione dei diritti

Con Azure AD gestione dei diritti, è possibile configurare i criteri per richiedere l'approvazione per i pacchetti di accesso e scegliere uno o più responsabili approvazione. Questo articolo descrive come gli approvatori designati possono approvare o negare le richieste di pacchetti di accesso.

## <a name="open-request"></a>Apri richiesta

Il primo passaggio per approvare o negare le richieste di accesso consiste nell'individuare e aprire la richiesta di accesso in attesa di approvazione. Esistono due modi per aprire la richiesta di accesso.

**Ruolo prerequisiti:** Approvatore

1. Cercare un messaggio di posta elettronica da Microsoft Azure che chieda di approvare o rifiutare una richiesta. Di seguito è riportato un messaggio di posta elettronica di esempio:

    ![Approva la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/email-approve-request.png)

1. Fare clic sul collegamento **approva o Nega richiesta** per aprire la richiesta di accesso.

1. Accedere al portale di accesso personale.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le richieste di accesso in attesa di approvazione attenendosi alla seguente procedura.

1. Accedere al portale di accesso personale all' [https://myaccess.microsoft.com](https://myaccess.microsoft.com).

1. Nel menu a sinistra fare clic su **approvazioni** per visualizzare un elenco di richieste di accesso in attesa di approvazione.

1. Individuare la richiesta nella scheda **in sospeso** .

## <a name="approve-or-deny-request"></a>Approva o rifiuta la richiesta

Dopo aver aperto una richiesta di accesso in attesa di approvazione, è possibile visualizzare i dettagli che consentiranno di prendere una decisione di approvazione o negazione.

**Ruolo prerequisiti:** Approvatore

1. Fare clic sul collegamento **Visualizza** per aprire il riquadro Richiesta di accesso.

1. Fare clic su **Dettagli** per visualizzare i dettagli relativi alla richiesta di accesso.

    I dettagli includono il nome, l'organizzazione, la data di inizio e di fine dell'accesso, se specificato, la giustificazione aziendale, il momento in cui la richiesta è stata inviata e la scadenza della richiesta.

1. Fare clic su **approva** o **Nega**.

1. Se necessario, immettere un motivo.

    ![Portale di Accesso personale - Richiesta di accesso](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Fare clic su **Invia** per inviare la decisione.

    Se un criterio è configurato con più responsabili approvazione, solo un responsabile approvazione deve prendere una decisione relativa all'approvazione in sospeso. Dopo che un responsabile approvazione ha inviato la richiesta di accesso, la richiesta è stata completata e non è più disponibile agli altri responsabili approvazione per approvare o negare la richiesta. Gli altri responsabili approvazione possono visualizzare la decisione della richiesta e il decision maker nel portale di accesso personale. Al momento, è supportata solo l'approvazione a fase singola.

    Se nessuno dei responsabili approvazione configurato è in grado di approvare o rifiutare la richiesta di accesso, la richiesta scade dopo la durata della richiesta configurata. L'utente riceve una notifica che indica che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
