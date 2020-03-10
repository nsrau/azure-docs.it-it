---
title: 'Esercitazione: Creare e gestire dati esportati da Gestione costi di Azure'
description: Questo articolo descrive come creare e gestire dati esportati di Gestione costi di Azure per poterli usare in sistemi esterni.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 04228a5f3f7e0b10e3cd881043af7861bb9fb347
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251128"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Esercitazione: Creare e gestire dati esportati

Se è già stata seguita l'esercitazione sull'analisi dei costi, si ha familiarità con il download manuale di dati di Gestione costi. Tuttavia, è possibile creare un'attività periodica che esporta automaticamente i dati di Gestione costi nell'archiviazione di Azure su base giornaliera, settimanale o mensile. I dati esportati sono in formato CSV e contengono tutte le informazioni raccolte da Gestione costi. È quindi possibile usare i dati esportati nell'archiviazione di Azure con sistemi esterni e combinarli con dati personalizzati. È possibile usare i dati esportati anche in un sistema esterno come un dashboard o un altro sistema finanziario.

Guarda il video [Come pianificare esportazioni in archiviazione con Gestione costi di Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sulla creazione di un'esportazione pianificata dei dati di costo di Azure in Archiviazione di Azure.

Gli esempi contenuti in questa esercitazione descrivono in modo dettagliato come esportare dati di gestione dei costi e come verificare che i dati siano stati esportati correttamente.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'esportazione giornaliera
> * Verificare che i dati vengano raccolti

## <a name="prerequisites"></a>Prerequisiti
L'esportazione dati è disponibile per vari tipi di account di Azure, inclusi i clienti con [Contratto Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Contratto del cliente Microsoft](get-started-partners.md). Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](understand-cost-mgt-data.md). Sono supportati gli ambiti o le autorizzazioni di Azure seguenti per ogni sottoscrizione per l'esportazione di dati per utente e gruppo. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Proprietario: può creare, modificare o eliminare esportazioni pianificate per una sottoscrizione.
- Collaboratore: può creare, modificare o eliminare le proprie esportazioni pianificate. Può modificare il nome delle esportazioni pianificate create da altri.
- Lettore: può pianificare le esportazioni per cui ha le autorizzazioni.

Per gli account di archiviazione di Azure:
- Sono necessarie autorizzazioni in scrittura per modificare l'account di archiviazione configurato, indipendentemente dalle autorizzazioni per l'esportazione.
- L'account di archiviazione di Azure deve essere configurato per l'archiviazione BLOB o file.

Se si ha una nuova sottoscrizione, non è possibile usare immediatamente le funzionalità di Gestione costi. Potrebbero essere necessarie fino a 48 ore prima di poter usarle usare tutte.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Creare un'esportazione giornaliera

Per creare o visualizzare un'esportazione di dati o pianificare un'esportazione, aprire l'ambito desiderato nel portale di Azure e selezionare **Analisi dei costi** nel menu. Passare ad esempio a **Sottoscrizioni**, selezionare una sottoscrizione dall'elenco, quindi selezionare **Analisi dei costi** nel menu. Nella parte superiore della pagina Analisi dei costi selezionare **Esporta** e quindi scegliere un'opzione di esportazione. Ad esempio, selezionare **Pianifica l'esportazione**.  

> [!NOTE]
> - Oltre alle sottoscrizioni, è possibile creare esportazioni di gruppi di risorse, account, reparti e iscrizioni. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).
>- Dopo aver eseguito l'accesso come partner a livello dell'ambito dell'account di fatturazione o nel tenant di un cliente, è possibile esportare i dati in un account di archiviazione di Azure collegato all'account di archiviazione partner. Tuttavia, è necessario disporre di una sottoscrizione attiva nel tenant CSP.
>


Selezionare **Aggiungi**, digitare un nome per l'esportazione e quindi selezionare l'opzione **Esportazione giornaliera dei costi da inizio mese**. Selezionare **Avanti**.

![Nuovo esempio di esportazione con tipo di esportazione](./media/tutorial-export-acm-data/basics_exports.png)

Specificare la sottoscrizione per l'account di archiviazione di Azure, quindi selezionare l'account di archiviazione.  Specificare il contenitore di archiviazione e il percorso della directory dove si desidera memorizzare il file di esportazione. Selezionare **Avanti**.

![Nuovo esempio di esportazione con i dettagli degli account di archiviazione](./media/tutorial-export-acm-data/storage_exports.png)

