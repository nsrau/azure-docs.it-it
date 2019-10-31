---
title: Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso in Azure AD gestione dei diritti Azure Active Directory
description: Informazioni su come visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso in Azure Active Directory gestione dei diritti.
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
ms.openlocfilehash: 3f06da43e758c24da65664b34dd83f876a00e3a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174902"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso in Azure AD gestione dei diritti

In Azure AD gestione dei diritti, è possibile visualizzare gli utenti a cui è stato assegnato l'accesso ai pacchetti, i relativi criteri e lo stato. Se un pacchetto di accesso dispone di un criterio appropriato, è anche possibile assegnare direttamente un utente a un pacchetto di accesso. Questo articolo descrive come visualizzare, aggiungere e rimuovere le assegnazioni per i pacchetti di accesso.

## <a name="view-who-has-an-assignment"></a>Visualizza chi ha un'assegnazione

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **assegnazioni** per visualizzare un elenco di assegnazioni attive.

    ![Elenco di assegnazioni a un pacchetto di accesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Fare clic su un'assegnazione specifica per visualizzare altri dettagli.

1. Per visualizzare un elenco di assegnazioni in cui non è stato eseguito correttamente il provisioning di tutti i ruoli delle risorse, fare clic sullo stato del filtro e selezionare **distribuzione**.

    È possibile visualizzare ulteriori dettagli sugli errori di recapito individuando la richiesta corrispondente dell'utente nella pagina **richieste** .

1. Per visualizzare le assegnazioni scadute, fare clic sullo stato del filtro e selezionare **scaduto**.

1. Per scaricare un file CSV dell'elenco filtrato, fare clic su **download**.

## <a name="directly-assign-a-user"></a>Assegnare direttamente un utente

In alcuni casi, potrebbe essere necessario assegnare direttamente utenti specifici a un pacchetto di accesso in modo che gli utenti non debbano eseguire il processo di richiesta del pacchetto di accesso. Per assegnare direttamente gli utenti, il pacchetto di accesso deve disporre di un criterio che consenta l'assegnazione diretta dell'amministratore.

**Ruolo prerequisiti:** Amministratore globale, Amministratore utenti, proprietario del catalogo o gestione pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nel menu a sinistra fare clic su **assegnazioni**.

1. Fare clic su **nuova assegnazione** per aprire Aggiungi utente per accedere al pacchetto.

    ![Assegnazioni-Aggiungi utente al pacchetto di accesso](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Fare clic su **Aggiungi utenti** per selezionare gli utenti a cui si desidera assegnare il pacchetto di accesso.

1. Nell'elenco **Seleziona criteri** selezionare un criterio che le richieste future e il ciclo di vita degli utenti verranno gestiti e monitorati da. Se si desidera che gli utenti selezionati dispongano di impostazioni dei criteri diverse, è possibile fare clic su **Crea nuovo criterio** per aggiungere un nuovo criterio.

1. Consente di impostare la data e l'ora di inizio e fine dell'assegnazione degli utenti selezionati. Se non viene specificata una data di fine, verranno usate le impostazioni del ciclo di vita del criterio.

1. Facoltativamente, fornire una giustificazione per l'assegnazione diretta per la conservazione dei record.

1. Fare clic su **Aggiungi** per assegnare direttamente gli utenti selezionati al pacchetto di accesso.

    Dopo alcuni istanti, fare clic su **Aggiorna** per visualizzare gli utenti nell'elenco assegnazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare la richiesta e le impostazioni per un pacchetto di accesso](entitlement-management-access-package-request-policy.md)
- [Visualizzare report e log](entitlement-management-reports.md)
