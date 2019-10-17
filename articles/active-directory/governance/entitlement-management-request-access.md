---
title: Richiedere l'accesso a un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come usare il portale di accesso personale per richiedere l'accesso a un pacchetto di accesso in Azure Active Directory gestione dei diritti (anteprima).
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
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86ab995327522ac5dead9b3f944e0760084aedce
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389095"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Richiedere l'accesso a un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

    ![Portale di accesso personale-ricerca risorse](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. Fare clic sul segno di spunta per selezionare il pacchetto di accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

1. Se viene visualizzata la casella **motivazione aziendale** , digitare una giustificazione per la necessità di accesso.

1. Se la **richiesta per un periodo specifico** è abilitata, selezionare **Sì** o **No**.

1. Se necessario, specificare la data di inizio e la data di fine.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Al termine, fare clic su **Invia** per inviare la richiesta.

1. Fare clic su **Cronologia richieste** per visualizzare un elenco delle richieste e dello stato.

    Se il pacchetto di accesso richiede l'approvazione, la richiesta è ora in uno stato di approvazione in sospeso.

## <a name="cancel-a-request"></a>Annulla una richiesta

Se si invia una richiesta di accesso e la richiesta è ancora in stato di **approvazione in sospeso** , è possibile annullare la richiesta.

**Ruolo prerequisiti:** Richiedente

1. Nel portale di accesso personale, a sinistra, fare clic su **Richiedi cronologia** per visualizzare un elenco delle richieste e dello stato.

1. Fare clic sul collegamento **Visualizza** per la richiesta che si desidera annullare.

1. Se la richiesta è ancora in stato di **approvazione in sospeso** , è possibile fare clic su **Annulla richiesta** per annullare la richiesta.

    ![Portale di accesso personale-richiesta di annullamento](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Fare clic su **Cronologia richieste** per confermare che la richiesta è stata annullata.

## <a name="select-a-policy"></a>Selezionare un criterio

Se si richiede l'accesso a un pacchetto di accesso con più criteri applicabili, potrebbe essere richiesto di selezionare un criterio. Una gestione pacchetti di Access, ad esempio, potrebbe configurare un pacchetto di accesso con due criteri per due gruppi di dipendenti interni. Il primo criterio potrebbe consentire l'accesso per 60 giorni e richiedere l'approvazione. Il secondo criterio potrebbe consentire l'accesso per 2 giorni e non richiede l'approvazione. Se si verifica questo scenario, è necessario selezionare il criterio che si desidera utilizzare.

**Ruolo prerequisiti:** Richiedente

## <a name="next-steps"></a>Passaggi successivi

- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)
- [Richieste di processo e notifiche tramite posta elettronica](entitlement-management-process.md)
