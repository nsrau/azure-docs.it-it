---
title: Tenere traccia delle modifiche nelle macchine virtuali di Azure | Microsoft Docs
description: Usare il rilevamento modifiche per tenere traccia delle modifiche nei file e nel Registro di sistema nelle macchine virtuali.
services: automation
documentationcenter: automation
author: georgewallace
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
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: 3a661fada2e768c2206183c125593d019b557c1d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Tenere traccia delle modifiche nelle macchine virtuali di Azure

Abilitando il rilevamento delle modifiche, è possibile tenere traccia delle modifiche ai file e alle chiavi del Registro di sistema nelle macchine virtuali. Rilevando le modifiche alla configurazione è possibile localizzare eventuali problemi operativi.

È possibile abilitare il rilevamento delle modifiche direttamente dalla macchina virtuale di Azure.

Se non si dispone di una macchina virtuale di Azure, è possibile crearne una seguendo le istruzioni nell'articolo sull'[avvio rapido di Windows](../virtual-machines/windows/quick-create-portal.md) o sull'[avvio rapido di Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Abilitare il rilevamento delle modifiche per una macchina virtuale di Azure

1. Nel riquadro sinistro del portale di Azure selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale nell'elenco.
3. In **Operazioni** nella finestra della macchina virtuale selezionare **Rilevamento modifiche**. 

   ![Macchina virtuale con caricamento di Rilevamento modifiche](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    Viene visualizzata la finestra **Abilita Gestione aggiornamenti**.

    Viene eseguita una convalida per determinare se il rilevamento delle modifiche è abilitato per la macchina virtuale. Se il rilevamento delle modifiche non è abilitato, viene visualizzato un banner che offre la possibilità di abilitare la soluzione.

   ![Banner di configurazione di caricamento di Rilevamento modifiche](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Selezionare il banner per abilitare la soluzione. Se non sono già disponibili, gli elementi seguenti verranno aggiunti automaticamente:

   * Area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md)
   * Account di [automazione](../automation/automation-offering-get-started.md)

5. Selezionare un'area di lavoro di Log Analytics per archiviare i log dei dati dal rilevamento delle modifiche e selezionare un account di Automazione per tenere traccia delle modifiche e quindi selezionare **Abilita**.  
    Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti.

## <a name="configure-change-tracking"></a>Configurare il rilevamento delle modifiche

Dopo che il rilevamento delle modifiche è stato abilitato, viene visualizzata la finestra **Rilevamento modifiche**. 

Per scegliere i file e le chiavi del Registro di sistema di cui tenere traccia, selezionare **Modifica impostazioni**.

   ![Rilevamento modifiche - Modifica impostazioni](./media/automation-vm-change-tracking/change-edit-settings.png)

   Verrà visualizzata la finestra **Configurazione dell'area di lavoro**. 

Nella finestra **Configurazione dell'area di lavoro** aggiungere le chiavi del Registro di sistema di Windows, i file di Windows o Linux da rilevare, come descritto nelle tre sezioni successive.

### <a name="add-a-windows-registry-key"></a>Aggiungere una chiave del Registro di sistema di Windows

1. Nella scheda **Registro di sistema di Windows** selezionare **Aggiungi**.  
    Verrà visualizzata la finestra **Aggiungi Registro di sistema di Windows per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi Registro di sistema](./media/automation-vm-change-tracking/change-add-registry.png)

2. In **Abilitato** selezionare **Vero**.
3. Immettere un nome descrittivo nella casella **Nome elemento**.
4. Nella casella **Gruppo** immettere il nome di un gruppo (facoltativo).
5. Nella casella **Chiave del Registro di sistema di Windows** immettere il nome della chiave del Registro di sistema da rilevare.
6. Selezionare **Salva**.

### <a name="add-a-windows-file"></a>Aggiungere un file di Windows

1. Nella scheda **File Windows** selezionare **Aggiungi**.  
    Verrà visualizzata la finestra **Aggiungi file Windows per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi file Windows](./media/automation-vm-change-tracking/change-add-win-file.png)

2. In **Abilitato** selezionare **Vero**.
3. Immettere un nome descrittivo nella casella **Nome elemento**.
4. Nella casella **Gruppo** immettere il nome di un gruppo (facoltativo).
5. Nella casella **Immettere il percorso** immettere il percorso completo e il nome del file da rilevare.
6. Selezionare **Salva**.

### <a name="add-a-linux-file"></a>Aggiungere un file di Linux

1. Nella scheda **File Linux** selezionare **Aggiungi**.  
    Verrà visualizzata la finestra **Aggiungi file Linux per Rilevamento modifiche**.

   ![Rilevamento modifiche - Aggiungi file Linux](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. In **Abilitato** selezionare **Vero**.
3. Immettere un nome descrittivo nella casella **Nome elemento**.
4. Nella casella **Gruppo** immettere il nome di un gruppo (facoltativo).
5. Nella casella **Immettere il percorso** immettere il percorso completo e il nome del file da rilevare.
6. Nella casella **Tipo di percorso** selezionare **File** o **Directory**.
7. In **Ricorsione** selezionare **Sì** per tenere traccia delle modifiche per il percorso specificato e tutti i file e i percorsi in esso contenuti. Per tenere traccia solo del percorso o del file selezionato, selezionare **No**.
8. In **Usa Sudo** selezionare `sudo`Sì **per tenere traccia dei file che richiedono il comando**  per l'accesso. In caso contrario, selezionare **No**.
9. Selezionare **Salva**.

## <a name="view-changes"></a>Visualizzare le modifiche

Nella finestra **Rilevamento modifiche** è possibile visualizzare le modifiche in ognuna delle diverse categorie per la macchina virtuale nel corso del tempo.

   ![Rilevamento modifiche - Visualizzazione delle modifiche](./media/automation-vm-change-tracking/change-view-changes.png)

È possibile selezionare le categorie e l'intervallo di tempo delle modifiche da visualizzare. Nella parte superiore della finestra è possibile visualizzare una rappresentazione grafica delle modifiche nel tempo. Nella parte inferiore della finestra è possibile visualizzare un elenco delle modifiche recenti.

## <a name="view-change-tracking-log-data"></a>Visualizzare i dati dei log di Rilevamento modifiche

Rilevamento modifiche genera dati di log che vengono inviati a Log Analytics. Per eseguire ricerche nei log tramite l'esecuzione di query, selezionare **Log Analytics** nella parte superiore della finestra **Rilevamento modifiche**.

   ![Rilevamento modifiche - Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

Per altre informazioni sull'esecuzione e sulla ricerca nei file di log in Log Analytics, vedere [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul rilevamento delle modifiche, vedere [Rilevare le modifiche software nell'ambiente in uso con la soluzione di rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per altre informazioni sulla gestione degli aggiornamenti per le macchine virtuali, vedere [Soluzione Gestione aggiornamenti in OMS](../operations-management-suite/oms-solution-update-management.md).
