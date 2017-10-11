---
title: "Gestire gli aggiornamenti per più macchine virtuali Azure | Microsoft Docs"
description: Caricare macchine virtuali Azure per gestire gli aggiornamenti.
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Gestire gli aggiornamenti per più macchine virtuali Azure

Gestione aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Azure.
Dall'account di [Automazione di Azure](automation-offering-get-started.md) è possibile, in modo rapido, caricare le macchine virtuali, valutare lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente a tutte le macchine virtuali per cui è abilitata la gestione degli aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura in questa guida, è necessario quanto segue:

* Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
* Una macchina virtuale di Azure Resource Manager (non classica). Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Abilitare la gestione degli aggiornamenti per le macchine virtuali Azure

1. Nel Portale di Azure aprire l'account di Automazione.
2. Sul lato sinistro della schermata selezionare **Gestione aggiornamenti**.
3. Nella parte superiore della schermata fare clic su **Aggiungi macchina virtuale di Azure**.
    ![Caricare le macchine virtuali](./media/manage-update-multi/update-onboard-vm.png)
4. Selezionare una macchina virtuale da caricare. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.
5. Fare clic su **Abilita**.

   ![Abilitare la gestione degli aggiornamenti](./media/manage-update-multi/update-enable.png)

La gestione degli aggiornamenti è abilitata per la macchina virtuale.

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

## <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio.
È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per una o più macchine virtuali facendo clic su **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata **Gestione aggiornamenti**. Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: specificare un nome univoco per identificare la distribuzione di aggiornamenti.
* **Tipo di sistema operativo**: selezionare Windows o Linux.
* **Computer da aggiornare**: selezionare le macchine virtuali da aggiornare.

  ![Selezionare le macchine virtuali da aggiornare](./media/manage-update-multi/update-select-computers.png)

* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. I tipi di classificazione sono:
  * Aggiornamenti critici
  * Aggiornamenti della sicurezza
  * Aggiornamenti cumulativi
  * Feature Pack
  * Service Pack
  * Aggiornamenti della definizione
  * Strumenti
  * Aggiornamenti
* **Impostazioni di pianificazione**: è possibile accettare la data e l'ora predefinite, ovvero 30 minuti dopo l'ora corrente o specificare un'ora diversa.
   È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. Per impostare una pianificazione ricorrente, fare clic sull'opzione Ricorrente in Ricorrenza.

   ![Schermata di impostazioni della pianificazione di aggiornamenti](./media/manage-update-multi/update-set-schedule.png)

* **Finestra di manutenzione (minuti)**: specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti.  In questo modo è possibile garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

Dopo avere configurato la pianificazione, fare clic sul pulsante **Crea**. Viene nuovamente visualizzato il dashboard di stato.
Si noti che la tabella **Pianificata** mostra la pianificazione della distribuzione appena creata.

> [!WARNING]
> Per gli aggiornamenti che richiedono un riavvio, la macchina virtuale verrà riavviata automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Se la distribuzione è in corso, viene visualizzato lo stato **In corso**. Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Se si verifica un errore in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.

![Stato della distribuzione degli aggiornamenti ](./media/manage-update-multi/update-view-results.png)

Fare clic sulla distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale.
La tabella di destra visualizza una descrizione dettagliata di ogni aggiornamento e i risultati dell'installazione che possono corrispondere a uno dei valori seguenti:

* Tentativo non eseguito: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* Completato: l'aggiornamento è stato completato
* Non riuscito: l'aggiornamento non è riuscito

Fare clic su **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Fare clic sul riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Fare clic su **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla gestione degli aggiornamenti, vedere [Gestione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md).
