---
title: Passaggi successivi per la gestione dell'accesso mediante i gruppi | Documentazione Microsoft
description: Procedura avanzata per la gestione dei gruppi di sicurezza e l'uso di questi gruppi per gestire l'accesso a una risorsa.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: 177e31b8b8aff90cd91135d02eb3a2d8c85e2c33
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
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
