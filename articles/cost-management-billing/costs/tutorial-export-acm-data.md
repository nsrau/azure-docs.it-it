---
title: 'Esercitazione: Creare e gestire dati esportati da Gestione costi di Azure'
description: Questo articolo descrive come creare e gestire dati esportati di Gestione costi di Azure per poterli usare in sistemi esterni.
author: bandersmsft
ms.author: banders
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: dcf9b925e7f0ce691a5a50850a30f723d48ec50b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "96007223"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Esercitazione: Creare e gestire dati esportati

Se è già stata seguita l'esercitazione sull'analisi dei costi, si ha familiarità con il download manuale di dati di Gestione costi. Tuttavia, è possibile creare un'attività periodica che esporta automaticamente i dati di Gestione costi nell'archiviazione di Azure su base giornaliera, settimanale o mensile. I dati esportati sono in formato CSV e contengono tutte le informazioni raccolte da Gestione costi. È quindi possibile usare i dati esportati nell'archiviazione di Azure con sistemi esterni e combinarli con dati personalizzati. È possibile usare i dati esportati anche in un sistema esterno come un dashboard o un altro sistema finanziario.

Guarda il video [Come pianificare esportazioni in archiviazione con Gestione costi di Azure](https://www.youtube.com/watch?v=rWa_xI1aRzo) sulla creazione di un'esportazione pianificata dei dati di costo di Azure in Archiviazione di Azure. Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

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

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare o visualizzare un'esportazione di dati o pianificare un'esportazione, aprire l'ambito desiderato nel portale di Azure e selezionare **Analisi dei costi** nel menu. Passare ad esempio a **Sottoscrizioni**, selezionare una sottoscrizione dall'elenco, quindi selezionare **Analisi dei costi** nel menu. Nella parte superiore della pagina Analisi dei costi selezionare **Impostazioni** e quindi **Esportazioni**.

> [!NOTE]
> - Oltre alle sottoscrizioni, è possibile creare esportazioni di gruppi di risorse, gruppi di gestione, reparti e registrazioni. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).
>- Dopo aver eseguito l'accesso come partner a livello dell'ambito dell'account di fatturazione o nel tenant di un cliente, è possibile esportare i dati in un account di archiviazione di Azure collegato all'account di archiviazione partner. Tuttavia, è necessario disporre di una sottoscrizione attiva nel tenant CSP.

1. Selezionare **Aggiungi** e digitare un nome per l'esportazione. 
1. Per **Metrica**, effettuare una selezione:
    - **Costo effettivo (utilizzo e acquisti)** : selezionare questa opzione per esportare dati su utilizzo e acquisti standard
    - **Costo ammortizzato (utilizzo e acquisti)** : selezionare questa opzione per esportare dati sui costi ammortizzati per gli acquisti, come le prenotazioni di Azure
1. Per **Tipo di esportazione**, effettuare una selezione:
    - **Esportazione giornaliera dei costi da inizio mese**: fornisce un nuovo file di esportazione giornaliera per i costi da inizio mese. I dati più recenti vengono aggregati dalle esportazioni giornaliere precedenti.
    - **Esportazione settimanale dei costi per gli ultimi sette giorni**: crea un'esportazione settimanale dei costi relativi agli ultimi sette giorni a partire dalla data di inizio dell'esportazione selezionata.  
    - **Esportazione mensile dei costi dell'ultimo mese**: consente di esportare i costi del mese scorso rispetto al mese corrente in cui si crea l'esportazione. In futuro, la pianificazione esegue un'esportazione il quinto giorno di ogni nuovo mese con i costi dei mesi precedenti.  
    - **Esportazione occasionale**: consente di scegliere un intervallo di date per i dati cronologici da esportare in archiviazione BLOB di Azure. È possibile esportare i costi cronologici relativi a un massimo di 90 giorni a partire dal giorno scelto. Questa esportazione viene eseguita immediatamente ed è disponibile nell'account di archiviazione entro due ore.  
        A seconda del tipo di esportazione, scegliere una data di inizio oppure scegliere le date **Da** e **A**.
