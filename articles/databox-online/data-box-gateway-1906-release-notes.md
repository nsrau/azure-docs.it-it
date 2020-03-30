---
title: Note sulla versione di Azure Data Box Gateway & Azure Data Box Edge 1906. Documenti Microsoft
description: Descrive i problemi e le soluzioni di apertura critici per il Gateway casella dati di Azure e Azure Data Box Edge con la versione 1906.Describes critical open issues and resolutions for the Azure Data Box Gateway and Azure Data Box Edge running 1906 release.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099486"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Note sulla versione di Azure Data Box Edge e Azure Data Box Gateway 1906Azure Data Box Edge and Azure Data Box Gateway 1906 release notes

The following release notes identify the critical open issues and the resolved issues for the 1906 release for Azure Data Box Edge and Azure Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il gateway Data Box Edge/Data Box, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questa versione corrisponde alle versioni del software:

- **Gateway data Box 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> L'aggiornamento 1906 può essere applicato solo ai dispositivi Data Box Edge che eseguono la disponibilità generale (GA) o la versione 1905 del software.

## <a name="whats-new"></a>Novità

- **Correzione di bug nel flusso** di lavoro di gestione delle chiavi di ripristino: nella versione precedente si è verificato un bug a causa del quale la chiave di ripristino non veniva applicata. Tale bug è stato risolto in questa versione. Si consiglia di applicare questo aggiornamento in quanto la chiave di ripristino consente di ripristinare i dati sul dispositivo, nel caso in cui il dispositivo non si avvia. Per ulteriori informazioni, vedere come salvare la chiave di ripristino quando si [distribuisce Data Box Edge o Data Box Gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- Miglioramenti alla **registrazione di Field Programmable Gate Array (FPGA):** a partire dalla versione 1905 sono stati apportati miglioramenti alla versione, alla registrazione e agli avvisi relativi a FPGA. Questo continua ad essere un aggiornamento obbligatorio per Data Box Edge se si utilizza la funzionalità di calcolo Edge con FPGA. Per ulteriori informazioni, vedere come trasformare i [dati con Edge compute in Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione GA

Non sono stati rilasciati nuovi problemi per questa versione. Tutti i problemi di rilascio indicati sono stati riportati dalle versioni precedenti. Per visualizzare un elenco dei problemi noti, vedere [Problemi noti nella versione GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Preparare la distribuzione di Azure Data Box Edge](data-box-edge-deploy-prep.md)
