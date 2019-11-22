---
title: Creare un test dell'analizzatore Internet tramite l'interfaccia della riga di comando | Microsoft Docs
description: Questo articolo spiega come creare il primo test dell'analizzatore Internet.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2952f7f24e92b952daafa682eee9d4605537a37b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839515"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Creare un test dell'analizzatore Internet con l'interfaccia della riga di comando (anteprima)

Per creare una risorsa analizzatore Internet, è possibile procedere in due modi, ovvero tramite il portale di Azure o l'[interfaccia della riga di comando](internet-analyzer-create-test-portal.md). Questa sezione illustra come creare una nuova risorsa analizzatore Internet di Azure tramite l'esperienza dell'interfaccia della riga di comando. 


> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

L'anteprima pubblica è disponibile per l'uso a livello globale. Durante l'anteprima l'archiviazione dei dati è tuttavia limitata all'area *Stati Uniti occidentali 2*.

## <a name="object-model"></a>Modello a oggetti
L'interfaccia della riga di comando dell'analizzatore Internet espone i tipi di risorse seguenti:
* **Test**: un test confronta nel tempo le prestazioni degli utenti finali di due endpoint Internet (A e B).
* **Profili**: i test vengono creati in un profilo dell'analizzatore Internet. I profili consentono di raggruppare i test correlati e un singolo profilo può contenere uno o più test.
* **Endpoint preconfigurati**: gli endpoint sono stati impostati per un'ampia gamma di configurazioni (aree, tecnologie di accelerazione e così via). È possibile usare nei test uno qualsiasi di questi endpoint preconfigurati.
* **Scorecard**: una scorecard offre riepiloghi rapidi e significativi dei risultati della misurazione. Fare riferimento a [Interpretazione della scorecard](internet-analyzer-scorecard.md).
* **Serie temporale**: una serie temporale mostra le variazioni di una metrica nel tempo.

## <a name="profile-and-test-creation"></a>Creazione di profili e test
1. Ottenere l'accesso all'anteprima dell'analizzatore Internet seguendo le istruzioni **Come si partecipa all'anteprima?** nelle [Domande frequenti sull'analizzatore Internet di Azure](internet-analyzer-faq.md).
2. [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
3. Eseguire il comando `login` per avviare una sessione dell'interfaccia della riga di comando:
    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.
    In caso contrario, aprire una pagina del browser all'indirizzo https://aka.ms/devicelogin e immettere il codice di autorizzazione visualizzato nel terminale.

4. Accedere con le credenziali dell'account nel browser.

5. Selezionare l'ID sottoscrizione a cui è stato concesso l'accesso all'anteprima pubblica dell'analizzatore Internet.

    Dopo l'accesso, viene visualizzato un elenco di sottoscrizioni associate all'account Azure. Le informazioni della sottoscrizione con `isDefault: true` rappresentano la sottoscrizione attualmente attivata dopo l'accesso. Per selezionare un'altra sottoscrizione, usare il comando [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) con l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere [Usare più sottoscrizioni di Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    È possibile accedere in modo non interattivo, come illustrato nei dettagli in [Accedere con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Facoltativo]** Creare un nuovo gruppo di risorse di Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installare l'estensione dell'analizzatore Internet con l'interfaccia della riga di comando di Azure:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Creare un nuovo profilo dell'analizzatore Internet:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Elencare tutti gli endpoint preconfigurati disponibili per il profilo appena creato:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Creare un nuovo test nel profilo dell'analizzatore Internet appena creato:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Il comando precedente presuppone che `www.contoso.com` e `www.microsoft.com` ospitino entrambi l'immagine da un pixel ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) in percorsi personalizzati. Se non si specifica in modo esplicito alcun percorso dell'oggetto, l'analizzatore Internet userà `/apc/trans.gif` come percorso dell'oggetto per impostazione predefinita, in cui gli endpoint preconfigurati ospitano l'immagine di un pixel. Tenere anche presente che non è necessario specificare lo schema (https/http). L'analizzatore Internet supporta solo endpoint HTTPS, pertanto si presuppone che venga usato il protocollo HTTPS.

11. Il nuovo test dovrebbe essere visualizzato nel profilo dell'analizzatore Internet:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Output di esempio:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Per iniziare a generare misure, il file JavaScript a cui fa riferimento il parametro **scriptFileUri** del test deve essere incorporato nell'applicazione Web. Per le istruzioni specifiche, vedere la pagina [Incorporare il client dell'analizzatore Internet](internet-analyzer-embed-client.md).

13. È possibile monitorare lo stato del test tenendo traccia del relativo valore di "stato":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. È possibile esaminare i risultati raccolti del test generando serie temporali o scorecard per il test:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Passaggi successivi

* Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
* Vedere le informazioni su come incorporare il [client dell'analizzatore Internet](internet-analyzer-embed-client.md) e su come creare un [endpoint personalizzato](internet-analyzer-custom-endpoint.md). 