1. Specificare la sottoscrizione per l'account di archiviazione di Azure, quindi selezionare un gruppo di risorse o crearne uno nuovo. 
1. Selezionare il nome dell'account di archiviazione o crearne uno nuovo. 
1. Selezionare la località (area di Azure).
1. Specificare il contenitore di archiviazione e il percorso della directory in cui memorizzare il file di esportazione. 
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Esempio di nuova esportazione" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Verificare i dati dell'esportazione e selezionare **Crea**.

La nuova esportazione verrà visualizzata nell'elenco di esportazioni. Per impostazione predefinita, le nuove esportazioni sono abilitate. Se si vuole disabilitare o eliminare un'esportazione pianificata, selezionare qualsiasi voce nell'elenco e quindi selezionare **Disabilita** o **Elimina**.

Inizialmente, possono essere necessarie 12-24 ore prima che l'esportazione venga eseguita. Tuttavia, può essere necessario più tempo prima che i dati vengano visualizzati nei file esportati.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare, preparare l'ambiente per l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Dopo aver eseguito l'accesso, usare il comando [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list) per vedere le esportazioni correnti:

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Oltre alle sottoscrizioni, è possibile creare esportazioni di gruppi di risorse e gruppi di gestione. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).
   >* Dopo aver eseguito l'accesso come partner a livello dell'ambito dell'account di fatturazione o nel tenant di un cliente, è possibile esportare i dati in un account di archiviazione di Azure collegato all'account di archiviazione partner. Tuttavia, è necessario disporre di una sottoscrizione attiva nel tenant CSP.

1. Creare un gruppo di risorse o usarne uno esistente. Per creare un gruppo di risorse, usare il comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Creare un account di archiviazione in cui ricevere i dati esportati oppure usarne uno esistente. Per creare un account di archiviazione, usare il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Per creare l'esportazione, eseguire il comando [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create):

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Per il parametro **--type**, è possibile scegliere `ActualCost`, `AmortizedCost` o `Usage`.

   In questo esempio viene utilizzato `MonthToDate`. L'esportazione crea un file ogni giorno per i costi da inizio mese. I dati più recenti vengono aggregati dalle esportazioni giornaliere del mese corrente.

