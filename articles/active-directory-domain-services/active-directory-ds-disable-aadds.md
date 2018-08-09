---
title: Disabilitare Azure Active Directory Domain Services | Microsoft Docs
description: Disabilitare Azure Active Directory Domain Services tramite il portale di Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: af24c7f9b721aab4f1ab810cf42f737fd14bdf88
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505576"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Disabilitare Azure Active Directory Domain Services tramite il portale di Azure
Questo articolo illustra come usare il portale di Azure per disabilitare Azure Active Directory (AD) Domain Services per la directory di Azure AD.

> [!WARNING]
> **L'eliminazione è permanente e non può essere annullata.**
> Procedere con cautela. Quando si elimina il dominio gestito:
  * Viene effettuato il deprovisioning dei controller di dominio per il dominio gestito e vengono rimossi dalla rete virtuale.
  * I dati del dominio gestito vengono eliminati definitivamente, ad esempio le unità organizzative personalizzate, gli oggetti Criteri di gruppo, i record DNS personalizzati, le entità servizio, gli account GMSA e così via.
  * I computer aggiunti al dominio gestito perdono la relazione di trust con il dominio e devono essere separati dal dominio.
  * Non è possibile accedere a questi computer con le credenziali AD aziendali. Usare le credenziali dell'amministratore locale per il computer.
L'eliminazione del dominio gestito non elimina la directory di Azure AD e non influisce in altro modo sulla directory.
>

Per eliminare il dominio gestito di Azure AD Domain Services, seguire questa procedura:
1. Passare all'[estensione Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) nel portale di Azure.
2. Fare clic sul nome del dominio gestito.

    ![Selezionare il dominio da eliminare](./media/getting-started/domain-services-delete-select-domain.png)

3. Nella pagina **Panoramica** fare clic sul pulsante **Elimina**.

    ![Eliminare il dominio](./media/getting-started/domain-services-delete-domain.png)

4. Per confermare l'eliminazione, digitare il nome di dominio DNS del dominio gestito. Al termine, fare clic sul pulsante **Elimina**.

    ![Conferma dell'eliminazione del dominio](./media/getting-started/domain-services-delete-domain-confirm.png)

Il dominio gestito viene eliminato in circa 15-20 minuti.

[Condividendo i loro commenti](active-directory-ds-contact-us.md) gli utenti ci aiuteranno a comprendere quali funzionalità potrebbero far ricadere la loro scelta in futuro su Servizi di dominio Azure AD. Questi commenti e suggerimenti ci consentiranno di migliorare il servizio in modo da adattarlo ad altre esigenze di distribuzione e casi di utilizzo.