Verificare i dati dell'esportazione e selezionare **Crea**.

La nuova esportazione viene visualizzata nell'elenco delle esportazioni. Per impostazione predefinita, le nuove esportazioni sono abilitate. Se si vuole disabilitare o eliminare un'esportazione pianificata, selezionare qualsiasi voce nell'elenco e quindi selezionare **Disabilita** o **Elimina**.

Inizialmente, possono essere necessarie da una a due ore prima che l'esportazione venga eseguita. Tuttavia, possono essere necessarie fino a quattro ore prima che i dati vengano visualizzati nei file esportati.

### <a name="export-schedule"></a>Pianificazione delle esportazioni

Le esportazioni pianificate dipendono dall'ora e dal giorno della settimana in cui è stata creata inizialmente l'esportazione. Quando si crea un'esportazione pianificata, l'esportazione viene eseguita con la stessa frequenza per ogni occorrenza successiva dell'esportazione. Ad esempio, per un'esportazione da inizio mese impostata con frequenza giornaliera, l'esportazione viene eseguita ogni giorno. Analogamente, per un'esportazione settimanale, l'esportazione viene eseguita ogni settimana nello stesso giorno in cui è stata pianificata. L'ora di recapito esatta dell'esportazione non è garantita e i dati esportati sono disponibili entro quattro ore dalla fase di esecuzione.
Con ogni esportazione viene creato un nuovo file, di conseguenza le esportazioni meno recenti non vengono sovrascritte.

Esistono due tipi di opzioni di esportazione:

**Esportazione giornaliera dei costi da inizio mese**: l'esportazione iniziale viene eseguita immediatamente. Le esportazioni successive vengono eseguite il giorno successivo alla stessa ora dell'esportazione iniziale. I dati più recenti vengono aggregati dalle esportazioni giornaliere precedenti.

**Personalizzata**: consente di pianificare esportazioni settimanali e mensili con le opzioni da inizio settimana e da inizio mese. *L'esportazione iniziale verrà eseguita immediatamente.*

Se si ha una sottoscrizione con pagamento in base al consumo, MSDN o Visual Studio, il periodo di fatturazione potrebbe non allinearsi con il mese di calendario. Per tali tipi di sottoscrizioni e gruppi di risorse, è possibile creare un'esportazione allineata al periodo di fatturazione o ai mesi di calendario. Per creare un'esportazione allineata al mese di fatturazione, passare a **Personalizzato**, quindi selezionare **Periodo di fatturazione a oggi**.  Per creare un'esportazione allineata al mese di calendario, selezionare **Da inizio mese**.
>
>

![Nuova esportazione - scheda Informazioni di base in cui è illustrata la selezione dell'opzione personalizzata per l'esportazione settimanale da inizio settimana](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Verificare che i dati vengano raccolti

È possibile verificare che i dati di Gestione costi vengano raccolti e visualizzare il file CSV esportato in tutta semplicità usando Azure Storage Explorer.

Nell'elenco delle esportazioni selezionare il nome dell'account di archiviazione. Nella pagina dell'account di archiviazione selezionare Apri in Explorer. Se viene visualizzata una finestra di conferma, selezionare **Sì** per aprire il file in Azure Storage Explorer.

![Pagina di account di archiviazione con le informazioni di esempio e il collegamento per l’apertura in Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

In Storage Explorer passare al contenitore che si vuole aprire e selezionare la cartella corrispondente al mese corrente. Viene visualizzato un elenco di file CSV. Selezionarne uno e quindi selezionare **Apri**.

![Informazioni di esempio visualizzate in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Il file viene aperto con l'applicazione o il programma impostato per aprire le estensioni di file CSV. Ecco un esempio in Excel.

![Esempio di esportazione di dati CSV visualizzati in Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Accedere a dati esportati da altri sistemi

Uno degli scopi dell'esportazione dei dati di Gestione costi è potervi accedere da sistemi esterni. È possibile usare un dashboard o un altro sistema finanziario. Poiché questi sistemi sono molto diversi tra loro, la visualizzazione di un solo esempio non è sufficiente né utile.  Tuttavia, per iniziare ad accedere ai dati dalle proprie applicazioni, fare riferimento a [Introduzione ad Archiviazione di Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'esportazione giornaliera
> * Verificare che i dati vengano raccolti

Passare alla prossima esercitazione per ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate.

> [!div class="nextstepaction"]
> [Esaminare e implementare i consigli per l'ottimizzazione](tutorial-acm-opt-recommendations.md)
