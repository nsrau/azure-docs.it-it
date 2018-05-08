---
title: Passaggi successivi per la gestione dell'accesso mediante i gruppi | Documentazione Microsoft
description: Procedura avanzata per la gestione dei gruppi di sicurezza e l'uso di questi gruppi per gestire l'accesso a una risorsa.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e4703ab5d9f4b8593e758764e50455672e368e18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="managing-owners-for-a-group"></a>Gestione dei proprietari di un gruppo
Quando il proprietario delle risorse ha assegnato l'accesso a una risorsa a un gruppo di Azure AD, l'appartenenza al gruppo viene gestita dal proprietario del gruppo. Il proprietario della risorsa delega in modo efficace l'autorizzazione ad assegnare agli utenti la loro risorsa al proprietario del gruppo.

## <a name="add-an-owner-to-a-group"></a>Aggiungere un proprietario a un gruppo

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi**.
2. Selezionare la scheda **Tutti i gruppi** e aprire il gruppo a cui si desidera aggiungere proprietari.
3. Selezionare **Aggiungi proprietari**.
4. Nella pagina **Aggiungi proprietari** selezionare l'utente che si desidera aggiungere come proprietario del gruppo e verificare che questo nome venga aggiunto al riquadro **Selezionato**.

## <a name="remove-an-owner-from-a-group"></a>Per rimuovere un proprietario da un gruppo

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi**.
2. Selezionare **Tutti i gruppi** e quindi aprire il gruppo da cui si desidera rimuovere proprietari.
3. Selezionare la scheda **Proprietari** .
4. Selezionare il proprietario da rimuovere dal gruppo e quindi scegliere **Rimuovi**.

## <a name="additional-information"></a>Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
