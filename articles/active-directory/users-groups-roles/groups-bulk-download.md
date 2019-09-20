---
title: Scaricare un elenco di gruppi nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco le proprietà del gruppo nell'interfaccia di amministrazione di Azure in Azure Active Directory.
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
ms.openlocfilehash: 76aafa978732b3f4bae82f8ad3c09e67caf297ae
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146294"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Scaricare in blocco un elenco di gruppi (anteprima) in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile eseguire il download bulk dell'elenco di tutti i gruppi dell'organizzazione in un file con valori delimitati da virgole (CSV).

> [!NOTE]
> Azure AD operazioni bulk sono una funzionalità di anteprima pubblica di Azure AD e sono disponibili con qualsiasi piano di licenza Azure AD a pagamento. Per ulteriori informazioni sui termini di utilizzo dell'anteprima, vedere [condizioni supplementari per l'utilizzo di Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-download-a-list-of-groups"></a>Per scaricare un elenco di gruppi

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure ad selezionare **gruppi** > gruppi di**download**.
1. Nella pagina di **download dei gruppi** selezionare **Avvia** per ricevere un file CSV che elenca i gruppi.

   ![Il comando Scarica gruppi si trova nella pagina tutti i gruppi](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifica lo stato di download

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![La pagina Risultati operazioni bulk Mostra lo stato della richiesta bulk](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per il download di un elenco di gruppi può essere eseguita per un massimo di un'ora. In questo modo è possibile scaricare un elenco di almeno 300.000 gruppi.

## <a name="next-steps"></a>Passaggi successivi

- [Rimuovere in blocco i membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
