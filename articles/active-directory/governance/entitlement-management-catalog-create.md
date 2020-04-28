---
title: Creare & gestire un catalogo di risorse nella gestione dei diritti-Azure AD
description: Informazioni su come creare un nuovo contenitore di risorse e accedere ai pacchetti in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437562"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creare e gestire un catalogo di risorse in Azure AD gestione dei diritti

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

### <a name="creating-a-catalog-programmatically"></a>Creazione di un catalogo a livello di codice

È anche possibile creare un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione che dispone dell' `EntitlementManagement.ReadWrite.All` autorizzazione delegata può chiamare l'API per [creare un accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

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

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Aggiunta di una risorsa a un catalogo a livello di codice

È anche possibile aggiungere una risorsa a un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato o un catalogo e un proprietario di risorse con un'applicazione che dispone dell' `EntitlementManagement.ReadWrite.All` autorizzazione delegata può chiamare l'API per [creare un accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Rimuovere risorse da un catalogo

È possibile rimuovere le risorse da un catalogo. Una risorsa può essere rimossa solo da un catalogo se non viene usata in nessuno dei pacchetti di accesso del catalogo.

**Ruolo prerequisiti:** Vedere i [ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo dal quale si desidera rimuovere le risorse.

1. Nel menu a sinistra fare clic su **risorse**.

1. Selezionare le risorse che si desidera rimuovere.

1. Fare clic su **Rimuovi** (oppure fare clic sui puntini di sospensione (**...**) e quindi fare clic su **Rimuovi risorsa**.

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

1. Fare clic su **Salva**.

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di pacchetti di accesso.

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo che si desidera eliminare.

1. Nella **Panoramica**del catalogo fare clic su **Elimina**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

### <a name="deleting-a-catalog-programmatically"></a>Eliminazione di un catalogo a livello di codice

È anche possibile eliminare un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione che dispone dell' `EntitlementManagement.ReadWrite.All` autorizzazione delegata può chiamare l'API per [eliminare un accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Passaggi successivi

- [Delega della governance di accesso per accedere ai gestori di pacchetti](entitlement-management-delegate-managers.md)
