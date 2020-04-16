---
title: Copia i file nuovi e modificati di LastModifiedDate
description: Informazioni su come usare un modello di soluzione per copiare i file nuovi e modificati di LastModifiedDate con Azure Data Factory.Learn how to use a solution template to copy new and changed files by LastModifiedDate with Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414808"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Copiare i file nuovi e modificati di LastModifiedDate con Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene descritto un modello di soluzione che è possibile utilizzare per copiare i file nuovi e modificati solo da LastModifiedDate da un archivio basato su file a un archivio di destinazione. 

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello seleziona innanzitutto i file nuovi e modificati solo in base ai relativi attributi **LastModifiedDate**, quindi copia i file selezionati dall'archivio origine dati all'archivio di destinazione dati.

Il modello contiene un'attività:
- **Copia** per copiare i file nuovi e modificati solo da LastModifiedDate da un archivio file in un archivio di destinazione.

Il modello definisce sei parametri:
-  *FolderPath_Source* è il percorso della cartella in cui è possibile leggere i file dall'archivio di origine. È necessario sostituire il valore predefinito con il percorso della cartella.
-  *Directory_Source* è il percorso della sottocartella in cui è possibile leggere i file dall'archivio di origine. È necessario sostituire il valore predefinito con il percorso delle sottocartelle.
-  *FolderPath_Destination* è il percorso della cartella in cui si desidera copiare i file nell'archivio di destinazione. È necessario sostituire il valore predefinito con il percorso della cartella.
-  *Directory_Destination* è il percorso della sottocartella in cui si desidera copiare i file nell'archivio di destinazione. È necessario sostituire il valore predefinito con il percorso delle sottocartelle.
-  *LastModified_From* viene utilizzato per selezionare i file il cui attributo LastModifiedDate è after o uguale a questo valore datetime.  Per selezionare solo i nuovi file, che non è stato copiato l'ultima volta, questo valore datetime può essere l'ora in cui la pipeline è stata attivata l'ultima volta. È possibile sostituire il valore predefinito '2019-02-01T00:00:00' con il valore LastModifiedDate previsto nel fuso orario UTC. 
-  *LastModified_To* viene utilizzato per selezionare i file il cui attributo LastModifiedDate è precedente a questo valore datetime. Per selezionare solo i nuovi file, che non sono stati copiati l'ultima volta, questo valore datetime può essere l'ora corrente.  È possibile sostituire il valore predefinito '2019-02-01T00:00:00' con il valore LastModifiedDate previsto nel fuso orario UTC. 

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Vai al modello **Copiare i nuovi file solo per LastModifiedDate**. Creare una **nuova** connessione all'archivio di archiviazione di origine. L'archivio di archiviazione di origine è la posizione da cui si desidera copiare i file.

    ![Creare una nuova connessione all'origine](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Creare una **nuova** connessione all'archivio di destinazione. L'archivio di destinazione è il punto in cui si desidera copiare i file. 

    ![Creare una nuova connessione alla destinazione](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selezionare **Usa questo modello**.

    ![Usa questo modello](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Nel pannello verrà visualizzata la pipeline disponibile, come illustrato nell'esempio seguente:

    ![Mostra la pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selezionare **Debug**, scrivere il valore per **I parametri** e selezionare **Fine**.  Nella foto qui sotto, abbiamo impostato i parametri come segue.
   - **FolderPath_Source** - cartelladiorigine
   - **Directory_Source** : sottocartella
   - **FolderPath_Destination** - cartella di destinazione
   - **Directory_Destination** - sottocartella
   - **LastModified_From** 2019-02-01T00:00:00
   - **LastModified_To** 2019-03-01T00:00:00
    
    Nell'esempio viene fatto indicare che i file, che sono stati modificati l'ultima volta nell'intervallo di tempo (**2019-02-01T00:00:00,** **a 2019-03-01T00:00:00 )** verranno copiati dalla **cartelladi origine nella** cartella di destinazione della cartella di **destinazione.**  È possibile sostituirli con i propri parametri.

    ![Eseguire la pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Esaminare il risultato. Vedrai solo i file modificati per l'ultima volta entro l'intervallo di tempo configurato sono stati copiati nell'archivio di destinazione.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. A questo punto è possibile aggiungere un trigger di windows a cascata per automatizzare questa pipeline, in modo che la pipeline possa sempre copiare i file nuovi e modificati solo da LastModifiedDate periodicamente.  Selezionare **Aggiungi trigger**e selezionare **Nuovo/Modifica**.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Nella finestra **Add Triggers** (Aggiungi trigger) selezionare **+ Nuovo**.

9. Selezionare **Finestra a cascata** per il tipo di trigger, impostare **Ogni 15 minuti** come ricorrenza (è possibile passare a qualsiasi intervallo di tempo). Selezionare **Sì** per Casella Attivato, quindi **scegliere OK**.

    ![Creare un trigger](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Impostare il valore per i **parametri di esecuzione trigger** come indicato di seguito e selezionare **Fine**.
    - **FolderPath_Sourcecartelladi** = **origine**.  È possibile sostituire con la cartella nell'archivio dati di origine.
    - **Directory_Source** = **sottocartella**.  È possibile sostituire con la sottocartella nell'archivio dati di origine.
    - **FolderPath_Destination** = **cartelladestinazione**.  È possibile sostituire con la cartella nell'archivio dati di destinazione.
    - **Directory_Destination** = **sottocartella**.  È possibile sostituire con la sottocartella nell'archivio dati di destinazione.
    - **LastModified_From** =  **LastModified_From\@trigger().outputs.windowStartTime**.  Si tratta di una variabile di sistema dal trigger che determina l'ora in cui la pipeline è stata attivata l'ultima volta.
    - **LastModified_To** = **LastModified_To\@trigger().outputs.windowEndTime**.  Si tratta di una variabile di sistema dal trigger che determina l'ora in cui la pipeline viene attivata questa volta.
    
    ![Parametri di input](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selezionare **Pubblica tutti**.
    
    ![Pubblica tutti](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Creare nuovi file nella cartella di origine dell'archivio origine dati.  Si è ora in attesa che la pipeline venga attivata automaticamente e solo i nuovi file verranno copiati nell'archivio di destinazione.

13. Selezionare la scheda **Monitor** nel pannello di navigazione a sinistra e attendere circa 15 minuti se la ricorrenza del trigger è stata impostata su ogni 15 minuti. 

14. Esaminare il risultato. La pipeline verrà attivata automaticamente ogni 15 minuti e solo i file nuovi o modificati dall'archivio di origine verranno copiati nell'archivio di destinazione in ogni esecuzione della pipeline.

    ![Esaminare il risultato](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)
