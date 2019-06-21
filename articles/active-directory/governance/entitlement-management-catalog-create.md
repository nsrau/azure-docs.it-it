---
title: Creare e gestire un catalogo in Gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come creare un nuovo contenitore di risorse e i pacchetti di accesso nella gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/29/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5988f4723f1ef73cf0767ef8ac1b9adf3c1435d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190234"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Creare e gestire un catalogo in Gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Creare un catalogo

Un catalogo è un contenitore di risorse e i pacchetti di accesso. Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Chiunque può creare il catalogo diventa il proprietario del catalogo prima. Un proprietario del catalogo possa aggiungere proprietari catalogo aggiuntivi.

**Ruolo prerequisito:** L'utente amministratore o creatore del catalogo

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi**.

    ![Cataloghi di gestione dei diritti nel portale di Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Fare clic su **nuovo catalogo**.

1. Immettere un nome univoco per il catalogo e fornire una descrizione.

    Gli utenti vedranno queste informazioni nei dettagli del pacchetto di un accesso.

1. Se si desidera che i pacchetti di accesso in questo catalogo sia disponibile agli utenti di richiedere, non appena vengono creati, impostare **Enabled** al **Yes**.

1. Se si desidera consentire agli utenti nella directory esterne selezionate per essere in grado di richiedere i pacchetti di accesso in questo catalogo, impostare **abilitata per gli utenti esterni** al **Yes**.

    ![Nuovo riquadro catalogo](./media/entitlement-management-catalog-create/new-catalog.png)

1. Fare clic su **Create** per creare il catalogo.

## <a name="add-resources-to-a-catalog"></a>Aggiungere risorse a un catalogo

Per includere le risorse in un pacchetto di accesso, le risorse devono esistere in un catalogo. I tipi di risorse che è possibile aggiungere sono i gruppi, applicazioni e siti di SharePoint Online. I gruppi possono essere cloud creato gruppi di Office 365 o Azure cloud creato gruppi di sicurezza di Active Directory. Le applicazioni possono essere applicazioni enterprise di Azure AD, tra cui le applicazioni SaaS e le proprie applicazioni federati ad Azure AD. I siti possono essere siti di SharePoint Online o SharePoint Online raccolte siti.

**Ruolo prerequisito:** Vedere [necessari ruoli per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere risorse a.

1. Nel menu a sinistra, fare clic su **risorse**.

1. Fare clic su **aggiungere le risorse**.

1. Fare clic su un tipo di risorsa: **I gruppi**, **Applications**, o **siti SharePoint**.

    Se non viene visualizzata una risorsa che si desidera aggiungere o è in grado di aggiungere una risorsa, assicurarsi di avere la ruolo della directory Azure AD e il ruolo di gestione dei diritti. Si potrebbe essere necessario a un utente con i ruoli richiesti aggiungere la risorsa di catalogo. Per altre informazioni, vedere [necessari ruoli per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selezionare una o più risorse del tipo che si desidera aggiungere al catalogo.

1. Al termine, fare clic su **Add**.

    Queste risorse possono ora essere inclusi nei pacchetti di accesso all'interno del catalogo.

## <a name="remove-resources-from-a-catalog"></a>Rimuovere le risorse da un catalogo

È possibile rimuovere le risorse da un catalogo. Una risorsa può essere rimossa solo da un catalogo se non utilizzato in uno dei pacchetti di accesso del catalogo.

**Ruolo prerequisito:** Vedere [necessari ruoli per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera rimuovere risorse dalla.

1. Nel menu a sinistra, fare clic su **risorse**.

1. Selezionare le risorse che si desidera rimuovere.

1. Fare clic su **rimuovere** (oppure fare clic sui puntini di sospensione ( **...** ) e quindi fare clic su **rimuovere risorse**).

## <a name="edit-a-catalog"></a>Modifica di un catalogo

È possibile modificare il nome e una descrizione per un catalogo. Gli utenti visualizzano queste informazioni nei dettagli del pacchetto di un accesso.

**Ruolo prerequisito:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo da modificare.

1. Il catalogo **Overview** pagina, fare clic su **modificare**.

1. Modifica nome o la descrizione del catalogo.

1. Fare clic su **Save**.

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di tutti i pacchetti di accesso.

**Ruolo prerequisito:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera eliminare.

1. Il catalogo **Overview**, fare clic su **eliminare**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere un creatore di catalogo](entitlement-management-delegate.md#add-a-catalog-creator)
- [Creare e gestire un pacchetto di accesso](entitlement-management-access-package-create.md)
