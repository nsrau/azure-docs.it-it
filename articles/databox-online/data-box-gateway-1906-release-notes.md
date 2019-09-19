---
title: Note sulla versione di Azure Data Box Gateway & Azure Data Box Edge 1906 | Microsoft Docs
description: Vengono descritti i problemi critici aperti e le soluzioni per il Azure Data Box Gateway e Azure Data Box Edge la versione 1906 in esecuzione.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099486"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Note sulla versione di Azure Data Box Edge e Azure Data Box Gateway 1906

Le seguenti note sulla versione identificano i problemi critici aperti e i problemi risolti per la versione 1906 per Azure Data Box Edge e Azure Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il Data Box Edge/Data Box Gateway, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questa versione corrisponde alle versioni del software:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> L'aggiornamento 1906 può essere applicato solo ai dispositivi Data Box Edge che eseguono la versione di disponibilità generale (GA) o 1905 del software.

## <a name="whats-new"></a>Novità

- **Correzione di bug nel flusso di lavoro di gestione delle chiavi di ripristino** : nella versione precedente si è verificato un bug a causa del quale la chiave di ripristino non è stata applicata. Tale bug è stato risolto in questa versione. Si consiglia vivamente di applicare questo aggiornamento poiché la chiave di ripristino consente di ripristinare i dati nel dispositivo, nel caso in cui il dispositivo non si avvii. Per ulteriori informazioni, vedere come [salvare la chiave di ripristino durante la distribuzione di data box Edge o data box gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Miglioramenti della registrazione di Field Programmable Gate Array (FPGA)** : è stata avviata la versione 1905, i miglioramenti della registrazione e degli avvisi correlati a FPGA. Questo continua a essere un aggiornamento necessario per Data Box Edge se si usa la funzionalità di calcolo Edge con l'FPGA. Per altre informazioni, vedere come [trasformare i dati con il calcolo Edge nel data box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione GA

Non sono stati rilasciati nuovi problemi per questa versione. Tutti i problemi rilasciati sono stati rilasciati dalle versioni precedenti. Per visualizzare un elenco di problemi noti, vedere [problemi noti nella versione GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Preparare la distribuzione di Azure Data Box Edge](data-box-edge-deploy-prep.md)
