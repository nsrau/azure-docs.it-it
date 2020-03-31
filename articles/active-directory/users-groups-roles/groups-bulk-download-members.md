---
title: Elenco di appartenenza al gruppo di download in blocco - Portale di Azure Active Directory Documenti Microsoft
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure.Add users in bulk in the Azure admin center.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517165"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Scaricare in blocco i membri di un gruppo (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile scaricare in blocco i membri di un gruppo dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-bulk-download-group-membership"></a>Per scaricare in blocco l'appartenenza al gruppo

1. Accedere al portale di [Azure](https://portal.azure.com) con un account amministratore utente nell'organizzazione. I proprietari dei gruppi possono anche scaricare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **tutti i gruppi**.
1. Aprire il gruppo di cui si desidera scaricare l'appartenenza e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Scarica membri** per scaricare un file CSV in cui sono elencati i membri del gruppo.

   ![Il comando Scarica membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Controllare lo stato del download

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione in blocco (anteprima).**

   ![La pagina dei risultati delle operazioni in blocco mostra lo stato della richiesta in blocco](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download in blocco

Ogni attività in blocco per scaricare un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Ciò consente di scaricare un elenco di almeno 500.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Membri del gruppo di importazione in blocco](groups-bulk-import-members.md)
- [Rimuovere in blocco i membri del gruppo](groups-bulk-download-members.md)
