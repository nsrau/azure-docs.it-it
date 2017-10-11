---
title: Tenere traccia delle modifiche nelle macchine virtuali di Azure | Microsoft Docs
description: Usare Rilevamento modifiche per tenere traccia delle modifiche nei file e nel Registro di sistema nelle macchine virtuali
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Tenere traccia delle modifiche nelle macchine virtuali di Azure

Abilitando il rilevamento delle modifiche, è possibile tenere traccia delle modifiche ai file e alle chiavi del Registro di sistema nelle macchine virtuali. . Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

È possibile abilitare il rilevamento delle modifiche direttamente dalla macchina virtuale di Azure.

Se non si dispone di una macchina virtuale di Azure, è possibile crearne una usando la [Guida introduttiva di Windows](../virtual-machines/windows/quick-create-portal.md) o la [Guida introduttiva di Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Abilitare il rilevamento delle modifiche per una macchina virtuale di Azure

1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
1. Selezionare una macchina virtuale dall'elenco.
1. Nella sezione **Operazioni** della schermata della macchina virtuale fare clic su **Rilevamento modifiche**. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.

   ![Macchina virtuale con caricamento di Rilevamento modifiche](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

Viene eseguita una convalida per determinare se il rilevamento delle modifiche è abilitato per la macchina virtuale. Se il rilevamento delle modifiche non è abilitato, viene visualizzato un banner che offre la possibilità di abilitare la soluzione.

   ![Banner di configurazione di caricamento di Rilevamento modifiche](./media/automation-vm-change-tracking/change-onboard-banner.png)

Fare clic sul banner per abilitare la soluzione. Se non si dispone degli elementi seguenti, verranno aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md)
* Account di [automazione](../automation/automation-offering-get-started.md)

Selezionare un'area di lavoro di Log Analytics per archiviare i log dei dati per il rilevamento delle modifiche e un account di Automazione per tenere traccia delle modifiche e quindi fare clic su **Abilita**.

Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti.

## <a name="configure-change-tracking"></a>Configurare il rilevamento delle modifiche

Dopo che il rilevamento delle modifiche è stato abilitato, viene visualizzata la schermata **Rilevamento modifiche**. Scegliere i file e le chiavi del Registro di sistema di cui tenere traccia facendo clic su **Modifica impostazioni**.

![Rilevamento modifiche - Modifica impostazioni](./media/automation-vm-change-tracking/change-edit-settings.png)

Verrà visualizzata la schermata **Configurazione dell'area di lavoro**. Fare clic su **Aggiungi** nella scheda appropriata per aggiungere le chiavi del Registro di sistema di Windows, i file di Windows o i file di Linux da rilevare.

## <a name="add-a-windows-registry-key"></a>Aggiungere una chiave del Registro di sistema di Windows

1. Nella scheda **Registro di sistema di Windows** della schermata **Configurazione dell'area di lavoro** fare clic su **Aggiungi**. Verrà visualizzata la schermata **Aggiungi Registro di sistema di Windows per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi Registro di sistema](./media/automation-vm-change-tracking/change-add-registry.png)

1. In **Abilitato** selezionare **Vero**.
1. Aggiungere un nome descrittivo nel campo **Nome elemento**.
1. Nel campo **Gruppo** immettere il nome di un gruppo (facoltativo).
1. Nel campo **Chiave del Registro di sistema di Windows** aggiungere il nome della chiave del Registro di sistema da rilevare.
1. Fare clic su **Salva**.

## <a name="add-a-windows-file"></a>Aggiungere un file di Windows

1. Nella scheda **File Windows** della schermata **Configurazione dell'area di lavoro** fare clic su **Aggiungi**. Verrà visualizzata la schermata **Aggiungi file Windows per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi file Windows](./media/automation-vm-change-tracking/change-add-win-file.png)

1. In **Abilitato** selezionare **Vero**.
1. Aggiungere un nome descrittivo nel campo **Nome elemento**.
1. Nel campo **Gruppo** immettere il nome di un gruppo (facoltativo).
1. Nel campo **Immettere il percorso** aggiungere il percorso completo e il nome del file da rilevare.
1. Fare clic su **Salva**.

## <a name="add-a-linux-file"></a>Aggiungere un file di Linux

1. Nella scheda **File Linux** della schermata **Configurazione dell'area di lavoro** fare clic su **Aggiungi**. Verrà visualizzata la schermata **Aggiungi file Linux per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi file Linux](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. In **Abilitato** selezionare **Vero**.
1. Aggiungere un nome descrittivo nel campo **Nome elemento**.
1. Nel campo **Gruppo** immettere il nome di un gruppo (facoltativo).
1. Nel campo **Immettere il percorso** aggiungere il percorso completo e il nome del file da rilevare.
1. Nel campo **Tipo di percorso** selezionare **File** o **Directory**.
1. In **Ricorsione** selezionare **Sì** per tenere traccia delle modifiche per il percorso specificato e tutti i file e i percorsi in esso contenuti. Per tenere traccia solo del percorso o del file selezionato, selezionare **No**.
1. In **Usa Sudo**selezionare **Sì** per tenere traccia dei file che richiedono il comando `sudo` per l'accesso. In caso contrario, selezionare **No**.
1. Fare clic su **Salva**.

## <a name="view-changes"></a>Visualizzare le modifiche

Nella schermata **Rilevamento modifiche** è possibile visualizzare le modifiche in ognuna delle diverse categorie per la macchina virtuale nel corso del tempo.

   ![Rilevamento modifiche - Visualizzazione delle modifiche](./media/automation-vm-change-tracking/change-view-changes.png)

È possibile selezionare le categorie e l'intervallo di tempo delle modifiche da visualizzare. Nella parte superiore della schermata è possibile visualizzare una rappresentazione grafica delle modifiche nel corso del tempo.
Nella parte inferiore della schermata è disponibile un elenco delle modifiche recenti.

## <a name="view-change-tracking-log-data"></a>Visualizzare i dati dei log di Rilevamento modifiche

Rilevamento modifiche genera dati di log che vengono inviati a Log Analytics. Per eseguire ricerche nei log tramite l'esecuzione di query, fare clic su **Log Analytics** nella parte superiore della schermata **Rilevamento modifiche**.

   ![Rilevamento modifiche - Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

Per altre informazioni sulle ricerche nei file di log in Log Analytics, vedere [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul rilevamento delle modifiche, vedere [Rilevamento delle modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per altre informazioni sulla gestione degli aggiornamenti per le macchine virtuali, vedere [Gestione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md).

