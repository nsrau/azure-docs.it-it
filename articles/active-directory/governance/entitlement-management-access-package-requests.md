---
title: Visualizzare e gestire le richieste per un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come visualizzare, rielaborare e annullare le richieste per un pacchetto di accesso in Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430292"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Visualizzare e gestire le richieste per un pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In Azure AD gestione dei diritti, è possibile visualizzare gli utenti che hanno richiesto l'accesso ai pacchetti, i relativi criteri e lo stato. Questo articolo descrive come visualizzare, rielaborare e annullare le richieste per i pacchetti di accesso.

## <a name="view-requests"></a>Visualizza richieste

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic su una richiesta specifica per visualizzare altri dettagli.

    ![Elenco di richieste per un pacchetto di accesso](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>Visualizzare gli errori di recapito di una richiesta

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Selezionare la richiesta che si desidera visualizzare.

    Se la richiesta presenta errori di recapito, lo stato della richiesta sarà non **recapitato** o **parzialmente recapitato**.

    Se si verificano errori di recapito, nel riquadro dei dettagli della richiesta sarà presente un conteggio degli errori di recapito.

1. Fare clic sul conteggio per visualizzare tutti gli errori di recapito della richiesta.

## <a name="reprocess-a-request"></a>Rielaborare una richiesta

Se una richiesta rileva un errore, è possibile rielaborare la richiesta per riprovare. È possibile rielaborare solo una richiesta con uno stato di **recapito non riuscito** o **parzialmente recapitato** e una data completata inferiore a una settimana.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera rielaborare.

1. Nel riquadro Dettagli richiesta fare clic su **rielaborare la richiesta**.

    ![Rielaborare una richiesta non riuscita](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>Annullare una richiesta in sospeso

È possibile annullare una richiesta in sospeso che non è ancora stata recapitata o il cui recapito non è riuscito.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **richieste**.

1. Fare clic sulla richiesta che si desidera annullare.

1. Nel riquadro Dettagli richiesta fare clic su **Annulla richiesta**.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso](entitlement-management-access-package-request-policy.md)
- [Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso](entitlement-management-access-package-assignments.md)