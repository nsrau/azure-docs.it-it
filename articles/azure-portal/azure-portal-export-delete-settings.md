---
title: Esportare o eliminare le impostazioni del portale di AzureExport or delete Azure portal settings
description: Informazioni su come esportare o eliminare le impostazioni utente, i dashboard privati e le impostazioni personalizzate nel portale di Azure.Learn how you can export or delete your user settings, private dashboards, and custom settings in the Azure portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900786"
---
# <a name="export-or-delete-user-settings"></a>Esportare o eliminare le impostazioni utente

È possibile usare le impostazioni e le funzionalità nel portale di Azure per creare un'esperienza personalizzata. Le informazioni sulle impostazioni personalizzate vengono archiviate in Azure.Information about your custom settings is stored in Azure. È possibile esportare o eliminare i seguenti dati utente:

* Dashboard privati nel portale di AzurePrivate dashboards in the Azure portal
* Impostazioni utente come abbonamenti o directory preferiti e ultima directory di accesso
* Temi e altre impostazioni personalizzate del portale

È consigliabile esportare ed esaminare le impostazioni prima di eliminarle. La ricostruzione dei dashboard o la riesecuzione delle impostazioni personalizzate può richiedere molto tempo.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Esportare o eliminare le impostazioni del portale

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Nell'intestazione del portale ![selezionare](media/azure-portal-export-delete-settings/settings-icon.png) l'icona **Impostazioni**impostazioni .

1. Selezionare **Esporta tutte le impostazioni** o **Elimina tutte le impostazioni e i dashboard privati**.

    ![Opzioni relative alle impostazioni e alle impostazioni del portale di AzureAzure portal settings and settings options](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Nella tabella seguente vengono descritte queste azioni.

      | Azione | Descrizione |
      | --- | --- |
      | **Esporta tutte le impostazioni** | Crea un file *.json* che contiene le impostazioni utente come il tema del colore, i preferiti e i dashboard privati.|
      | **Elimina tutte le impostazioni e i dashboard privati** | Elimina tutti i collegamenti ai dashboard privati e altre impostazioni personalizzate apportate al portale. |

> [!NOTE]
> A causa della natura dinamica delle impostazioni utente e del rischio di danneggiamento dei dati, non è possibile importare le impostazioni dal file *.json.*
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard di Azure tramite il Controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md)
* [Aggiungere, rimuovere e ridisporre i preferiti](azure-portal-add-remove-sort-favorites.md)
