---
title: Scaricare un elenco di gruppi nel portale di Azure Active Directory . Documenti Microsoft
description: Scaricare le proprietà del gruppo in blocco nell'interfaccia di amministrazione di Azure in Azure Active Directory.Download group properties in bulk in the Azure admin center in Azure Active Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517142"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Scaricare in blocco un elenco di gruppi (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD) è possibile scaricare in blocco l'elenco di tutti i gruppi dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-download-a-list-of-groups"></a>Per scaricare un elenco di gruppi

1. Accedere al portale di [Azure](https://portal.azure.com) con un account amministratore nell'organizzazione.
1. In Azure AD selezionare **Gruppi** > **Di download gruppi**.
1. Nella pagina **di download di Gruppi,** seleziona **Avvia** per ricevere un file CSV che elenca i tuoi gruppi.

   ![Il comando download groups si trova nella pagina Tutti i gruppi](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Controllare lo stato del download

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione in blocco (anteprima).**

   ![La pagina dei risultati delle operazioni in blocco mostra lo stato della richiesta in blocco](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download in blocco

Ogni attività in blocco per scaricare un elenco di gruppi può essere eseguita per un massimo di un'ora. Ciò consente di scaricare un elenco di almeno 300.000 gruppi.

## <a name="next-steps"></a>Passaggi successivi

- [Rimuovere in blocco i membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
