---
title: Copiare i file nuovi e modificati dal LastModifiedDate & lt; con Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per copiare i file nuovi e modificati dal LastModifiedDate & lt; con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111940"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiare i file nuovi e modificati dal LastModifiedDate & lt; con Azure Data Factory

Questo articolo descrive un modello di soluzione che è possibile usare per copiare i file nuovi e modificati solo da LastModifiedDate & lt; da un archivio basato su file a un archivio di destinazione. 

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello consente di selezionare prima di tutto i file nuovi e modificati solo per i relativi attributi **LastModifiedDate & lt;** e quindi copia i file selezionati dall'archivio dati di origine all'archivio di destinazione dei dati.

Il modello contiene un'attività:
- **Copia** per copiare i file nuovi e modificati solo da LastModifiedDate & lt; da un archivio di file a un archivio di destinazione.

Il modello definisce quattro parametri:
-  *FolderPath_Source* è il percorso della cartella in cui è possibile leggere i file dall'archivio di origine. È necessario sostituire il valore predefinito con il proprio percorso di cartella.
-  *FolderPath_Destination* è il percorso della cartella in cui si desidera copiare i file nell'archivio di destinazione. È necessario sostituire il valore predefinito con il proprio percorso di cartella.
-  *LastModified_From* viene usato per selezionare i file il cui attributo LastModifiedDate & lt; è dopo o uguale a questo valore datetime.  Per selezionare solo i nuovi file che non è stato copiato ultima volta, questo valore di data/ora può essere il tempo quando la pipeline è stata attivata l'ultima volta. È possibile sostituire il valore predefinito ' 2019-02-01T00:00:00Z' per il LastModifiedDate & lt; previsto nel fuso orario UTC. 
-  *LastModified_To* consente di selezionare i file il cui attributo LastModifiedDate & lt; è prima di questo valore datetime. Per selezionare solo i nuovi file che non è stato copiato ultima volta, questo valore di data/ora può essere l'ora corrente.  È possibile sostituire il valore predefinito ' 2019-02-01T00:00:00Z' per il LastModifiedDate & lt; previsto nel fuso orario UTC. 

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **copia i nuovi file solo da LastModifiedDate & lt;**. Creare un **New** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è in cui si desidera copiare i file.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Selezionare prima di tutto lo spazio di archiviazione **tipo**. Dopo che lo spazio di archiviazione di input **nome dell'account** e il **chiave dell'account**. Selezionare infine **fine**.

    ![Immettere una stringa di connessione](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Creare un **New** connessione all'archivio di destinazione. L'archivio di destinazione è in cui si desidera copiare i file. È anche necessario immettere le informazioni di connessione dell'archivio di destinazione dei dati simile come è avvenuto nel passaggio 2.

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Selezionare **usare questo modello**.

    ![Usa questo modello](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Nel pannello verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Selezionare **Debug**, scrivere il valore per il **parametri** e selezionare **fine**.  Nell'immagine seguente, vengono impostati i parametri come riportato di seguito.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     L'esempio è che indica i file che sono stati modificati ultimo entro l'intervallo di tempo tra *2019-02-01T00:00:00Z* e *2019-03-01T00:00:00Z* verranno copiati da una cartella */source/*  in una cartella */destination/*.  È possibile sostituire questi valori con i propri parametri.
    
     ![Eseguire la pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Esaminare il risultato. Verranno visualizzati solo i file all'interno dell'applicazione configurata dell'ultima modifica intervallo di tempo è stato copiato nell'archivio di destinazione.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. A questo punto è possibile aggiungere un trigger di windows a cascata per automatizzare questa pipeline, in modo che la pipeline sempre possibile copiare periodicamente i file nuovi e modificati solo da LastModifiedDate & lt;.  Selezionare **Aggiungi trigger**e selezionare **nuova/modifica**.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Nella finestra **Add Triggers** (Aggiungi trigger) selezionare **+ Nuovo**.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Selezionare **finestra a cascata** per il tipo di trigger, impostare **ogni 15 minuti** come la ricorrenza (è possibile cambiare in qualsiasi intervallo di tempo), quindi selezionare **Next**.

    ![Creare trigger](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Scrivere il valore per il **parametri esecuzione Trigger** come segue, quindi selezionare **fine**.
    - **FolderPath_Source** = **/source/**.  È possibile sostituire con la cartella nell'archivio dati di origine.
    - **FolderPath_Destination** = **/destination/**.  È possibile sostituire con la cartella nell'archivio dati di destinazione.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  È una variabile di sistema dal trigger di determinare il tempo quando la pipeline è stata attivata l'ultima volta.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  È una variabile di sistema dal trigger di determinare il tempo quando la pipeline viene attivata questo momento.
    
    ![Parametri di input](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Selezionare **Pubblica tutti**.
    
    ![Pubblica tutti](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Creare nuovi file nella cartella di origine di archivio dell'origine dati.  A questo punto si è in attesa per l'attivazione automatica della pipeline e solo i nuovi file verranno copiati nell'archivio di destinazione.

14. Selezionare **Monitoring** scheda nel riquadro di spostamento sinistro e attendere circa 15 minuti se la ricorrenza del trigger è stata impostata su ogni 15 minuti. 

    ![Selezionare il monitoraggio](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Esaminare il risultato. Si noterà la pipeline verrà attivata automaticamente ogni 15 minuti, e solo i file nuovi o modificati dall'archivio di origine verranno copiati nell'archivio di destinazione in ogni esecuzione della pipeline.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)