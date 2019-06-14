---
title: Eliminare un gruppo - Azure Active Directory | Microsoft Docs
description: Istruzioni su come eliminare un gruppo con Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9543908aafbb4ecd8f642f766f656f780706a36
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60249176"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Eliminare un gruppo con Azure Active Directory
È possibile eliminare un gruppo di Azure Active Directory (Azure AD) per diversi motivi, ma i più comuni sono i seguenti:

- **Tipo gruppo** è stato impostato sull'opzione errata.

- È stato creato il gruppo sbagliato oppure è stato duplicato un gruppo per errore. 

- Il gruppo non è più necessario.

## <a name="to-delete-a-group"></a>Per eliminare un gruppo
1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory** e quindi **Gruppi**.

3. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo che si vuole eliminare. In questa procedura verrà usato il gruppo **MDM policy - East**.

    ![Pagina Gruppi - Tutti i gruppi, con il nome del gruppo evidenziato](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Nella pagina **Panoramica MDM policy - East** selezionare **Elimina**.

    Il gruppo viene eliminato dal tenant di Azure Active Directory.

    ![Pagina Panoramica MDM policy - East con l'opzione di eliminazione evidenziata](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Passaggi successivi

- Se si elimina accidentalmente un gruppo, è possibile crearlo nuovamente. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](active-directory-groups-create-azure-portal.md).

- Se si elimina accidentalmente un gruppo di Office 365, è possibile ripristinarlo. Per altre informazioni, vedere [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](../users-groups-roles/groups-restore-deleted.md).
