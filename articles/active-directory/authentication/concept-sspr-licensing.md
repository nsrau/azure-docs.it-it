---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulla differenza dei requisiti di licenza per la reimpostazione della password self-service di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393070"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione della password self-service di Azure Active Directory

Per ridurre le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al proprio dispositivo o a un'applicazione, è possibile abilitare gli account utente in Azure Active Directory (Azure AD) per la reimpostazione della password self-service (SSPR). Le funzionalità che costituiscono SSPR includono la modifica della password, la reimpostazione, lo sblocco e il writeback in una directory locale. Le funzionalità SSPR di base sono disponibili per gli utenti di Office 365 e di Azure AD senza alcun costo.

In questo articolo vengono descritti in dettaglio i diversi modi in cui la reimpostazione della password self-service può essere concessa in licenza e utilizzata. Per informazioni dettagliate sui prezzi e la fatturazione, vedere la pagina dei prezzi di [Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="compare-editions-and-features"></a>Confrontare le edizioni e le funzionalità

SSPR è concesso in licenza per utente. Per mantenere la conformità, le organizzazioni devono assegnare la licenza appropriata ai propri utenti.

Nella tabella seguente vengono descritti i diversi scenari SSPR per la modifica della password, la reimpostazione o il writeback locale e gli SKU che forniscono la funzionalità.

| Funzionalità | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| **Modifica della password utente solo cloud**<br />Quando un utente in Azure AD conosce la password e vuole cambiarla in qualcosa di nuovo. | ● | ● | ● | ● |
| **Reimpostazione della password utente solo cloud**<br />Quando un utente in Azure AD ha dimenticato la password e deve reimpostarla. | | ● | ● | ● |
| **Modifica o reimpostazione della password utente ibrida con writeback in alternativa**<br />Quando un utente in Azure AD sincronizzato da una directory locale con Azure AD Connect desidera modificare o reimpostare la password e scrivere la nuova password in locale. | | | ● | ● |

> [!WARNING]
> I piani di licenza autonomi di Office 365 non supportano SSPR con il writeback locale. Questi piani di licenza di Office 365 richiedono Azure AD Premium P1, Premium P2 o Microsoft 365 Business affinché questa funzionalità funzioni.

Per ulteriori informazioni sulle licenze, inclusi i costi, vedere le pagine seguenti:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Mobilità aziendale - Sicurezza](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD supporta le licenze basate su gruppo. Gli amministratori possono assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. Per altre informazioni, vedere [Assegnare, verificare e risolvere i problemi relativi alle licenze](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alcuni servizi Microsoft non sono disponibili in tutte le località. Prima di poter assegnare una licenza a un utente, l'amministratore deve specificare la proprietà **Posizione di utilizzo** dell'utente. L'assegnazione delle licenze può essere eseguita nella sezione Impostazioni**profilo utente** > nel portale di Azure.Assignment of licenses can be done under the **User** > Profile**Settings** section in the Azure portal. *Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory.*

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, completare l'esercitazione seguente:To get started with SSPR, complete the following tutorial:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare la reimpostazione della password self-service (SSPR)Tutorial: Enable self-service password reset (SSPR)](tutorial-enable-sspr.md)
