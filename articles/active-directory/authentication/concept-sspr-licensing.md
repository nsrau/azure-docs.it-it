---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulle differenze Azure Active Directory requisiti di licenza per la reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd17e446766514fa90f8beaaab104bbbd2f389f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965030"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione della password self-service Azure Active Directory

Per ridurre le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione, è possibile abilitare gli account utente in Azure Active Directory (Azure AD) per la reimpostazione della password self-service (SSPR). Le funzionalità che costituiscono SSPR includono modifiche della password, reimpostazione, sblocco e writeback in una directory locale. Le funzionalità di base di SSPR sono disponibili in Microsoft 365 Business standard o versione successiva e tutti gli SKU di Azure AD Premium senza costi aggiuntivi.

Questo articolo illustra in dettaglio i diversi modi in cui la reimpostazione della password self-service può essere concessa in licenza e usata. Per dettagli specifici su prezzi e fatturazione, vedere la [pagina](https://azure.microsoft.com/pricing/details/active-directory/)relativa ai prezzi di Azure ad.

## <a name="compare-editions-and-features"></a>Confrontare le edizioni e le funzionalità

SSPR è concesso in licenza per utente. Per mantenere la conformità, è necessario che le organizzazioni assegni la licenza appropriata ai propri utenti.

La tabella seguente descrive i diversi scenari di SSPR per la modifica della password, la reimpostazione o il writeback locale e quali SKU forniscono la funzionalità.

| Funzionalità | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| **Modifica della password utente solo cloud**<br />Quando un utente in Azure AD conosce la password e vuole modificarla in un nuovo elemento. | ● | ● | ● | ● |
| **Reimpostazione della password utente solo cloud**<br />Quando un utente in Azure AD ha dimenticato la password ed è necessario reimpostarla. | | ● | ● | ● |
| **Modifica o reimpostazione della password di un utente ibrido con writeback locale**<br />Quando un utente in Azure AD sincronizzato da una directory locale con Azure AD Connect desidera modificare o reimpostare la password e scrivere nuovamente la nuova password in locale. | | | ● | ● |

> [!WARNING]
> I piani di licenza autonomi Microsoft 365 Basic e standard non supportano SSPR con writeback locale. La funzionalità di writeback locale richiede Azure AD Premium P1, Premium P2 o Microsoft 365 Business Premium.

Per ulteriori informazioni sulle licenze, inclusi i costi, vedere le pagine seguenti:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD supporta le licenze basate sui gruppi. Gli amministratori possono assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. Per altre informazioni, vedere [Assegnare, verificare e risolvere i problemi relativi alle licenze](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alcuni servizi Microsoft non sono disponibili in tutte le località. Per poter assegnare una licenza a un utente, l'amministratore deve specificare la proprietà **località di utilizzo** per l'utente. L'assegnazione delle licenze può essere eseguita **User**  >  **Profile**  >  nella sezione**Impostazioni** profilo utente del portale di Azure. *Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory.*

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: abilitazione della reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)