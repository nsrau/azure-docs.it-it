---
title: Creare e gestire un catalogo di risorse in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come creare un nuovo contenitore di risorse e accedere ai pacchetti in Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85479abfd19110703d5ce9f59f545830e84feadb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429598"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management-preview"></a>Creare e gestire un catalogo di risorse in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Creare un catalogo

Un catalogo è un contenitore di risorse e pacchetti di accesso. Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Chi crea il catalogo diventa il primo proprietario del catalogo. Un proprietario del catalogo può aggiungere altri proprietari del catalogo.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o autore del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi**.

    ![Cataloghi di gestione dei diritti nel portale di Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Fare clic su **nuovo catalogo**.

1. Immettere un nome univoco per il catalogo e specificare una descrizione.

    Gli utenti visualizzeranno queste informazioni nei dettagli del pacchetto di accesso.

1. Se si desidera che i pacchetti di accesso in questo catalogo siano disponibili per richiedere agli utenti non appena vengono creati, impostare **abilitato** su **Sì**.

1. Se si desidera consentire agli utenti di directory esterne selezionate di richiedere pacchetti di accesso in questo catalogo, impostare **abilitato per utenti esterni** su **Sì**.

    ![Nuovo riquadro Catalogo](./media/entitlement-management-shared/new-catalog.png)

1. Fare clic su **Crea** per creare il catalogo.

## <a name="add-resources-to-a-catalog"></a>Aggiungere risorse a un catalogo

Per includere le risorse in un pacchetto di accesso, le risorse devono esistere in un catalogo. I tipi di risorse che è possibile aggiungere sono i gruppi, le applicazioni e i siti di SharePoint Online. I gruppi possono essere gruppi di Office 365 creati dal cloud o gruppi di sicurezza Azure AD creati dal cloud. Le applicazioni possono essere Azure AD applicazioni aziendali, incluse le applicazioni SaaS e le applicazioni federate a Azure AD. I siti possono essere siti di SharePoint Online o raccolte di siti di SharePoint Online.

**Ruolo prerequisiti:** Vedere i [ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo al quale si desidera aggiungere risorse.

1. Nel menu a sinistra fare clic su **risorse**.

1. Fare clic su **Aggiungi risorse**.

1. Fare clic su un tipo di risorsa: **gruppi e team**, **applicazioni**o **siti di SharePoint**.

    Se non viene visualizzata una risorsa che si vuole aggiungere o se non si è in grado di aggiungere una risorsa, verificare di avere il ruolo Azure AD della directory e il ruolo di gestione dei diritti richiesti. Potrebbe essere necessario disporre di un utente con i ruoli necessari per aggiungere la risorsa al catalogo. Per ulteriori informazioni, vedere [ruoli obbligatori per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selezionare una o più risorse del tipo che si desidera aggiungere al catalogo.

    ![Aggiungere risorse a un catalogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Al termine, fare clic su **Aggiungi**.

    Queste risorse possono ora essere incluse nei pacchetti di accesso all'interno del catalogo.

## <a name="remove-resources-from-a-catalog"></a>Rimuovere risorse da un catalogo

È possibile rimuovere le risorse da un catalogo. Una risorsa può essere rimossa solo da un catalogo se non viene usata in nessuno dei pacchetti di accesso del catalogo.

**Ruolo prerequisiti:** Vedere i [ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo dal quale si desidera rimuovere le risorse.

1. Nel menu a sinistra fare clic su **risorse**.

1. Selezionare le risorse che si desidera rimuovere.

1. Fare clic su **Rimuovi** (oppure fare clic sui puntini di sospensione ( **...** ) e quindi fare clic su **Rimuovi risorsa**.

## <a name="add-additional-catalog-owners"></a>Aggiunta di proprietari di cataloghi aggiuntivi

L'utente che ha creato un catalogo diventa il primo proprietario del catalogo. Per delegare la gestione di un catalogo, è necessario aggiungere gli utenti al ruolo di proprietario del catalogo. Ciò consente di condividere le responsabilità di gestione del catalogo. 

Per assegnare un utente al ruolo di proprietario del catalogo, attenersi alla procedura seguente:

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo al quale si desidera aggiungere gli amministratori.

1. Nel menu a sinistra fare clic su **ruoli e amministratori**.

    ![Ruoli e amministratori di cataloghi](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Fare clic su **Aggiungi proprietari** per selezionare i membri per questi ruoli.

1. Fare clic su **Seleziona** per aggiungere questi membri.

## <a name="edit-a-catalog"></a>Modificare un catalogo

È possibile modificare il nome e la descrizione di un catalogo. Gli utenti visualizzano queste informazioni nei dettagli di un pacchetto di accesso.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo che si desidera modificare.

1. Nella pagina **Panoramica** del catalogo fare clic su **modifica**.

1. Modificare il nome, la descrizione o le impostazioni abilitate del catalogo.

    ![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

1. Fare clic su **Salva**

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di pacchetti di accesso.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo che si desidera eliminare.

1. Nella **Panoramica**del catalogo fare clic su **Elimina**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Delega della governance di accesso per accedere ai gestori di pacchetti](entitlement-management-delegate-managers.md)