1. Per vedere i dettagli dell'operazione di esportazione, usare il comando [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Per aggiornare un'esportazione, usare il comando [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update):

   ```azurecli
   az costmanagement export update --name DemoExport 
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Questo esempio cambia la directory di output.

>[!NOTE]
>Inizialmente, possono essere necessarie 12-24 ore prima che l'esportazione venga eseguita. Tuttavia, può essere necessario più tempo prima che i dati vengano visualizzati nei file esportati.

È possibile eliminare un'esportazione usando il comando [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete):

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

---

### <a name="export-schedule"></a>Pianificazione dell'esportazione

Le esportazioni pianificate dipendono dall'ora e dal giorno della settimana in cui è stata creata inizialmente l'esportazione. Quando si crea un'esportazione pianificata, l'esportazione viene eseguita con la stessa frequenza per ogni occorrenza successiva dell'esportazione. Ad esempio, un'esportazione giornaliera dei costi da inizio mese impostata con frequenza giornaliera viene eseguita ogni giorno. Analogamente, per un'esportazione settimanale, l'esportazione viene eseguita ogni settimana nello stesso giorno in cui è stata pianificata. L'ora di recapito esatta dell'esportazione non è garantita e i dati esportati sono disponibili entro quattro ore dall'ora di esecuzione.

Ogni esportazione crea un nuovo file, per cui quelle meno recenti non vengono sovrascritte.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Creare un'esportazione per più sottoscrizioni

Se si ha un Contratto Enterprise, è possibile usare un gruppo di gestione per aggregare le informazioni sui costi della sottoscrizione in un singolo contenitore. È quindi possibile esportare i dati di gestione dei costi per il gruppo di gestione.

Le esportazioni per i gruppi di gestione di altri tipi di sottoscrizione non sono supportate.

1. Se non è stato ancora creato un gruppo di gestione, crearne uno e assegnarvi le sottoscrizioni.
1. Nell'analisi dei costi impostare l'ambito sul gruppo di gestione e scegliere **Select this management group** (Seleziona questo gruppo di gestione).  
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Esempio che mostra l'opzione per selezionare il gruppo di gestione" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Creare un'esportazione a livello di ambito per ottenere i dati di gestione dei costi per le sottoscrizioni nel gruppo di gestione.  
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Esempio che mostra l'opzione Crea nuova esportazione con un ambito del gruppo di gestione":::

## <a name="verify-that-data-is-collected"></a>Verificare che i dati vengano raccolti

È possibile verificare in tutta semplicità che i dati di Gestione costi vengano raccolti e visualizzare il file CSV esportato usando Azure Storage Explorer.

Nell'elenco delle esportazioni selezionare il nome dell'account di archiviazione. Nella pagina dell'account di archiviazione selezionare Apri in Explorer. Se viene visualizzata una finestra di conferma, selezionare **Sì** per aprire il file in Azure Storage Explorer.

![Pagina di account di archiviazione con le informazioni di esempio e il collegamento per l’apertura in Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

In Storage Explorer passare al contenitore che si vuole aprire e selezionare la cartella corrispondente al mese corrente. Viene visualizzato un elenco di file CSV. Selezionarne uno e quindi selezionare **Apri**.

![Informazioni di esempio visualizzate in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Il file viene aperto con l'applicazione o il programma impostato per aprire le estensioni di file CSV. Ecco un esempio in Excel.

![Esempio di esportazione di dati CSV visualizzati in Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Scaricare un file di dati CSV esportato

È anche possibile scaricare il file CSV esportato nel portale di Azure. Nei passaggi seguenti viene illustrato come individuarlo dall'analisi dei costi.

1. Nell'analisi dei costi selezionare **Impostazioni** e quindi selezionare **Esportazioni**.
1. Nell'elenco delle esportazioni selezionare l'account di archiviazione per un'esportazione.
1. Nell'account di archiviazione selezionare **Contenitori**.
1. Nell'elenco dei contenitori selezionare il contenitore.
1. Spostarsi tra le directory e i BLOB di archiviazione fino alla data desiderata.
1. Selezionare il file CSV e quindi selezionare **Scarica**.

[![Download di esportazione di esempio](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Visualizzare la cronologia di esecuzione dell'esportazione  

È possibile visualizzare la cronologia di esecuzione dell'esportazione pianificata selezionando una singola esportazione nella pagina dell'elenco. La pagina dell'elenco di esportazioni fornisce un accesso rapido per visualizzare l'ora di esecuzione delle esportazioni precedenti e l'ora successiva in cui ne verrà eseguita una. Ecco un esempio che mostra la cronologia di esecuzione.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Screenshot che illustra il riquadro Esportazioni.":::

Selezionare un'esportazione per visualizzare la relativa cronologia di esecuzione.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Screenshot che illustra la cronologia di esecuzione di un'esportazione.":::

## <a name="access-exported-data-from-other-systems"></a>Accedere a dati esportati da altri sistemi

Uno degli scopi dell'esportazione dei dati di Gestione costi è potervi accedere da sistemi esterni. È possibile usare un dashboard o un altro sistema finanziario. Poiché questi sistemi sono molto diversi tra loro, la visualizzazione di un solo esempio non è sufficiente né utile.  Tuttavia, per iniziare ad accedere ai dati dalle applicazioni, vedere [Introduzione ad Archiviazione di Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'esportazione giornaliera
> * Verificare che i dati vengano raccolti

Passare alla prossima esercitazione per ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate.

> [!div class="nextstepaction"]
> [Esaminare e implementare i consigli per l'ottimizzazione](tutorial-acm-opt-recommendations.md)
