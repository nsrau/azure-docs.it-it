---
title: Usare lo strumento di migrazione di Power BI Embedded | Microsoft Docs
description: "Lo strumento di migrazione di Power BI Embedded può essere usato per copiare i report dalle raccolte di aree di lavoro di Power BI a Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Usare lo strumento di migrazione di Power BI Embedded

Lo strumento di migrazione di Power BI Embedded può essere usato per copiare i report dalle raccolte di aree di lavoro di Power BI a Power BI Embedded.

La migrazione del contenuto dalle raccolte di aree di lavoro nel servizio Power BI può essere eseguita in parallelo alla soluzione corrente e non richiede alcun tempo di inattività.

## <a name="limitations"></a>Limitazioni

* I set di dati di cui è stato eseguito il push non possono essere scaricati e devono essere ricreati usando le API REST di Power BI per il servizio Power BI.
* I file PBIX importati prima del 26 novembre 2016 non possono essere scaricati.

## <a name="download"></a>Download

È possibile scaricare l'esempio di strumento di migrazione da [GitHub](https://github.com/Microsoft/powerbi-migration-sample). È possibile scaricare un file ZIP del repository oppure è possibile clonarlo localmente. Una volta eseguito il download, è possibile aprire *powerbi-migration-sample.sln* in Visual Studio per creare ed eseguire lo strumento di migrazione.

## <a name="migration-plans"></a>Piani di migrazione

Il piano di migrazione è costituito semplicemente dai metadati che catalogano il contenuto nelle raccolte di aree di lavoro di Power BI e dalla modalità di pubblicazione in Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Iniziare con un nuovo piano di migrazione

Un piano di migrazione è costituito dai metadati disponibili nelle raccolte di aree di lavoro di Power BI da spostare in Power BI Embedded. Il piano di migrazione viene archiviato come file XML.

È consigliabile iniziare creando un nuovo piano di migrazione. Per creare un nuovo piano di migrazione, eseguire le operazioni seguenti.

1. Selezionare **File** > **New Migration Plan** (Nuovo piano di migrazione).

    ![Nuovo piano di migrazione](media/migrate-tool/migrate-tool-plan.png)

2. Nella finestra di dialogo **Select Power BI Workspace Collections Resource Group** (Seleziona gruppo di risorse di raccolte di aree di lavoro di Power BI) selezionare il menu a discesa **Environment** (Ambiente) e selezionare prod.

3. Verrà chiesto di accedere. Usare l'account di accesso alla sottoscrizione di Azure.

    > [!IMPORTANT]
    > **Non** si tratta dell'account dell'organizzazione di Office 365 usato per accedere a Power BI.

