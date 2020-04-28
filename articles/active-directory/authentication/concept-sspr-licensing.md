---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulle differenze Azure Active Directory requisiti di licenza per la reimpostazione della password self-service
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393070"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione della password self-service Azure Active Directory

Per ridurre le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione, è possibile abilitare gli account utente in Azure Active Directory (Azure AD) per la reimpostazione della password self-service (SSPR). Le funzionalità che costituiscono SSPR includono modifiche della password, reimpostazione, sblocco e writeback in una directory locale. Le funzionalità di base di SSPR sono disponibili gratuitamente per Office 365 e per tutti gli utenti Azure AD.

Questo articolo illustra in dettaglio i diversi modi in cui la reimpostazione della password self-service può essere concessa in licenza e usata. Per dettagli specifici su prezzi e fatturazione, vedere la [pagina](https://azure.microsoft.com/pricing/details/active-directory/)relativa ai prezzi di Azure ad.

## <a name="compare-editions-and-features"></a>Confrontare le edizioni e le funzionalità

SSPR è concesso in licenza per utente. Per mantenere la conformità, è necessario che le organizzazioni assegni la licenza appropriata ai propri utenti.

La tabella seguente descrive i diversi scenari di SSPR per la modifica della password, la reimpostazione o il writeback locale e quali SKU forniscono la funzionalità.

| Funzionalità | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| **Modifica della password utente solo cloud**<br />Quando un utente in Azure AD conosce la password e vuole modificarla in un nuovo elemento. | ● | ● | ● | ● |
| **Reimpostazione della password utente solo cloud**<br />Quando un utente in Azure AD ha dimenticato la password ed è necessario reimpostarla. | | ● | ● | ● |
| **Modifica o reimpostazione della password di un utente ibrido con writeback locale**<br />Quando un utente in Azure AD sincronizzato da una directory locale con Azure AD Connect desidera modificare o reimpostare la password e scrivere nuovamente la nuova password in locale. | | | ● | ● |

> [!WARNING]
> I piani di licenza di Office 365 autonomo non supportano SSPR con writeback locale. I piani di licenza di Office 365 richiedono Azure AD Premium P1, Premium P2 o Microsoft 365 Business per il funzionamento di questa funzionalità.

Per ulteriori informazioni sulle licenze, inclusi i costi, vedere le pagine seguenti:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD supporta le licenze basate sui gruppi. Gli amministratori possono assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. Per altre informazioni, vedere [Assegnare, verificare e risolvere i problemi relativi alle licenze](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alcuni servizi Microsoft non sono disponibili in tutte le località. Per poter assegnare una licenza a un utente, l'amministratore deve specificare la proprietà **località di utilizzo** per l'utente. L'assegnazione delle licenze può essere eseguita nella sezione**Impostazioni** **profilo** >  **utente** > del portale di Azure. *Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory.*

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: abilitazione della reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)
