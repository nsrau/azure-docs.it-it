---
title: Inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di Monitoraggio di Azure usando un'API REST
description: Inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di Monitoraggio di Azure usando un'API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aa842979bf86410e9dab97d6209f336eb6b02bd3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621907"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Inviare metriche personalizzate per una risorsa di Azure all'archivio delle metriche di Monitoraggio di Azure usando un'API REST

Questo articolo illustra come inviare metriche personalizzate per le risorse di Azure nell'archivio delle metriche di Monitoraggio di Azure usando un'API REST. Una volta che le metriche sono nel Monitoraggio di Azure, è possibile eseguire tutte le operazioni eseguibili con le metriche standard. Esempi sono la creazione di grafici, avvisi e il routing verso altri strumenti esterni.  

>[!NOTE]  
>L'API REST consente solo l'invio delle metriche personalizzate per le risorse di Azure. Per inviare metriche per le risorse in ambienti diversi o in locale, è possibile usare [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Creare e autorizzare un'entità servizio a generare metriche 

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni disponibili in [Creare un'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md). 

Tenere presente quanto segue durante questo processo: 

- È possibile immettere qualsiasi URL come URL di accesso.  
- Creare un nuovo segreto client per l'app.  
- Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.  

Assegnare all'app creata come parte del passaggio 1 "Monitoring Metrics Publisher" (Autore delle metriche di monitoraggio) le autorizzazioni per la risorsa di cui si desidera generare le metriche. Se si prevede di usare l'app per generare metriche personalizzate di numerose risorse, è possibile concedere queste autorizzazioni a livello di gruppo di risorse o di sottoscrizione. 

## <a name="get-an-authorization-token"></a>Ottenere un token di autorizzazione
Aprire un prompt dei comandi ed eseguire il comando seguente:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Salvare il token di accesso ottenuto dalla risposta.

![Token di accesso](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Generare la metrica tramite l'API REST 

1. Incollare il codice JSON seguente in un file e salvarlo come  **custommetric.json** nel computer locale. Aggiornare il parametro dell'orario nel file JSON: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Nella finestra del prompt dei comandi inserire i dati delle metriche: 
   - **azureRegion**. Deve corrispondere all'area di distribuzione della risorsa di cui si stanno generando le metriche. 
   - **resourceID**.  ID della risorsa di Azure di cui si stanno rilevando le metriche.  
   - **AccessToken**. Incollare il token acquisito in precedenza.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Modificare il timestamp e i valori nel file JSON. 
1. Ripetere i due passaggi precedenti più volte in modo da disporre dei dati relativi a diversi minuti.

## <a name="troubleshooting"></a>Risoluzione dei problemi 
Se si riceve un messaggio di errore con una parte del processo, prendere in considerazione le seguenti informazioni sulla risoluzione dei problemi:

1. Non è possibile generare metriche rispetto a una sottoscrizione o a un gruppo di risorse come risorsa di Azure. 
1. Non è possibile inserire nell'archivio una metrica più vecchia di 20 minuti. L'archivio delle metriche è ottimizzato per la creazione di avvisi e di grafici in tempo reale. 
2. Il numero di nomi delle dimensioni deve corrispondere ai valori e viceversa. Controllare i valori. 
2. È possibile che vengano generate metriche per un'area geografica che non supporta le metriche personalizzate. Vedere [aree supportate](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Visualizzare le metriche 

1. Accedere al portale di Azure. 

1. Nel menu a sinistra selezionare **Monitoraggio**. 

1. Nella pagina **Monitoraggio** selezionare **Metriche**. 

   ![Selezionare Metriche](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modificare il periodo di aggregazione in **Ultimi 30 minuti**.  

1. Nell'elenco a discesa **Risorsa** selezionare la risorsa di cui si è generata la metrica.  

1. Nell'elenco a discesa **spazi dei nomi** selezionare **QueueProcessing**. 

1. Nell'elenco a discesa **metriche** selezionare **QueueDepth**.  

 
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](../../azure-monitor/platform/metrics-custom-overview.md).