4. Selezionare la sottoscrizione di Azure in cui è archiviata la risorsa Raccolte di aree di lavoro di Power BI.

    ![Selezionare la sottoscrizione ad Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Sotto l'elenco di sottoscrizioni, selezionare il**gruppo di risorse** che contiene le raccolte di aree di lavoro e scegliere **Seleziona**.

    ![Selezionare il gruppo di risorse](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Selezionare **Analyze** (Analizza). Verrà visualizzato un inventario degli elementi nella sottoscrizione di Azure con cui iniziare la creazione del piano.

    ![Selezionare il pulsante per l'analisi](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Il processo di analisi potrebbe richiedere alcuni minuti, a seconda del numero di raccolte di aree di lavoro e della quantità di contenuto presente nella raccolta di aree di lavoro.

7. Al termine**dell'analisi**, verrà chiesto di salvare il piano di migrazione.

A questo punto, il piano di migrazione è stato connesso alla sottoscrizione di Azure. Leggere quanto riportato di seguito per comprendere il flusso per l'uso del piano di migrazione. Il flusso include l'analisi e la pianificazione della migrazione, il download, la creazione di gruppi e l'upload.

### <a name="save-your-migration-plan"></a>Salvare il piano di migrazione

È possibile salvare il piano di migrazione per usarlo successivamente. Verrà creato un file XML contenente tutte le informazioni nel piano di migrazione.

Per salvare il piano di migrazione, procedere come segue.

1. Selezionare **File** > **Save Migration Plan** (Salva piano di migrazione).

    ![Opzione di menu per il salvataggio del piano di migrazione](media/migrate-tool/migrate-tool-save-plan.png)

2. Assegnare un nome al file o usare il nome file generato e scegliere **Save** (Salva).

### <a name="open-an-existing-migration-plan"></a>Aprire un piano di migrazione esistente

È possibile aprire un piano di migrazione salvato per continuare a lavorare alla migrazione.

Per aprire un piano di migrazione esistente, procedere come segue.

1. Selezionare **File** > **Open Existing Migration Plan** (Apri piano di migrazione esistente).

    ![Opzione di menu per l'apertura di un piano di migrazione esistente](media/migrate-tool/migrate-tool-open-plan.png)

2. Selezionare il file di migrazione e quindi **Open** (Apri).

## <a name="step-1-analyze-and-plan-migration"></a>Passaggio 1: Analisi e pianificazione della migrazione

La scheda **Analyze & Plan Migration** (Analisi e pianificazione della migrazione) visualizza il contenuto attuale del gruppo di risorse della sottoscrizione di Azure.

![Scheda per l'analisi e la pianificazione della migrazione](media/migrate-tool/migrate-tool-step1.png)

Come esempio si analizzerà *SampleResourceGroup*.

### <a name="paas-topology"></a>Topologia PaaS

Si tratta di un elenco di *Gruppo di risorse > Raccolte di aree di lavoro > Aree di lavoro*. Per il gruppo di risorse e le raccolte di aree di lavoro verrà visualizzato un nome descrittivo. Per le aree di lavoro verrà visualizzato un GUID.

Gli elementi nell'elenco vengono contrassegnati anche da un colore e un numero nel formato (#/#). Tale valore indica il numero di report che possono essere scaricati.

Il colore nero indica che tutti i report possono essere scaricati. Il colore rosso indica che alcuni report non possono essere scaricati. Il numero a sinistra indica il numero totale di report che possono essere scaricati. Il numero a destra indica il numero totale di report all'interno del raggruppamento.

È possibile selezionare un elemento nella topologia PaaS per visualizzare i report nella sezione dei report.

### <a name="reports"></a>Report

La sezione dei report elenca i report disponibili indicando se possono essere scaricati o meno.

![Elenco di report nelle raccolte di aree di lavoro di Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Struttura di destinazione

La **struttura di destinazione** è la posizione in cui si indica allo strumento dove verranno scaricati gli elementi e come caricarli.

#### <a name="download-plan"></a>Piano di download

Verrà creato automaticamente un percorso. Se lo si desidera, è possibile modificare questo percorso. Se si modifica il percorso, sarà necessario selezionare **Update paths** (Aggiorna percorsi).

**Questa operazione non esegue il download.** Viene solo specificata la struttura dove verranno scaricati i report.

#### <a name="upload-plan"></a>Piano di upload

In questa posizione è possibile specificare un prefisso da usare per le aree di lavoro per le app che verranno create nel servizio Power BI. Dopo il prefisso ci sarà il GUID per l'area di lavoro esistente in Azure.

![Specificare il prefisso del nome del gruppo](media/migrate-tool/migrate-tool-upload-plan.png)

**Questo non comporta la creazione di gruppi nel servizio Power BI.** Viene solo definita la struttura di denominazione per i gruppi.

Se si modifica il prefisso, è necessario selezionare **Generate Upload Plan** (Genera piano di upload).

È possibile fare clic con il pulsante destro del mouse su un gruppo e scegliere di rinominare il gruppo direttamente nel piano di upload, se necessario.

![Opzione del menu di scelta rapida per la ridenominazione del gruppo](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Il nome del *gruppo* non deve contenere spazi o caratteri non validi.

## <a name="step-2-download"></a>Passaggio 2: Download

Nella scheda **Download** verrà visualizzato l'elenco di report e metadati associati. È possibile visualizzare lo stato di esportazione corrente insieme allo stato di esportazione precedente.

![Scheda Download](media/migrate-tool/migrate-tool-download-tab.png)

Sono disponibili due opzioni.

* Selezionare i report specifici e quindi **Download Selected** (Scarica selezionati)
* Selezionare **Download All** (Scarica tutto).

![Pulsante per il download dei report selezionati](media/migrate-tool/migrate-tool-download-options.png)

In caso di esito positivo del download, verrà visualizzato lo stato *Done* (Fine), indicando l'esistenza del file PBIX.

Una volta completato il download, selezionare la scheda **Create Groups** (Creazione gruppi).

## <a name="step-3-create-groups"></a>Passaggio 3: Creazione di gruppi

Dopo avere scaricato i report disponibili, è possibile passare alla scheda **Create Groups** (Creazione gruppi). Questa scheda consente di creare le aree di lavoro per le app nel servizio Power BI in base al piano di migrazione creato. L'area di lavoro per le app viene creata con il nome fornito nella scheda **Upload** in **Analyze & Plan Migration** (Analisi e pianificazione della migrazione).

![Scheda per la creazione dei gruppi](media/migrate-tool/migrate-tool-create-groups.png)

Per creare le aree di lavoro per le app, è possibile selezionare **Create Selected Groups** (Crea gruppi selezionati) o **Create All Missing Groups** (Crea tutti i gruppi mancanti).

Quando si seleziona una di queste opzioni, verrà chiesto di accedere. *È consigliabile usare le credenziali per il servizio Power BI in cui si vogliono creare le aree di lavoro per le app.*

![Schermata di accesso a Power BI](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Verrà creata un'area di lavoro per le app nel servizio Power BI. I report non verranno tuttavia caricati nell'area di lavoro per le app.

È possibile verificare che l'area di lavoro per le app sia stata creata eseguendo l'accesso a Power BI e verificando la presenza dell'area di lavoro. Si noterà che l'area di lavoro è priva di contenuto.

![Area di lavoro per le app nel servizio Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Dopo aver creato l'area di lavoro, è possibile passare alla scheda **Upload**.

## <a name="step-4-upload"></a>Passaggio 4: Upload

Nella scheda **Upload** verranno caricati i report nel servizio Power BI. Verrà visualizzato un elenco dei report scaricati nella scheda Download, insieme al nome del gruppo di destinazione basato sul piano di migrazione.

![Scheda Upload](media/migrate-tool/migrate-tool-upload-tab.png)

È possibile caricare i report selezionati o tutti i report. È anche possibile reimpostare lo stato di upload per caricare nuovamente gli elementi.

È inoltre possibile scegliere cosa fare se c'è un report con lo stesso nome. Le opzioni disponibili sono **Abort** (Interrompi), **Ignore** (Ignora) e **Overwrite** (Sovrascrivi).

![Menu a discesa con le opzioni disponibili se il report c'è già](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Caricare i risultati selezionati](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Nomi di report duplicati

Se si ha un report diverso ma con lo stesso nome, sarà necessario modificare il valore **TargetName** del report. È possibile cambiare il nome modificando manualmente il codice XML del piano di migrazione.

È necessario chiudere lo strumento di migrazione per apportare la modifica e quindi aprire nuovamente lo strumento e il piano di migrazione.

Nell'esempio precedente uno dei report clonati ha avuto esito negativo a causa della presenza di un report con lo stesso nome. Se si esamina il codice XML del piano di migrazione, si vedrà quanto segue.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Per l'elemento con esito negativo, è possibile modificare il nome di SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Sarà quindi possibile aprire nuovamente il piano nello strumento di migrazione e caricare il report con esito negativo.

Tornando a Power BI, si noterà che i report e i set di dati sono stati caricati nell'area di lavoro per le app.

![Elenco di report nel servizio Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Caricare un file PBIX locale

È possibile caricare una versione locale di un file di Power BI Desktop. È necessario chiudere lo strumento, modificare il file XML e inserire il percorso completo del file PBIX locale nella proprietà **PbixPath**.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Dopo aver modificato il file XML, aprire nuovamente il piano nello strumento di migrazione e caricare il report.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>Report di DirectQuery

È necessario eseguire l'aggiornamento per aggiornare la stringa di connessione per i report di DirectQuery. Questa operazione può essere eseguita in *powerbi.com* oppure è possibile eseguire una query a livello di codice sulla stringa di connessione da Power BI Embedded (Paas). Per un esempio, vedere [Estrarre la stringa di connessione di DirectQuery dal report PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

È quindi possibile aggiornare la stringa di connessione per il set di dati nel servizio Power BI (Saas) e impostare le credenziali per l'origine dati. Esaminare gli esempi seguenti per informazioni su come eseguire questa operazione.

* [Aggiornare la stringa di connessione di DirectQuery in Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Impostare le credenziali di DirectQuery in Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere eseguito la migrazione dei report dalle raccolte di aree di lavoro di Power BI a Power BI Embedded, è possibile aggiornare l'applicazione e iniziare a incorporare i report nell'area di lavoro per le app.

Per altre informazioni, vedere [How to migrate Power BI Workspace Collection content to Power BI Embedded](migrate-from-power-bi-workspace-collections.md) (Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)