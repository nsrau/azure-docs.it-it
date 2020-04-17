---
title: Elenco di appartenenza al gruppo di download in blocco - Portale di Azure Active Directory Documenti Microsoft
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure.Add users in bulk in the Azure admin center.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533683"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Scaricare in blocco i membri di un gruppo in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile scaricare in blocco i membri di un gruppo dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-bulk-download-group-membership"></a>Per scaricare in blocco l'appartenenza al gruppo

1. Accedere al portale di [Azure](https://portal.azure.com) con un account amministratore utente nell'organizzazione. I proprietari dei gruppi possono anche scaricare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **tutti i gruppi**.
1. Aprire il gruppo di cui si desidera scaricare l'appartenenza e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Scarica membri** per scaricare un file CSV in cui sono elencati i membri del gruppo.

   ![Il comando Scarica membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Controllare lo stato del download

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download in blocco

Ogni attività in blocco per scaricare un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Ciò consente di scaricare un elenco di almeno 500.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Membri del gruppo di importazione in blocco](groups-bulk-import-members.md)
- [Rimuovere in blocco i membri del gruppo](groups-bulk-download-members.md)
