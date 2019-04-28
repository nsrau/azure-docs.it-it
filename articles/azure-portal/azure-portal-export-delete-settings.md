---
title: Esportare o eliminare le impostazioni del portale di Azure | Microsoft Docs
description: Informazioni su come è possibile esportare o eliminare le impostazioni utente, i dashboard privati e le impostazioni personalizzate nel portale di Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551668"
---
# <a name="export-or-delete-user-settings"></a>Esportare o eliminare le impostazioni utente

È possibile utilizzare le impostazioni e funzionalità nel portale di Azure per creare un'esperienza personalizzata. Informazioni sulle impostazioni personalizzate vengono archiviate in Azure. È possibile esportare o eliminare i dati utente seguenti:

* Dashboard privati nel portale di Azure
* Impostazioni utente come preferite sottoscrizioni o directory e ultima directory ha eseguito l'accesso
* I temi e altre impostazioni del portale personalizzati

È una buona idea per esportare e controllare le impostazioni prima dell'eliminazione. La ricompilazione di dashboard o Ripristina le impostazioni personalizzate può richiedere molto tempo.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Esportare o eliminare le impostazioni del portale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'intestazione del portale, selezionare **impostazioni**.

    ![Screenshot che mostra l'ingranaggio per le impostazioni del portale](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Selezionare **Esporta tutte le impostazioni** o **Elimina tutte le impostazioni e i dashboard privati**.

    ![Screenshot che mostra il portale di esportazione ed eliminazione delle impostazioni](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      Nella tabella seguente vengono descritte tali azioni.

      | Azione | DESCRIZIONE |
      | --- | --- |
      | **Esporta tutte le impostazioni** | Crea un file con estensione JSON che contiene le impostazioni dell'utente, ad esempio il tema colori, report Preferiti e i dashboard privati.|
      | **Elimina tutte le impostazioni e i dashboard privati** | Elimina tutti i collegamenti ai dashboard privato e altre impostazioni personalizzate che sono state apportate al portale. |

> [!NOTE]
> A causa della natura dinamica delle impostazioni utente e rischio di danneggiamento dei dati, è possibile importare le impostazioni dal file con estensione JSON.
>
>


## <a name="next-steps"></a>Passaggi successivi

* [Creare e condividere dashboard di Azure](azure-portal-dashboard-share-access.md)
* [Aggiungere, rimuovere e ordinare i Preferiti](azure-portal-add-remove-sort-favorites.md)
