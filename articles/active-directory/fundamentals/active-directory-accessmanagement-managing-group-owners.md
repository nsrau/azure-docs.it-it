---
title: Passaggi successivi per la gestione dell'accesso tramite gruppi | Microsoft Docs
description: Procedura avanzata per la gestione dei gruppi di sicurezza e l'uso di questi gruppi per gestire l'accesso a una risorsa.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/12/2017
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: bdc8754253ce2567d957b4d6240fe52242aea2ea
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448945"
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
* [Azure Active Directory cmdlets for configuring group settings](../users-groups-roles/groups-settings-cmdlets.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](../active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](../connect/active-directory-aadconnect.md)
