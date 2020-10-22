---
title: Elenco di appartenenza al gruppo per il download bulk-portale Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec712d2f58c3028aa11d0b12132669648351bfb1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375596"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Scaricare in blocco i membri di un gruppo in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile eseguire il download bulk dei membri di un gruppo dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-bulk-download-group-membership"></a>Per scaricare in blocco l'appartenenza al gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore utenti nell'organizzazione. I proprietari del gruppo possono anche scaricare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **Tutti i gruppi**.
1. Aprire il gruppo di cui si desidera scaricare l'appartenenza, quindi selezionare **membri**.
1. Nella pagina **membri** selezionare **Scarica membri** per scaricare un file CSV che elenca i membri del gruppo.

   ![Il comando Scarica membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verifica lo stato di download

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

[![Verificare lo stato nella pagina Risultati operazioni bulk.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per il download di un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. In questo modo è possibile scaricare un elenco di almeno 500.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Importare in blocco i membri del gruppo](groups-bulk-import-members.md)
- [Rimozione in blocco di membri del gruppo](groups-bulk-download-members.md)
