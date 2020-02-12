---
title: Ripristinare un dashboard eliminato nel portale di Azure | Microsoft Docs
description: Se si elimina un dashboard pubblicato nella portale di Azure, è possibile ripristinare il dashboard.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133281"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Ripristinare un dashboard eliminato nel portale di Azure

Se ci si trova nel cloud pubblico di Azure e si elimina un dashboard _pubblicato_ nella portale di Azure, è possibile ripristinare il dashboard entro 14 giorni dall'eliminazione. Se ci si trova in un cloud di Azure per enti pubblici o se il dashboard non è pubblicato, non è possibile ripristinarlo ed è necessario ricompilarlo. Per altre informazioni sulla pubblicazione di un dashboard, vedere [pubblicare il dashboard](azure-portal-dashboard-share-access.md#publish-dashboard). Per ripristinare un dashboard pubblicato, seguire questa procedura:

1. Dal menu portale di Azure selezionare gruppi di **risorse**, quindi selezionare il gruppo di risorse in cui è stato pubblicato il dashboard (per impostazione predefinita, i dashboard sono denominati **Dashboard**).

1. In **log attività**espandere l'operazione **Elimina dashboard** . Selezionare la scheda **cronologia modifiche** , quindi selezionare **\<\>risorsa eliminata** .

    ![Screenshot della scheda cronologia modifiche](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selezionare e copiare il contenuto del riquadro a sinistra, quindi salvare un file di testo con estensione _JSON_ . Il portale usa il file JSON per ricreare il dashboard.

    ![Screenshot della differenza della cronologia delle modifiche](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Dal menu portale di Azure selezionare **Dashboard**, quindi selezionare **carica**.

    ![Screenshot del caricamento del dashboard](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selezionare il file JSON salvato. Il portale ricrea il dashboard con lo stesso nome e gli stessi elementi del dashboard eliminato.

1. Selezionare **share (Condividi** ) per pubblicare il dashboard e ristabilire il controllo di accesso appropriato.

    ![Screenshot della condivisione del dashboard](media/recover-shared-deleted-dashboard/dashboard-share.png)
