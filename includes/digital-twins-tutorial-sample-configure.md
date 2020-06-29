---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - configurazione del progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 463fb7860eb35a7ff45d3794d200c69685825827
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737198"
---
## <a name="configure-the-sample-project"></a>Configurare il progetto di esempio

Configurare quindi un'applicazione client di esempio che interagirà con l'istanza di Gemelli digitali di Azure. Se il progetto di esempio non è ancora stato scaricato, procedere ora [scaricando il repository di esempi di Gemelli digitali di Azure come file ZIP](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Passare al file scaricato nel computer e decomprimerlo.

Una volta all'interno della cartella decompressa, passare a _digital-twins-samples-master/AdtSampleApp/_ . Aprire _**AdtE2ESample.sln**_ in Visual Studio 2019. 

In Visual Studio usare il riquadro *Esplora soluzioni* per creare una copia del file _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (è possibile usare i menu di scelta rapida per copiare e incollare). Rinominare la copia *serviceConfig.json*. Questo file verrà usato come file JSON preimpostato con le variabili di configurazione necessarie per eseguire il progetto.

Selezionare il file *serviceConfig.json* per aprirlo nella finestra di modifica. Modificare il valore `tenantId` con l'*ID directory*, `clientId` con l'*ID applicazione* e `instanceUrl` con l'URL del *nome host* dell'istanza di Gemelli digitali di Azure (preceduto da *https://* come illustrato di seguito).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salvare e chiudere il file. 

Configurare quindi il file *serviceConfig. JSON* da copiare nella directory di output quando si compila il progetto *SampleClientApp*. A tale scopo, fare clic con il pulsante destro del mouse sul file *serviceConfig.json* e scegliere *Proprietà*. Nel controllo *Proprietà* modificare il valore della proprietà *Copia nella directory di output* con *Copia se più recente*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Estratto della finestra di Visual Studio che mostra il riquadro Esplora soluzioni con serviceConfig.json evidenziato e il riquadro Proprietà con la proprietà 'Copia nella directory di output' impostata su 'Copia se più recente'" border="false":::

Mantenere il progetto _**AdtE2ESample**_ aperto in Visual Studio per continuare a usarlo nell'esercitazione.

