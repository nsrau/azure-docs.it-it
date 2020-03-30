---
title: Richiedere un pacchetto di accesso - Gestione dei diritti di Azure ADRequest an access package - Azure AD entitlement management
description: Informazioni su come usare il portale My Access per richiedere l'accesso a un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to use the My Access portal to request access to an access package in Azure Active Directory entitlement management.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261749"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Richiedere l'accesso a un pacchetto di accesso nella gestione dei diritti di Azure ADRequest access to an access package in Azure AD entitlement management

Con la gestione dei diritti di Azure AD, un pacchetto di accesso consente un'impostazione una tantera di risorse e criteri che amministra automaticamente l'accesso per tutta la durata del pacchetto di accesso. 

Un gestore di pacchetti di accesso può configurare i criteri per richiedere l'approvazione per consentire agli utenti di avere accesso ai pacchetti di accesso. Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta per ottenere l'accesso. In questo articolo viene descritto come inviare una richiesta di accesso.

## <a name="sign-in-to-the-my-access-portal"></a>Accedere al portale My Access

Il primo passaggio consiste nell'accedere al portale My Access dove è possibile richiedere l'accesso a un pacchetto di accesso.

**Ruolo prerequisito:** Richiedente

1. Cercare un messaggio di posta elettronica o un messaggio del progetto o del manager aziendale con cui si sta lavorando. L'e-mail deve includere un link al pacchetto di accesso a cui avrai bisogno di accedere. Il collegamento `myaccess`inizia con , include un hint di directory e termina con un ID pacchetto di accesso.  (Per il governo degli `https://myaccess.microsoft.us` Stati Uniti, il dominio può essere invece.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Aprire il collegamento.

1. Accedere al portale My Access.

    Assicurati di usare il tuo account aziendale (di lavoro o dell'istituto di istruzione). In caso di dubbi, rivolgersi al progetto o al manager aziendale.

## <a name="request-an-access-package"></a>Richiedere un pacchetto di accesso

Dopo aver trovato il pacchetto di accesso nel portale My Access, è possibile inviare una richiesta.

**Ruolo prerequisito:** Richiedente

1. Trovare il pacchetto di accesso nell'elenco.  Se necessario, è possibile eseguire una ricerca digitando una stringa di ricerca e selezionando il filtro **Nome**, **Catalogo**o **Risorse** .

    ![Portale Accesso personale - Ricerca risorse](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se viene visualizzata la casella **Giustificazione aziendale,** digitare una motivazione per la necessità di accedere.

1. Se **l'opzione Richiesta per periodo specifico?** è abilitata, selezionare **Sì** o **No**.

1. Se necessario, specificare la data di inizio e la data di fine.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Al termine, fare clic su **Invia** per inviare la richiesta.

1. Fare clic su **Cronologia richieste** per visualizzare un elenco delle richieste e dello stato.

    Se il pacchetto di accesso richiede l'approvazione, la richiesta è ora in uno stato di approvazione in sospeso.

### <a name="select-a-policy"></a>Selezionare un criterio

Se si richiede l'accesso a un pacchetto di accesso con più criteri applicabili, è possibile che venga richiesto di selezionare un criterio. Ad esempio, un gestore di pacchetti di accesso potrebbe configurare un pacchetto di accesso con due criteri per due gruppi di dipendenti interni. Il primo criterio potrebbe consentire l'accesso per 60 giorni e richiedere l'approvazione. Il secondo criterio potrebbe consentire l'accesso per 2 giorni e non richiedere l'approvazione. Se si verifica questo scenario, è necessario selezionare il criterio che si desidera utilizzare.

![Portale Accesso personale - Richiedi accesso - più criteri](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Inviare nuovamente una richiestaResubmit a request

Quando richiedi l'accesso a un pacchetto di accesso, la richiesta potrebbe essere negata o la richiesta potrebbe scadere se gli approvatori non rispondono in tempo. Se è necessario accedere, è possibile riprovare e inviare nuovamente la richiesta. Nella procedura seguente viene illustrato come inviare nuovamente una richiesta di accesso:The following procedure explains how to resubmit an access request:

**Ruolo prerequisito:** Richiedente

1. Accedere al portale **My Access.**

1. Fai clic su **Cronologia richieste** dal menu di navigazione a sinistra.

1. Individuare il pacchetto di accesso per il quale si invia nuovamente una richiesta.

1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

1. Fare clic sul collegamento blu **Visualizza** a destra del pacchetto di accesso selezionato.
    
    ![Seleziona pacchetto di accesso e visualizza link](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Verrà aperto un riquadro a destra con la cronologia delle richieste per il pacchetto di accesso.
    
    ![Seleziona il pulsante Invia di nuovo](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Fare clic sul pulsante **Invia di nuovo** nella parte inferiore del riquadro.

## <a name="cancel-a-request"></a>Annullare una richiesta

Se si invia una richiesta di accesso e la richiesta è ancora nello stato **di approvazione in sospeso,** è possibile annullare la richiesta.

**Ruolo prerequisito:** Richiedente

1. Nel portale My Access, a sinistra, fare clic su **Cronologia richieste** per visualizzare un elenco delle richieste e dello stato.

1. Fare clic sul collegamento **Visualizza** per la richiesta che si desidera annullare.

1. Se la richiesta è ancora nello stato **di approvazione in sospeso,** è possibile fare clic su **Annulla richiesta** per annullare la richiesta.

    ![Portale My Access - Annulla richiesta](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Fare clic su **Cronologia richieste** per confermare l'annullamento della richiesta.

## <a name="next-steps"></a>Passaggi successivi

- [Approvare o rifiutare le richieste di accesso](entitlement-management-request-approve.md)
- [Processo di richiesta e notifiche e-mail](entitlement-management-process.md)
