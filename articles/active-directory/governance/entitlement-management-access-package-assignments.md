---
title: Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso nella gestione dei diritti di Azure AD - Azure Active DirectoryView, add, and remove assignments for an access package in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to view, add, and remove assignments for an access package in Azure Active Directory entitlement management.
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
ms.openlocfilehash: 12e3b86f41e7188778393ab717554907ef5d44ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631737"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso nella gestione dei diritti di Azure ADView, add, and remove assignments for an access package in Azure AD entitlement management

Nella gestione dei diritti di Azure AD è possibile vedere chi è stato assegnato ai pacchetti di accesso, ai criteri e allo stato. Se un pacchetto di accesso dispone di un criterio appropriato, è anche possibile assegnare direttamente l'utente a un pacchetto di accesso. In questo articolo viene descritto come visualizzare, aggiungere e rimuovere le assegnazioni per i pacchetti di accesso.

## <a name="prerequisites"></a>Prerequisiti

Per usare la gestione dei diritti di Azure AD e assegnare gli utenti ai pacchetti di accesso, è necessario disporre di una delle licenze seguenti:To use Azure AD entitlement management and assign users to access packages, you must have one of the following licenses:

- Azure AD Premium P2
- Licenza di Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Visualizzare chi ha un compito

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Fare clic su **Assegnazioni** per visualizzare un elenco di assegnazioni attive.

    ![Elenco delle assegnazioni a un pacchetto di accesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Fare clic su un'assegnazione specifica per visualizzare ulteriori dettagli.

1. Per visualizzare un elenco delle assegnazioni per cui non è stato eseguito correttamente il provisioning di tutti i ruoli delle risorse, fare clic sullo stato del filtro e selezionare **Recapita**.

    È possibile visualizzare ulteriori dettagli sugli errori di recapito individuando la richiesta corrispondente dell'utente nella pagina **Richieste.**

1. Per visualizzare le assegnazioni scadute, fare clic sullo stato del filtro e selezionare **Scaduto**.

1. Per scaricare un file CSV dell'elenco filtrato, fare clic su **Scarica**.

### <a name="viewing-assignments-programmatically"></a>Visualizzazione delle assegnazioni a livello di codiceViewing assignments programmatically

È anche possibile recuperare le assegnazioni in un pacchetto di accesso utilizzando Microsoft Graph.You can also retrieve assignments in an access package using Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione con l'autorizzazione delegata può chiamare l'API per [elencare accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Assegnare direttamente un utente

In alcuni casi, potrebbe essere necessario assegnare direttamente utenti specifici a un pacchetto di accesso in modo che gli utenti non debbano eseguire il processo di richiesta del pacchetto di accesso. Per assegnare direttamente gli utenti, il pacchetto di accesso deve disporre di un criterio che consenta assegnazioni dirette all'amministratore.

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **Assegnazioni.**

1. Fare clic su **Nuova assegnazione** per aprire Aggiungi utente per accedere al pacchetto.

    ![Assegnazioni - Aggiungere un utente per accedere al pacchetto](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Fare clic su **Aggiungi utenti** per selezionare gli utenti a cui si desidera assegnare il pacchetto di accesso.

1. Nell'elenco **Seleziona criteri** selezionare un criterio per il futuro e il ciclo di vita degli utenti. Se si desidera che gli utenti selezionati abbiano impostazioni dei criteri diverse, è possibile fare clic su **Crea nuovo criterio** per aggiungere un nuovo criterio.

1. Impostare la data e l'ora di inizio e di fine dell'assegnazione degli utenti selezionati. Se non viene fornita una data di fine, verranno utilizzate le impostazioni del ciclo di vita del criterio.

1. Facoltativamente, fornire una giustificazione per l'assegnazione diretta per la conservazione dei record.

1. Fare clic su **Aggiungi** per assegnare direttamente gli utenti selezionati al pacchetto di accesso.

    Dopo alcuni istanti, fare clic su **Aggiorna** per visualizzare gli utenti nell'elenco Assegnazioni.

### <a name="directly-assigning-users-programmatically"></a>Assegnazione diretta degli utenti a livello di codice

È anche possibile assegnare direttamente un utente a un pacchetto di accesso utilizzando Microsoft Graph.You can also directly assign a user to an access package using Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione con l'autorizzazione delegata può chiamare l'API per [creare un oggetto accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Rimuovere un'assegnazione

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nel menu a sinistra, fare clic su **Assegnazioni.**
 
1. Fare clic sulla casella di controllo accanto all'utente di cui si desidera rimuovere l'assegnazione dal pacchetto di accesso. 

1. Fare clic sul pulsante **Rimuovi** nella parte superiore del riquadro sinistro. 
 
    ![Assegnazioni - Rimuovi utente dal pacchetto di accesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Apparirà una notifica che informa che l'assegnazione è stata rimossa. 

## <a name="next-steps"></a>Passaggi successivi

- [Richiesta di modifica e impostazioni per un pacchetto di accesso](entitlement-management-access-package-request-policy.md)
- [Visualizzare report e log](entitlement-management-reports.md)
