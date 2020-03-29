---
title: Collegamento alla condivisione per richiedere un pacchetto di accesso nella gestione dei diritti di Azure AD - Azure Active DirectoryShare link to request an access package in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come condividere il collegamento per richiedere un pacchetto di accesso nella gestione dei diritti di Azure Active Directory.Learn how to share link to request an access package in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968756"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Condividere il collegamento per richiedere un pacchetto di accesso nella gestione dei diritti di Azure ADShare link to request an access package in Azure AD entitlement management

La maggior parte degli utenti nella directory può accedere al portale My Access e visualizzare automaticamente un elenco di pacchetti di accesso che possono richiedere. Tuttavia, per gli utenti di Business Partner esterni che non sono ancora presenti nella directory, è necessario inviare loro un collegamento che possono utilizzare per richiedere un pacchetto di accesso. 

Finché il catalogo per il pacchetto di accesso è [abilitato per gli utenti esterni](entitlement-management-catalog-create.md) e si dispone di un criterio per la directory [dell'utente esterno,](entitlement-management-access-package-request-policy.md)l'utente esterno può utilizzare il collegamento del portale My Access per richiedere il pacchetto di accesso.

## <a name="share-link-to-request-an-access-package"></a>Condividi link per richiedere un pacchetto di accesso

**Ruolo prerequisito:** Amministratore globale, Amministratore utente, Proprietario catalogo o Gestore pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Pacchetti di accesso** e quindi apri il pacchetto di accesso.

1. Nella pagina Panoramica copiare il **collegamento Al portale Accesso personale**.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

    È importante copiare l'intero collegamento al portale My Access quando lo si invia a un Business Partner interno. Ciò garantisce che il partner otterrà l'accesso al portale della directory per effettuare la richiesta. Il collegamento `myaccess`inizia con , include un hint di directory e termina con un ID pacchetto di accesso.  (Per il governo degli Stati Uniti, il `myaccess.microsoft.us`dominio nel collegamento al portale My Access sarà .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Inviare il collegamento tramite posta elettronica o inviare il collegamento al Business Partner esterno. Possono condividere il link con i propri utenti per richiedere il pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accessoRequest access to an access package](entitlement-management-request-access.md)
- [Approvare o rifiutare le richieste di accesso](entitlement-management-request-approve.md)