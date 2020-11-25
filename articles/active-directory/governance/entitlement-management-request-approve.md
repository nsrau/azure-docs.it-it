---
title: Approva o rifiuta le richieste di accesso-gestione dei diritti Azure AD
description: Informazioni su come usare il portale di accesso personale per approvare o negare le richieste a un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfbd5e6f190e0600c31aa17e9b1a75f08e213df4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973576"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Approva o rifiuta le richieste di accesso in Azure AD gestione dei diritti

Con Azure AD gestione dei diritti, è possibile configurare i criteri per richiedere l'approvazione per i pacchetti di accesso e scegliere uno o più responsabili approvazione. Questo articolo descrive come gli approvatori designati possono approvare o negare le richieste di pacchetti di accesso.

## <a name="open-request"></a>Apri richiesta

Il primo passaggio per approvare o negare le richieste di accesso consiste nell'individuare e aprire la richiesta di accesso in attesa di approvazione. Esistono due modi per aprire la richiesta di accesso.

**Ruolo prerequisiti:** Approvatore

1. Cercare un messaggio di posta elettronica da Microsoft Azure che chieda di approvare o rifiutare una richiesta. Di seguito è riportato un messaggio di posta elettronica di esempio:

    ![Approva la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/approver-request-email.png)

1. Fare clic sul collegamento **approva o Nega richiesta** per aprire la richiesta di accesso.

1. Accedere al portale di accesso personale.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le richieste di accesso in attesa di approvazione attenendosi alla seguente procedura.

1. Accedere al portale di accesso personale all'indirizzo [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  Per il governo degli Stati Uniti, il dominio nel collegamento al portale di accesso personale sarà `myaccess.microsoft.us` .

1. Nel menu a sinistra fare clic su **approvazioni** per visualizzare un elenco di richieste di accesso in attesa di approvazione.

1. Individuare la richiesta nella scheda **in sospeso** .

## <a name="view-requestors-answers-to-questions-preview"></a>Visualizza le risposte del richiedente alle domande (anteprima)

1. Passare alla scheda **approvazioni** in accesso personale.

1. Passare alla richiesta che si desidera approvare e fare clic su **Dettagli**. È anche possibile fare clic su **approva** o **Nega** se si è pronti per prendere una decisione.

1. Fare clic su **Dettagli richiesta**.

     ![Portale di accesso personale-richiesta di accesso-dettagli richiesta](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. Le informazioni fornite dal richiedente saranno nella parte inferiore del pannello.

     ![Screenshot che mostra i dettagli della richiesta](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. In base alle informazioni fornite dal richiedente, è possibile approvare o rifiutare la richiesta. Per istruzioni, vedere la procedura in approva o Nega richiesta.

## <a name="approve-or-deny-request"></a>Approva o rifiuta la richiesta

Dopo aver aperto una richiesta di accesso in attesa di approvazione, è possibile visualizzare i dettagli che consentiranno di prendere una decisione di approvazione o negazione.

**Ruolo prerequisiti:** Approvatore

1. Fare clic sul collegamento **Visualizza** per aprire il riquadro Richiesta di accesso.

1. Fare clic su **Dettagli** per visualizzare i dettagli relativi alla richiesta di accesso.

    I dettagli includono il nome, l'organizzazione, la data di inizio e di fine dell'accesso, se specificato, la giustificazione aziendale, il momento in cui la richiesta è stata inviata e la scadenza della richiesta.

1. Fare clic su **approva** o **Nega**.

1. Se necessario, immettere un motivo.

    ![Screenshot mostra la pagina in cui si accetta o si nega la richiesta.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Fare clic su **Invia** per inviare la decisione.

    Se un criterio è configurato con più responsabili approvazione, solo un responsabile approvazione deve prendere una decisione relativa all'approvazione in sospeso. Dopo che un responsabile approvazione ha inviato la richiesta di accesso, la richiesta è stata completata e non è più disponibile agli altri responsabili approvazione per approvare o negare la richiesta. Gli altri responsabili approvazione possono visualizzare la decisione della richiesta e il decision maker nel portale di accesso personale. Al momento, è supportata solo l'approvazione a fase singola.

    Se nessuno dei responsabili approvazione configurato è in grado di approvare o rifiutare la richiesta di accesso, la richiesta scade dopo la durata della richiesta configurata. L'utente riceve una notifica che indica che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
