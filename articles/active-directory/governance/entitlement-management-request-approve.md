---
title: Approvare o negare le richieste di accesso - Gestione dei diritti di Azure ADApprove or deny access requests - Azure AD entitlement management
description: Informazioni su come usare il portale My Access per approvare o rifiutare le richieste a un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to use the My Access portal to approve or deny requests to an access package in Azure Active Directory entitlement management.
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
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261723"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Approvare o negare le richieste di accesso nella gestione dei diritti di Azure ADApprove or deny access requests in Azure AD entitlement management

Con la gestione dei diritti di Azure AD è possibile configurare criteri per richiedere l'approvazione per i pacchetti di accesso e scegliere uno o più approvatori. In questo articolo viene descritto come gli approvatori designati possono approvare o rifiutare le richieste di pacchetti di accesso.

## <a name="open-request"></a>Richiesta aperta

Il primo passaggio per approvare o rifiutare le richieste di accesso consiste nel trovare e aprire la richiesta di accesso in attesa di approvazione. Esistono due modi per aprire la richiesta di accesso.

**Ruolo prerequisito:** Approvatore

1. Cercare un messaggio di posta elettronica da Microsoft Azure che richiede di approvare o rifiutare una richiesta. Ecco un esempio di email:

    ![Approvare la richiesta di accesso alla posta elettronica del pacchetto](./media/entitlement-management-shared/approver-request-email.png)

1. Fare clic sul collegamento **Approva o rifiuta richiesta** per aprire la richiesta di accesso.

1. Accedere al portale My Access.

Se non si dispone del messaggio di posta elettronica, è possibile trovare le richieste di accesso in attesa di approvazione attenendosi alla procedura seguente.

1. Accedere al portale My [https://myaccess.microsoft.com](https://myaccess.microsoft.com)Access all'indirizzo .  (Per il governo degli Stati Uniti, il `myaccess.microsoft.us`dominio nel collegamento al portale My Access sarà .)

1. Nel menu a sinistra fare clic su **Approvazioni** per visualizzare un elenco di richieste di accesso in attesa di approvazione.

1. Nella scheda **In sospeso** individuare la richiesta.

## <a name="approve-or-deny-request"></a>Approvare o rifiutare la richiesta

Dopo aver aperto una richiesta di accesso in attesa di approvazione, è possibile visualizzare i dettagli che consentono di approvare o rifiutare la decisione.

**Ruolo prerequisito:** Approvatore

1. Fare clic sul collegamento **Visualizza** per aprire il riquadro Richiesta di accesso.

1. Fare clic su **Dettagli** per visualizzare i dettagli relativi alla richiesta di accesso.

    I dettagli includono il nome dell'utente, l'organizzazione, la data di inizio e di fine dell'utente, se forniti, la motivazione aziendale, la data in cui la richiesta è stata inviata e la data di scadenza della richiesta.

1. Fare clic su **Approva** o **Nega**.

1. Se necessario, immettere un motivo.

    ![Portale di Accesso personale - Richiesta di accesso](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Fare clic su **Invia** per inviare la decisione.

    Se un criterio è configurato con più approvatori, solo un approvatore deve prendere una decisione sull'approvazione in sospeso. Dopo che un approvatore ha inviato la propria decisione alla richiesta di accesso, la richiesta viene completata e non è più disponibile per gli altri approvatori di approvare o rifiutare la richiesta. Gli altri approvatori possono visualizzare la decisione relativa alla richiesta e il decisore nel portale My Access. Al momento, è supportata solo l'approvazione in una singola fase.

    Se nessuno degli approvatori configurati è in grado di approvare o rifiutare la richiesta di accesso, la richiesta scade dopo la durata della richiesta configurata. L'utente riceve una notifica che la richiesta di accesso è scaduta e che è necessario inviare nuovamente la richiesta di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accessoRequest access to an access package](entitlement-management-request-access.md)
- [Processo di richiesta e notifiche e-mail](entitlement-management-process.md)
