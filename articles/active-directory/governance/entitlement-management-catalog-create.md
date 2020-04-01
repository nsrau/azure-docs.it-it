---
title: Creare & gestire un catalogo di risorse nella gestione dei diritti - Azure ADCreate & manage a catalog of resources in entitlement management - Azure AD
description: Informazioni su come creare un nuovo contenitore di risorse e pacchetti di accesso nella gestione dei diritti di Azure Active Directory.Learn how to create a new container of resources and access packages in Azure Active Directory entitlement management.
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437562"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creare e gestire un catalogo di risorse nella gestione dei diritti di Azure ADCreate and manage a catalog of resources in Azure AD entitlement management

## <a name="create-a-catalog"></a>Creare un catalogo

Un catalogo è un contenitore di risorse e pacchetti di accesso. Creare un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Chiunque crei il catalogo diventa il primo proprietario del catalogo. Il proprietario di un catalogo può aggiungere altri proprietari del catalogo.

**Ruolo prerequisito:** Amministratore globale, amministratore utente o creatore del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fare clic su **Cataloghi.**

    ![Cataloghi di gestione dei diritti nel portale di AzureEntitlement management catalogs in the Azure portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Fare clic su **Nuovo catalogo**.

1. Immettere un nome univoco per il catalogo e fornire una descrizione.

    Gli utenti vedranno queste informazioni nei dettagli di un pacchetto di accesso.

1. Se si desidera che i pacchetti di accesso in questo catalogo siano disponibili per la richiesta da parte degli utenti non appena vengono creati, impostare **Abilitato** su **Sì**.

1. Se si desidera consentire agli utenti delle directory esterne selezionate di richiedere pacchetti di accesso in questo catalogo, impostare **Abilitato per gli utenti esterni** su **Sì**.

    ![Nuovo riquadro del catalogo](./media/entitlement-management-shared/new-catalog.png)

1. Fare clic su **Crea** per creare il catalogo.

### <a name="creating-a-catalog-programmatically"></a>Creazione di un catalogo a livello di codiceCreating a catalog programmatically

È anche possibile creare un catalogo utilizzando Microsoft Graph.You can also create a catalog using Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione con l'autorizzazione delegata può chiamare l'API per [creare un oggetto accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Aggiungere risorse a un catalogo

Per includere risorse in un pacchetto di accesso, le risorse devono essere presenti in un catalogo. I tipi di risorse che è possibile aggiungere sono gruppi, applicazioni e siti di SharePoint Online. I gruppi possono essere gruppi di Office 365 creati nel cloud o gruppi di sicurezza di Azure AD creati dal cloud. Le applicazioni possono essere applicazioni aziendali di Azure AD, incluse le applicazioni SaaS e le proprie applicazioni federate in Azure AD. I siti possono essere siti di SharePoint Online o raccolte siti di SharePoint Online.

**Ruolo prerequisito:** Vedere [Ruoli necessari per aggiungere risorse a un catalogoSee Required roles to add resources to a catalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere risorse.

1. Nel menu a sinistra fare clic su **Risorse**.

1. Fare clic su **Aggiungi risorse**.

1. Fare clic su un tipo di risorsa: **Gruppi e team**, **Applicazioni**o Siti **di SharePoint**.

    Se non viene visualizzata una risorsa che si vuole aggiungere o non è possibile aggiungere una risorsa, assicurarsi di disporre del ruolo di gestione dei diritti e del ruolo di gestione dei diritti di Azure AD richiesto. Potrebbe essere necessario fare in modo che qualcuno con i ruoli necessari aggiuri la risorsa al catalogo. Per ulteriori informazioni, vedere [Ruoli necessari per aggiungere risorse a un catalogo.](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Selezionare una o più risorse del tipo che si desidera aggiungere al catalogo.

    ![Aggiungere risorse a un catalogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Al termine, fare clic su **Aggiungi**.

    Queste risorse possono ora essere incluse nei pacchetti di accesso all'interno del catalogo.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Aggiunta di una risorsa a un catalogo a livello di codiceAdding a resource to a catalog programmatically

È anche possibile aggiungere una risorsa a un catalogo usando Microsoft Graph.You can also add a resource to a catalog using Microsoft Graph.  Un utente con un ruolo appropriato, o un proprietario di catalogo `EntitlementManagement.ReadWrite.All` e risorsa, con un'applicazione che dispone dell'autorizzazione delegata può chiamare l'API per [creare un oggetto accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Rimuovere risorse da un catalogo

È possibile rimuovere risorse da un catalogo. Una risorsa può essere rimossa da un catalogo solo se non viene utilizzata in nessuno dei pacchetti di accesso del catalogo.

**Ruolo prerequisito:** Vedere [Ruoli necessari per aggiungere risorse a un catalogoSee Required roles to add resources to a catalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Cataloghi** e quindi aprire il catalogo da cui si desidera rimuovere le risorse.

1. Nel menu a sinistra fare clic su **Risorse**.

1. Selezionare le risorse da rimuovere.

1. Fare clic su **Rimuovi** oppure sui solips (**...**) e quindi su **Rimuovi risorsa**.

## <a name="add-additional-catalog-owners"></a>Aggiungere altri proprietari di cataloghi

L'utente che ha creato un catalogo diventa il primo proprietario del catalogo. Per delegare la gestione di un catalogo, aggiungere utenti al ruolo di proprietario del catalogo. Ciò consente di condividere le responsabilità di gestione del catalogo. 

Per assegnare un utente al ruolo di proprietario del catalogo, attenersi alla seguente procedura:

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere amministratori.

1. Nel menu a sinistra, fai clic su **Ruoli e amministratori.**

    ![Cataloga i ruoli e gli amministratori](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Fare clic su **Aggiungi proprietari** per selezionare i membri per questi ruoli.

1. Fare clic su **Seleziona** per aggiungere questi membri.

## <a name="edit-a-catalog"></a>Modificare un catalogo

È possibile modificare il nome e la descrizione di un catalogo. Gli utenti vedono queste informazioni nei dettagli di un pacchetto di accesso.

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Cataloghi,** quindi apri il catalogo che desideri modificare.

1. Nella pagina **Panoramica** del catalogo fare clic su **Modifica**.

1. Modificare il nome, la descrizione o le impostazioni abilitate del catalogo.

    ![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

1. Fare clic su **Salva**.

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di pacchetti di accesso.

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Cataloghi,** quindi apri il catalogo che desideri eliminare.

1. In **Panoramica**del catalogo fare clic su **Elimina**.

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

### <a name="deleting-a-catalog-programmatically"></a>Eliminazione di un catalogo a livello di codiceDeleting a catalog programmatically

È inoltre possibile eliminare un catalogo utilizzando Microsoft Graph.You can also delete a catalog using Microsoft Graph.  Un utente con un ruolo appropriato con `EntitlementManagement.ReadWrite.All` un'applicazione che dispone dell'autorizzazione delegata può chiamare l'API per [eliminare un oggetto accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Passaggi successivi

- [Delegare la governance dell'accesso per accedere ai gestori di pacchettiDelegate access governance to access package manager](entitlement-management-delegate-managers.md)
