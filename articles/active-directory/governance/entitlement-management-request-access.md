---
title: Richiedere l'accesso a un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come usare il portale di accesso personale per richiedere l'accesso a un pacchetto di accesso in Azure Active Directory gestione dei diritti.
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
ms.openlocfilehash: 81697293be235c12ca009c76a9fd9d3975e232fc
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554069"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Richiedere l'accesso a un pacchetto di accesso in Azure AD gestione dei diritti

Con Azure AD gestione dei diritti, un pacchetto di accesso consente di impostare una sola volta le risorse e i criteri che gestiscono automaticamente l'accesso per tutta la durata del pacchetto di accesso. 

Una gestione pacchetti di accesso può configurare i criteri per richiedere l'approvazione per consentire agli utenti di accedere ai pacchetti. Un utente che deve accedere a un pacchetto di accesso può inviare una richiesta per ottenere l'accesso. Questo articolo descrive come inviare una richiesta di accesso.

## <a name="sign-in-to-the-my-access-portal"></a>Accedere al portale di accesso personale

Il primo passaggio consiste nell'accedere al portale di accesso personale, in cui è possibile richiedere l'accesso a un pacchetto di accesso.

**Ruolo prerequisiti:** Richiedente

1. Cercare un messaggio di posta elettronica o un messaggio dal progetto o dal gestore di business che si sta utilizzando. Il messaggio di posta elettronica deve includere un collegamento al pacchetto di accesso a cui sarà necessario accedere. Il collegamento inizia con `myaccess`, include un hint di directory e termina con un ID pacchetto di accesso.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Aprire il collegamento.

1. Accedere al portale di accesso personale.

    Assicurarsi di usare l'account aziendale o dell'Istituto di istruzione. Se non si è certi, rivolgersi al progetto o al responsabile di business.

## <a name="request-an-access-package"></a>Richiedere un pacchetto di accesso

Dopo aver trovato il pacchetto di accesso nel portale di accesso personale, è possibile inviare una richiesta.

**Ruolo prerequisiti:** Richiedente

1. Trovare il pacchetto di accesso nell'elenco.  Se necessario, è possibile eseguire una ricerca digitando una stringa di ricerca e quindi selezionando il **nome**, il **Catalogo**o il filtro **risorse** .

    ![Portale di accesso personale-ricerca risorse](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se viene visualizzata la casella **motivazione aziendale** , digitare una giustificazione per la necessità di accesso.

1. Se la **richiesta per un periodo specifico** è abilitata, selezionare **Sì** o **No**.

1. Se necessario, specificare la data di inizio e la data di fine.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Al termine, fare clic su **Invia** per inviare la richiesta.

1. Fare clic su **Cronologia richieste** per visualizzare un elenco delle richieste e dello stato.

    Se il pacchetto di accesso richiede l'approvazione, la richiesta è ora in uno stato di approvazione in sospeso.

### <a name="select-a-policy"></a>Selezionare un criterio

Se si richiede l'accesso a un pacchetto di accesso con più criteri applicabili, potrebbe essere richiesto di selezionare un criterio. Una gestione pacchetti di Access, ad esempio, potrebbe configurare un pacchetto di accesso con due criteri per due gruppi di dipendenti interni. Il primo criterio potrebbe consentire l'accesso per 60 giorni e richiedere l'approvazione. Il secondo criterio potrebbe consentire l'accesso per 2 giorni e non richiede l'approvazione. Se si verifica questo scenario, è necessario selezionare il criterio che si desidera utilizzare.

![Portale di accesso personale-richiesta di accesso-più criteri](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Inviare nuovamente una richiesta

Quando si richiede l'accesso a un pacchetto di Access, la richiesta potrebbe essere negata o la richiesta potrebbe scadere se i responsabili approvazione non rispondono nel tempo. Se è necessario l'accesso, è possibile riprovare e inviare di nuovo la richiesta. Nella procedura seguente viene illustrato come inviare nuovamente una richiesta di accesso:

**Ruolo prerequisiti:** Richiedente

1. Accedere al portale di **accesso personale** .

1. Fare clic su **Cronologia richieste** dal menu di spostamento a sinistra.

1. Trovare il pacchetto di accesso per cui si sta riinviando una richiesta.

1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

1. Fare clic sul collegamento **visualizzazione** blu a destra del pacchetto di accesso selezionato.
    
    ![Selezionare il collegamento di accesso al pacchetto e alla visualizzazione](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Un riquadro si aprirà a destra con la cronologia delle richieste per il pacchetto di accesso.
    
    ![Selezionare il pulsante Invia di più](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Fare clic sul pulsante **Invia** di più nella parte inferiore del riquadro.

## <a name="cancel-a-request"></a>Annulla una richiesta

Se si invia una richiesta di accesso e la richiesta è ancora in stato di **approvazione in sospeso** , è possibile annullare la richiesta.

**Ruolo prerequisiti:** Richiedente

1. Nel portale di accesso personale, a sinistra, fare clic su **Richiedi cronologia** per visualizzare un elenco delle richieste e dello stato.

1. Fare clic sul collegamento **Visualizza** per la richiesta che si desidera annullare.

1. Se la richiesta è ancora in stato di **approvazione in sospeso** , è possibile fare clic su **Annulla richiesta** per annullare la richiesta.

    ![Portale di accesso personale-richiesta di annullamento](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Fare clic su **Cronologia richieste** per confermare che la richiesta è stata annullata.

## <a name="next-steps"></a>Passaggi successivi

- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
