---
title: Scaricare un elenco di gruppi nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco le proprietà del gruppo nell'interfaccia di amministrazione di Azure in Azure Active Directory.
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
ms.openlocfilehash: 00fdd94e8a8cd4b8769260cd595dfee5ff898039
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732619"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Scaricare in blocco un elenco di gruppi in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile eseguire il download bulk dell'elenco di tutti i gruppi dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-download-a-list-of-groups"></a>Per scaricare un elenco di gruppi

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure ad selezionare **gruppi**gruppi di  >  **download**.
1. Nella pagina di **download dei gruppi** selezionare **Avvia** per ricevere un file CSV che elenca i gruppi.

   ![Il comando Scarica gruppi si trova nella pagina tutti i gruppi](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifica lo stato di download

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per il download di un elenco di gruppi può essere eseguita per un massimo di un'ora. In questo modo è possibile scaricare un elenco di almeno 300.000 gruppi.

## <a name="next-steps"></a>Passaggi successivi

- [Rimozione in blocco di membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
