---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - configurazione del progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ad9eb95c9e3a21cd33a367cc864a4d791888afb9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124561"
---
## <a name="configure-the-sample-project"></a>Configurare il progetto di esempio

Configurare quindi un'applicazione client di esempio che interagirà con l'istanza di Gemelli digitali di Azure. Se non è già stato fatto, scaricare il progetto di esempio dalla pagina di destinazione degli [*esempi di Gemelli digitali di Azure*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) selezionando il pulsante *Scarica ZIP* sotto il titolo.

Passare al file scaricato nel computer e decomprimerlo.

Dall'interno della cartella decompressa, passare a _AdtSampleApp/_ . Aprire _**AdtE2ESample.sln**_ in Visual Studio 2019. 

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

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Estratto della finestra di Visual Studio che mostra il riquadro Esplora soluzioni con serviceConfig.json evidenziato e il riquadro Proprietà con la proprietà 'Copia nella directory di output' impostata su 'Copia se più recente'" border="false":::

Mantenere il progetto _**AdtE2ESample**_ aperto in Visual Studio per continuare a usarlo nell'esercitazione.

