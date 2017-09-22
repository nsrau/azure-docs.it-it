---
title: Gestione dei proprietari di un gruppo in Azure Active Directory | Microsoft Docs
description: Informazioni sulla gestione dei proprietari di un gruppo e su come usare questi gruppi per gestire l'accesso a una risorsa.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>Gestione dei proprietari di un gruppo
Quando un proprietario di risorse assegna l'accesso a una risorsa a un gruppo di Azure AD, l'appartenenza al gruppo viene gestita dal proprietario del gruppo. Il proprietario della risorsa delega in modo efficace l'autorizzazione ad assegnare agli utenti la loro risorsa al proprietario del gruppo.

## <a name="add-an-owner-to-a-group"></a>Aggiungere un proprietario a un gruppo

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi**.
2. Selezionare la scheda **Tutti i gruppi** e aprire il gruppo a cui si desidera aggiungere proprietari.
3. Selezionare **Proprietari** e quindi **Aggiungi proprietari**.
4. Nella pagina **Aggiungi proprietari** selezionare l'utente che si desidera aggiungere come proprietario del gruppo e quindi toccare o fare clic su **Seleziona**. 

## <a name="remove-an-owner-from-a-group"></a>Per rimuovere un proprietario da un gruppo

1. Nell'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) selezionare **Utenti e gruppi**.
2. Selezionare **Tutti i gruppi** e quindi aprire il gruppo da cui si desidera rimuovere proprietari.
3. Selezionare **Proprietari**, selezionare il proprietario che si desidera rimuovere dal gruppo, quindi toccare o fare clic su **Seleziona**.
4. Nel riquadro aperto per il proprietario selezionato, selezionare **Rimuovi**.

## <a name="additional-information"></a>Informazioni aggiuntive
Questi articoli contengono informazioni aggiuntive sui gruppi di Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

