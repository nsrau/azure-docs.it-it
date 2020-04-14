---
title: Ottenere i dati meteo dai partner meteorologici
description: In questo articolo viene descritto come ottenere dati meteo dai partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266162"
---
# <a name="get-weather-data-from-weather-partners"></a>Ottenere i dati meteo dai partner meteorologici

Azure FarmBeats helps you to bring weather data from your weather data provider(s) using a docker-based Connector Framework. Utilizzando questo framework, i provider di dati meteorologici implementano una finestra mobile che può essere integrata con FarmBeats. Attualmente sono supportati i seguenti fornitori di dati meteo:

[Dati NOAA da Azure Open Dataset](https://azure.microsoft.com/services/open-datasets/)

I dati meteo possono essere utilizzati per generare informazioni utili e creare modelli AI/ML su FarmBeats.

## <a name="before-you-start"></a>Prima di iniziare

Per ottenere i dati meteo, assicurarsi di aver installato FarmBeats. **L'integrazione meteo è supportata nella versione 1.2.11 o successiva.** Per installare Azure FarmBeats, vedere [Installare FarmBeats.](https://aka.ms/farmbeatsinstalldocumentation)

## <a name="enable-weather-integration-with-farmbeats"></a>Abilitare l'integrazione meteo con FarmBeats

Per iniziare a ottenere dati meteo nell'hub FarmBeats Data, attenersi alla seguente procedura:

1. Vai al tuo hub FarmBeats Data swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Passare all'API /Partner ed effettuare una richiesta POST con il payload di input seguente:Navigate to /Partner API and make a POST request with the following input payload:

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

   Ad esempio, per ottenere dati meteo da NOAA by Azure Open Datasets, usare il payload riportato di seguito. È possibile modificare il nome e la descrizione in base alle proprie preferenze.

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
   > Per ulteriori informazioni sull'oggetto Partner, vedere [Appendice](get-weather-data-from-weather-partner.md#appendix)

   Il passaggio precedente eseguirà il provisioning delle risorse per consentire l'esecuzione di Docker nell'ambiente FarmBeats del cliente.  

   Il provisioning delle risorse precedenti richiede circa 10-15 minuti.

3. Controllare lo stato dell'oggetto /Partner creato nel passaggio 2. A tale scopo, effettuare una richiesta GET sull'API /Partner e verificare **lo stato** dell'oggetto partner. Una volta che FarmBeats esegue correttamente il provisioning del partner, lo stato viene impostato su **Attivo**.

4. Passare all'API /JobType ed effettuare una richiesta GET sullo stesso. Verificare la presenza di processi meteo creati come parte del processo di addizione partner nel passaggio 2. Il campo **pipelineName** nei processi meteo sarà del seguente formato: "partner-name_partner-type_job-name".

5. Ora l'istanza FarmBeats ha un partner di dati meteo attivi ed è possibile eseguire processi per richiedere dati meteo per una determinata posizione (latitudine/longitudine) e un intervallo di date. I JobType avranno i dettagli sui parametri necessari per eseguire i lavori meteo.

   Ad esempio, per i dati NOAA da Azure Open Dataset, verranno creati i seguenti JobType:For example, for NOAA data from Azure Open Datasets, following JobType(s) will be created:

   - get_weather_data (Ottenere dati meteorologici ISD/storici)
   - get_weather_forecast_data (Ottenere i dati meteo GFS/previsione)

6. Prendere nota **dell'ID** e dei parametri dei JobType.

7. Passare all'API /Jobs ed effettuare una richiesta POST su /Jobs con il payload di input seguente:Navigate to /Jobs API and make a POST request on /Jobs with the following input payload:

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

   Ad esempio, per eseguire **get_weather_data**, utilizzare il seguente payload:

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

8. Il passaggio precedente eseguirà i processi meteo come definito nella finestra mobile partner e inserirà i dati meteorologici in FarmBeats. È possibile controllare lo stato del processo effettuando una richiesta GET in /Jobs e cercare **currentState** nella risposta. Al termine, l'oggetto currentState viene impostato su **Succeeded**.

## <a name="query-ingested-weather-data"></a>Interrogare i dati meteo ingeriti

Una volta completati i processi meteo, è possibile eseguire una query sui dati meteo ingeriti per creare modelli o informazioni dettagliate utili. Esistono due modi per accedere ed eseguire query sui dati meteo da FarmBeats:

- API e
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query using REST API

Per eseguire una query sui dati meteo utilizzando l'API REST FarmBeats, attenersi alla seguente procedura:

1. Nell'hub farmBeats Data swagger ( ,https://yourdatahub.azurewebsites.net/swagger)passare all'API /WeatherDataLocation ed effettuare una richiesta GET. La risposta avrà /WeatherDataLocation oggetto creato per la posizione (latitudine/longitudine) che è stato specificato come parte dell'esecuzione del processo. Prendere nota **dell'ID** e **weatherDataModelId** degli oggetti.

2. Creare un'API GET/'id' in /WeatherDataModel per **weatherDataModelId** come indicato nel passaggio 1. Il "Modello di dati meteo" contiene tutti i metadati e i dettagli sui dati meteo ingeriti. Ad esempio, **Misura meteo** all'interno dell'oggetto **Weather Data Model** contiene dettagli sulle informazioni meteo supportate e sui tipi e le unità. Ad esempio,

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

   Prendere nota della risposta dalla chiamata GET/ s id per il modello di dati meteo.

3. Passare all'API di **telemetria** ed effettuare una richiesta POST con il payload di input seguente:Navigate to Telemetry API and make a POST request with the following input payload:

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

Nell'esempio precedente, la risposta contiene i dati per due timestamp insieme al nome della misura ("Temperature") e i valori dei dati meteo segnalati nei due timestamp. È necessario fare riferimento al modello di dati meteo associato (come descritto nel passaggio 2 precedente) per interpretare il tipo e l'unità dei valori segnalati.

### <a name="query-using-azure-time-series-insights-tsi"></a>Eseguire query tramite Azure Time Series Insights (TSI)

FarmBeats usa [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) per l'inserimento, l'archiviazione, l'esecuzione di query e la visualizzazione di dati su scala IoT: dati altamente contestualizzati e ottimizzati per le serie temporali.

I dati meteo vengono ricevuti in un EventHub e quindi inviati a un ambiente TSI all'interno del gruppo di risorse FarmBeats.Weather data is received on an EventHub and then pushed to a TSI environment within FarmBeats resource group. I dati possono quindi essere interrogati direttamente dal TSI. Per ulteriori informazioni, vedere [la documentazione di TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Seguire i passaggi per visualizzare i dati su TSI:

1. Passare al gruppo di**risorse FarmBeats DataHub del** **portale** > di Azure > selezionare **Ambiente Time Series Insights** (tsi-xxxx) > criteri di accesso **ai dati**. Aggiungere un utente con accesso Lettore o Collaboratore.Add user with Reader or Contributor access.

2. Passare alla pagina **Panoramica** dell'ambiente **Time Series Insights** (tsi-xxxx) e selezionare l'URL di Time Series Insights **Explorer.** È ora possibile visualizzare i dati meteo ingeriti.

Oltre all'archiviazione, all'esecuzione di query e alla visualizzazione dei dati meteo, TSI consente anche l'integrazione in un dashboard di Power BI. Per altre informazioni, vedere [Visualizzare i dati da Time Series Insights in Power BI.](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="appendix"></a>Appendice

|        Partner   |  Dettagli   |
| ------- | -------             |
|     DockerDetails - imageName         |          Nome dell'immagine Docker. Ad esempio, docker.io/azurefarmbeats/farmbeats-noaa (immagine in hub.docker.com) OPPURE myazureacr.azurecr.io/mydockerimage (immagine nel Registro di sistema del contenitore di Azure) e così via. Se non viene fornito alcun Registro di sistema, l'impostazione predefinita è hub.docker.com      |
|          DockerDetails - imageTag             |         Nome del tag dell'immagine della finestra mobile. Il valore predefinito è "latest"     |
|  DockerDetails - credenziali      |  Credenziali per accedere alla finestra mobile privata. Questo sarà fornito dal partner al cliente   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    SKU di VM Batch di Azure.Azure Batch VM SKU. Vedere [qui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per tutte le macchine virtuali Linux disponibili. I valori validi sono: "Small", 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2'STANDARD_G4 STANDARD_G3 STANDARD_G2 STANDARD_G1 STANDARD_D14_V2 STANDARD_D13_V2' , 'STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8 STANDARD_F16', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV24 STANDARD_NV12 STANDARD_F16', STANDARD_NC24r STANDARD_NC24 STANDARD_NC12 'STANDARD_NC6'' , 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2 STANDARD_A8_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **Il valore predefinito è "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatoComputerNodes   |  No. di nodi di computer dedicati per il pool batch. Il valore predefinito è 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID SKU dell'agente di nodo batch di Azure.Azure Batch Node Agent SKU ID. Attualmente è supportato solo l'agente nodo batch "batch.node.ubuntu 18.04".    |
| DockerDetails - partnerCredentials | credenziali per chiamare l'API del partner nella finestra mobile. Il partner deve fornire queste informazioni ai clienti in base al meccanismo di autenticazione supportato, ad esempio. Nome utente/password o Chiavi API. |
| partnerType (tipo di partner) | "Meteo" (Altri tipi di partner in FarmBeats sono "Sensor" e "Imagery")  |
|  name   |   Nome desiderato del partner nel sistema FarmBeats   |
|  description |  Descrizione   |

## <a name="next-steps"></a>Passaggi successivi

Sono ora stati sottoposti a query sui dati del sensore dall'istanza di Azure FarmBeats.You have now queryed sensor data from your Azure FarmBeats instance. A questo punto, scopri come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le tue farm.
