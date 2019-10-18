---
title: Elenco di appartenenza al gruppo per il download bulk-portale Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure.
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
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517165"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Scaricare in blocco i membri di un gruppo (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile eseguire il download bulk dei membri di un gruppo dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-bulk-download-group-membership"></a>Per scaricare in blocco l'appartenenza al gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione. I proprietari del gruppo possono anche scaricare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **gruppi**  > **tutti i gruppi**.
1. Aprire il gruppo di cui si desidera scaricare l'appartenenza, quindi selezionare **membri**.
1. Nella pagina **membri** selezionare **Scarica membri** per scaricare un file CSV che elenca i membri del gruppo.

   ![Il comando Scarica membri si trova nella pagina del profilo del gruppo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verifica lo stato di download

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![La pagina Risultati operazioni bulk Mostra lo stato della richiesta bulk](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per il download di un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. In questo modo è possibile scaricare un elenco di almeno 500.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Importazione bulk dei membri del gruppo](groups-bulk-import-members.md)
- [Rimuovere in blocco i membri del gruppo](groups-bulk-download-members.md)
