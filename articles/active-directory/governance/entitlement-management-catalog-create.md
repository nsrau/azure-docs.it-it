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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541616"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Creare e gestire un catalogo in Gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione dei diritti di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Creare un catalogo

Un catalogo è un contenitore di risorse e i pacchetti di accesso. Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Chiunque può creare il catalogo diventa il proprietario del catalogo prima. Un proprietario del catalogo possa aggiungere proprietari catalogo aggiuntivi.

**Ruolo prerequisiti:** L'utente amministratore o creatore del catalogo

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

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

Per includere le risorse in un pacchetto di accesso, le risorse devono esistere in un catalogo. I tipi di risorse che è possibile aggiungere sono i gruppi, applicazioni e siti di SharePoint Online.

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure, fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere risorse a.

1. Nel menu a sinistra, fare clic su **risorse**.

1. Fare clic su **aggiungere le risorse**.

1. Fare clic su un tipo di risorsa: **I gruppi**, **Applications**, o **siti SharePoint**.

    Se sei un creatore di catalogo, è possibile aggiungere qualsiasi gruppo di Office 365 o il gruppo di sicurezza di Azure AD che si è proprietari per il catalogo. Se è presente un gruppo che si desidera assegnare agli utenti, ma non si è proprietari del gruppo, è necessario avere un utente come amministratore di aggiungere il gruppo al catalogo.

    Se sei un creatore di catalogo, è possibile aggiungere qualsiasi applicazione aziendale di Azure AD si è proprietari, tra cui le applicazioni SaaS e applicazioni personalizzate federato ad Azure AD, al catalogo. Se è presente un'applicazione che si desidera assegnare agli utenti ma non si è proprietari, è necessario avere un utente come amministratore di aggiungere tale applicazione per il catalogo. Dopo l'applicazione fa parte del catalogo, è possibile selezionare uno dei ruoli dell'applicazione in un pacchetto di accesso.

1. Selezionare una o più risorse del tipo che si desidera aggiungere al catalogo.

1. Al termine, fare clic su **Add**.

    Queste risorse possono ora essere inclusi nei pacchetti di accesso all'interno del catalogo.

## <a name="remove-resources-from-a-catalog"></a>Rimuovere le risorse da un catalogo

È possibile rimuovere le risorse da un catalogo. Una risorsa può essere rimossa solo da un catalogo se non utilizzato in uno dei pacchetti di accesso del catalogo.

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure, fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera rimuovere risorse dalla.

1. Nel menu a sinistra, fare clic su **risorse**.

1. Selezionare le risorse che si desidera rimuovere.

1. Fare clic su **rimuovere** (oppure fare clic sui puntini di sospensione (**...** ) e quindi fare clic su **rimuovere risorse**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Aggiungere i proprietari del catalogo o accedere a strumenti di gestione pacchetti

Se si desidera delegare la gestione di pacchetti di accesso nel catalogo o il catalogo, i proprietari di catalogo aggiunti o accedere a strumenti di gestione pacchetti. Utente che crea un catalogo diventa il proprietario del catalogo prima.

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure, fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo da aggiungere agli amministratori.

1. Nel menu a sinistra, fare clic su **ruoli e gli amministratori**.

1. Fare clic su **Aggiungi proprietari** oppure **aggiungere i gestori di pacchetti di accesso** per selezionare i membri per questi ruoli.

1. Fare clic su **seleziona** per aggiungere questi membri.

## <a name="edit-a-catalog"></a>Modifica di un catalogo

È possibile modificare il nome e una descrizione per un catalogo. Gli utenti visualizzano queste informazioni nei dettagli del pacchetto di un accesso.

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure, fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo da modificare.

1. Il catalogo **Overview** pagina, fare clic su **modificare**.

1. Modifica nome o la descrizione del catalogo.

1. Fare clic su **Save**.

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di tutti i pacchetti di accesso.

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Nel portale di Azure, fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **cataloghi** e quindi aprire il catalogo a cui si desidera eliminare.

1. Il catalogo **Overview**, fare clic su **eliminare**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire un pacchetto di accesso](entitlement-management-access-package-create.md)
