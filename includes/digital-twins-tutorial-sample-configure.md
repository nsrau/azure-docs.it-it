---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - configurazione del progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f5b332415f1abf7d4c1002bdd4f3bfcef12f1267
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135567"
---
## <a name="configure-the-sample-project"></a>Configurare il progetto di esempio

Configurare quindi un'applicazione client di esempio che interagirà con l'istanza di Gemelli digitali di Azure.

Nel computer passare al file scaricato in precedenza dagli [*esempi end-to-end di Gemelli digitali di Azure*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) e decomprimerlo se non è già stato fatto.

All'interno della cartella passare a _AdtSampleApp_. Aprire _**AdtE2ESample.sln**_ in Visual Studio 2019. 

In Visual Studio selezionare il file _SampleClientApp > **appsettings.json**_ per aprirlo nella finestra di modifica. Questo file verrà usato come file JSON preimpostato con le variabili di configurazione necessarie per eseguire il progetto.

Nel corpo del file sostituire `instanceUrl` con l'URL del *nome host* dell'istanza di Gemelli digitali di Azure (preceduto da *https://* come illustrato di seguito).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salvare e chiudere il file. 

Configurare quindi il file *appsettings.json* in modo che venga copiato nella directory di output quando si compila *SampleClientApp*. A tale scopo, fare clic con il pulsante destro del mouse sul file *appsettings.json* e scegliere *Proprietà*. Nel controllo *Proprietà* modificare il valore della proprietà *Copia nella directory di output* con *Copia se più recente*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Estratto della finestra di Visual Studio che mostra il riquadro Esplora soluzioni con appsettings.json evidenziato e il riquadro Proprietà con la proprietà 'Copia nella directory di output' impostata su 'Copia se più recente'" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Mantenere il progetto _**AdtE2ESample**_ aperto in Visual Studio per continuare a usarlo nell'esercitazione.

