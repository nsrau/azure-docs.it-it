---
title: Ottenere dati meteorologici da partner meteorologici
description: Questo articolo descrive come ottenere i dati meteo dai partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266162"
---
# <a name="get-weather-data-from-weather-partners"></a>Ottenere dati meteorologici da partner meteorologici

Azure FarmBeats consente di portare i dati meteo dai provider di dati meteorologici usando un Framework connettore basato su Docker. Usando questo Framework, i provider di dati meteorologici implementano un Docker che può essere integrato con FarmBeats. Attualmente sono supportati i provider di dati meteorologici seguenti:

[Dati NOAA da Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/)

I dati meteorologici possono essere usati per generare informazioni dettagliate di utilità pratica e creare modelli AI/ML in FarmBeats.

## <a name="before-you-start"></a>Prima di iniziare

Per ottenere i dati meteorologici, verificare di aver installato FarmBeats. L' **integrazione Meteo è supportata nella versione 1.2.11 o successiva**. Per installare Azure FarmBeats, vedere [Install FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Abilitare l'integrazione di Weather con FarmBeats

Per avviare il recupero dei dati meteorologici nell'hub dati FarmBeats, seguire questa procedura:

1. Passare alla spavalderia dell'hub dati FarmBeats (https://yourdatahub.azurewebsites.net/swagger)

