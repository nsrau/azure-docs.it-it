---
title: Elaborare i file di testo a lunghezza fissa con i flussi di dati di mapping in Azure Data Factory
description: Informazioni su come elaborare i file di testo a lunghezza fissa in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210676"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Elaborare file di testo a lunghezza fissa usando Data Factory flussi di dati di mapping

Data Factory i flussi di dati di mapping supportano i dati di trasformazione da file di testo a larghezza fissa. Si definirà un set di dati per un file di testo senza un delimitatore e quindi si configureranno le suddivisioni delle sottostringhe in base alla posizione ordinale.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Passare a **+ nuova pipeline** per avviare una nuova pipeline

2. Aggiungere un'attività flusso di dati che verrà utilizzata per l'elaborazione di file a larghezza fissa

  ![Pipeline a larghezza fissa](media/data-flow/fwpipe.png)

3. Nell'attività flusso di dati selezionare nuovo flusso di dati di mapping

4. Aggiungere una trasformazione origine, colonna derivata, selezione e trasformazione sink

  ![Flusso di dati a larghezza fissa](media/data-flow/fw2.png)

5. Configurare la trasformazione origine per l'utilizzo di un nuovo set di dati che sarà di tipo testo delimitato

6. Non impostare alcun delimitatore di colonna e nessuna intestazione

Verranno impostati semplicemente i punti di inizio e le lunghezze del campo per il contenuto del file:

```
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
```

7. Nella scheda proiezione della trasformazione origine dovrebbe essere visualizzata una colonna stringa denominata "column_1"

8. A questo punto, nella colonna derivata creare una nuova colonna

9. Verranno fornite le colonne nomi semplici come col1

10. Quindi, nel generatore di espressioni, digitare:

  ```substring(Column_1,1,4)```

  ![colonna derivata](media/data-flow/fwderivedcol1.png)

10. Ripetere questa operazione per tutte le colonne che è necessario analizzare

12. Fare clic sulla scheda controlla per visualizzare le nuove colonne che verranno generate

  ![ispezionare](media/data-flow/fwinspect.png)

13. Usare la trasformazione seleziona per rimuovere le colonne che non sono necessarie per la trasformazione

  ![selezione trasformazione](media/data-flow/fwselect.png)

14. Usare infine sink per restituire i dati in una cartella:

  ![sink a larghezza fissa](media/data-flow/fwsink.png)

  L'output sarà simile al seguente:

  ![output a larghezza fissa](media/data-flow/fxdoutput.png)

  I dati a larghezza fissa sono ora divisi da quattro caratteri e assegnati a col1, Col2, col3, col4,... In base all'esempio precedente, i dati vengono suddivisi in 4 colonne

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md) del flusso di dati