2. Passare all'API/partner ed effettuare una richiesta POST con il payload di input seguente:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Ad esempio, per ottenere i dati meteo da NOAA da Azure Open DataSets, usare il payload seguente. È possibile modificare il nome e la descrizione in base alle proprie preferenze.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Per ulteriori informazioni sull'oggetto partner, vedere [appendice](get-weather-data-from-weather-partner.md#appendix)

   Il passaggio precedente effettuerà il provisioning delle risorse per consentire l'esecuzione di Docker nell'ambiente FarmBeats del cliente.  

   Sono necessari circa 10-15 minuti per effettuare il provisioning delle risorse sopra indicate.

3. Verificare lo stato dell'oggetto/partner creato nel passaggio 2. A tale scopo, effettuare una richiesta GET sull'API/partner e verificare lo **stato** dell'oggetto partner. Dopo che FarmBeats ha effettuato il provisioning del partner, lo stato è impostato su **attivo**.

4. Passare all'API/JobType ed effettuare una richiesta GET nello stesso. Verificare la presenza di processi Meteo creati come parte del processo di aggiunta partner nel passaggio 2. Il campo **PipelineName** nei processi meteorologici avrà il formato seguente: "partner-name_partner-type_job-Name".

5. A questo punto l'istanza di FarmBeats dispone di un partner dati meteo attivo ed è possibile eseguire i processi per richiedere dati meteorologici per una posizione specifica (Latitudine/Longitudine) e un intervallo di date. In Tipoprocesso sono disponibili informazioni dettagliate sui parametri necessari per eseguire i processi Meteo.

   Ad esempio, per i dati NOAA da Azure Open DataSets, verranno creati i seguenti Tipoprocesso:

   - get_weather_data (Get DSi/Historical Weather Data)
   - get_weather_forecast_data (ottenere i dati meteorologici/di previsione)

6. Prendere nota dell' **ID** e dei parametri del tipoprocesso.

7. Passare all'API chiamata/Jobs ed effettuare una richiesta POST in chiamata/Jobs con il payload di input seguente:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Ad esempio, per eseguire **get_weather_data**, usare il payload seguente:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Il passaggio precedente eseguirà i processi Meteo in base a quanto definito nel docker del partner e inserirà i dati meteorologici in FarmBeats. È possibile controllare lo stato del processo effettuando una richiesta GET su chiamata/Jobs e cercare **CurrentState** nella risposta. Al termine, currentState è impostato su **succeeded**.

## <a name="query-ingested-weather-data"></a>Eseguire query sui dati meteo inseriti

Una volta completati i processi meteorologici, è possibile eseguire query sui dati meteo inseriti per compilare modelli o informazioni dettagliate di utilità pratica. Esistono due modi per accedere ed eseguire query sui dati meteo di FarmBeats:

- API e
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Eseguire query con l'API REST

Per eseguire query sui dati meteorologici usando l'API REST di FarmBeats, seguire questa procedura:

1. In FarmBeats Data Hubhttps://yourdatahub.azurewebsites.net/swagger), passare all'API/WeatherDataLocation ed effettuare una richiesta GET. Per la risposta sono stati creati oggetti/WeatherDataLocation per il percorso (Latitudine/Longitudine) specificato come parte dell'esecuzione del processo. Prendere nota dell' **ID** e del **weatherDataModelId** degli oggetti.

2. Eseguire un'operazione GET/{ID} sull'API/WeatherDataModel per **weatherDataModelId** come indicato nel passaggio 1. Il "modello di dati meteorologici" include tutti i metadati e i dettagli relativi ai dati meteorologici inseriti. Ad esempio, la **misura Meteo** nell'oggetto **modello di dati meteorologici** contiene informazioni dettagliate sulle informazioni meteorologiche supportate e sui tipi e le unità. Ad esempio,

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Prendere nota della risposta dalla chiamata GET/{ID} per il modello di dati meteorologici.

3. Passare all'API di **telemetria** ed effettuare una richiesta post con il payload di input seguente:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. La risposta contenente i dati meteo disponibili per l'intervallo di tempo specificato sarà simile alla seguente:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Nell'esempio precedente, la risposta contiene dati per due timestamp insieme al nome della misura ("temperature") e ai valori dei dati meteorologici segnalati nei due timestamp. È necessario fare riferimento al modello di dati meteorologico associato (come descritto nel passaggio 2 precedente) per interpretare il tipo e l'unità dei valori segnalati.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query con Azure Time Series Insights (TSI)

FarmBeats USA [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) per inserire, archiviare, eseguire query e visualizzare i dati su larga scala, ovvero i dati altamente contestuale e ottimizzati per le serie temporali.

I dati meteo vengono ricevuti in un EventHub e quindi inseriti in un ambiente TSI nel gruppo di risorse FarmBeats. I dati possono quindi essere sottoposti a query direttamente dalla TSI. Per ulteriori informazioni, vedere la [documentazione di TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Seguire i passaggi per visualizzare i dati in TSI:

1. Passare a **portale di Azure** > **gruppo di risorse FarmBeats datahub** > selezionare **Time Series Insights** ambiente (TSI-xxxx) > **criteri di accesso ai dati**. Aggiungere un utente con accesso in lettura o collaboratore.

2. Passare alla pagina **Panoramica** di **Time Series Insights** Environment (TSI-xxxx) e selezionare l' **URL di Time Series Insights Explorer**. È ora possibile visualizzare i dati meteo inseriti.

Oltre all'archiviazione, all'esecuzione di query e alla visualizzazione dei dati meteorologici, TSI consente anche l'integrazione in un dashboard Power BI. Per altre informazioni, vedere [visualizzare i dati da Time Series Insights in Power bi](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Appendice

|        Partner   |  Dettagli   |
| ------- | -------             |
|     DockerDetails-ImageName         |          Nome dell'immagine docker. Ad esempio, docker.io/azurefarmbeats/farmbeats-noaa (Image in hub.docker.com) o myazureacr.azurecr.io/mydockerimage (Image in Azure Container Registry) e così via. Se non viene fornito alcun registro, il valore predefinito è hub.docker.com      |
|          DockerDetails-imageTag             |         Nome del tag dell'immagine docker. Il valore predefinito è "Latest"     |
|  DockerDetails-credenziali      |  Credenziali per accedere al Docker privato. Che verrà fornito dal partner al cliente   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch SKU della macchina virtuale. Vedere [qui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per tutte le macchine virtuali Linux disponibili. I valori validi sono: "Small", "overlarge", "large", "a8", "A9", "medium", "a5", "a6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4',' STANDARD_D11',' STANDARD_D12',' STANDARD_D13',' STANDARD_D14',' a10',' a11',' STANDARD_D1_V2',' STANDARD_D2_V2',' STANDARD_D3_V2',' STANDARD_D4_V2',' STANDARD_D11_V2',' STANDARD_D12_V2',' STANDARD_D13_V2',' STANDARD_D14_V2',' STANDARD_G1',' STANDARD_G2',' STANDARD_G3',' STANDARD_G4' ,' STANDARD_G5',' STANDARD_D5_V2',' BASIC_A1',' BASIC_A2',' BASIC_A3',' BASIC_A4',' STANDARD_A1',' STANDARD_A2',' STANDARD_A3',' STANDARD_A4',' STANDARD_A5',' STANDARD_A6',' STANDARD_A7',' STANDARD_A8',' STANDARD_A9',' STANDARD_A10',' STANDARD_A11',' STANDARD_D15_V2',' STANDARD_F1',' STANDARD_F2',' STANDARD_F4',' STANDARD_F8',' STANDARD_F16',' STANDARD_NV6',' STANDARD_NV12',' STANDARD_NV24',' STANDARD_NC6',' STANDARD_NC12',' STANDARD_NC24',' STANDARD_NC24r ' ,' STANDARD_H8',' STANDARD_H8m ',' STANDARD_H16',' STANDARD_H16m ',' STANDARD_H16mr ',' STANDARD_H16r ',' STANDARD_A1_V2',' STANDARD_A2_V2',' STANDARD_A4_V2',' STANDARD_A8_V2',' STANDARD_A2m_V2',' STANDARD_A4m_V2',' STANDARD_A8m_V2',' STANDARD_M64ms ',' STANDARD_M128s ',' STANDARD_D2_V3'. **Il valore predefinito è "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  No. dei nodi computer dedicati per il pool di batch. Il valore predefinito è 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID SKU dell'agente del nodo Azure Batch. Attualmente è supportato solo l'agente del nodo batch "batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | credenziali per la chiamata dell'API partner in docker. Il partner deve fornire tali informazioni ai clienti in base al meccanismo di autenticazione supportato, ad esempio. Nome utente/password o chiavi API. |
| partnerType | "Weather" (altri tipi di partner in FarmBeats sono "Sensor" e "Imagery")  |
|  name   |   Nome desiderato del partner nel sistema FarmBeats   |
|  description |  Descrizione   |

## <a name="next-steps"></a>Passaggi successivi

È ora possibile eseguire query sui dati dei sensori dall'istanza di Azure FarmBeats. A questo punto, viene illustrato come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le farm.
