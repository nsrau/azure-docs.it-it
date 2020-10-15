---
title: Ottenere dati meteorologici da partner meteorologici
description: Questo articolo descrive come ottenere i dati meteo dai partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 35acf4e9bd338a0e67b046a59d8884df0626e516
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429269"
---
# <a name="get-weather-data-from-weather-partners"></a>Ottenere dati meteorologici da partner meteorologici

Azure FarmBeats consente di portare i dati meteo dai provider di dati meteorologici usando un Framework connettore basato su Docker. Usando questo Framework, i provider di dati meteorologici implementano un Docker che può essere integrato con FarmBeats. Attualmente, è supportato il seguente provider di dati meteorologici.

  ![Partner FarmBeats](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

I dati meteorologici possono essere usati per generare informazioni dettagliate di utilità pratica e compilare modelli di intelligenza artificiale o ML in FarmBeats.

## <a name="before-you-start"></a>Prima di iniziare

Per ottenere i dati meteorologici, verificare di aver [installato FarmBeats](https://aka.ms/farmbeatsinstalldocumentation). L'integrazione Meteo è supportata nelle versioni 1.2.11 e successive. 

## <a name="enable-weather-integration-with-farmbeats"></a>Abilitare l'integrazione di Weather con FarmBeats

Per iniziare a ottenere i dati meteo nel datahub di FarmBeats:

1. Passare a FarmBeats datahub spavalderia `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Passare all'API/partner e quindi effettuare una richiesta POST. Usare il payload di input seguente:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Ad esempio, per ottenere i dati meteo da DTN, usare il payload seguente. È possibile modificare il nome e la descrizione in base alle proprie preferenze.

   > [!NOTE]
   > Il passaggio seguente richiede una chiave API. Per ottenere una chiave per la sottoscrizione di DTN, contattare DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Per ulteriori informazioni sull'oggetto partner, vedere l' [appendice](get-weather-data-from-weather-partner.md#appendix) in questo articolo.

   Il passaggio precedente effettuerà il provisioning delle risorse per consentire l'esecuzione di Docker nell'ambiente FarmBeats del cliente.  

   Il provisioning delle risorse richiede circa 10-15 minuti.

3. Verificare lo stato dell'oggetto/partner creato nel passaggio precedente. Per controllare lo stato, effettuare una richiesta GET sull'API/partner e verificare lo stato dell'oggetto partner. Dopo che FarmBeats ha effettuato il provisioning del partner, lo stato viene impostato su **attivo**.

4. Nell'API/JobType creare una richiesta GET. Verificare la presenza di processi Meteo creati in precedenza nel processo di aggiunta partner. Nei processi meteorologici, il campo **PipelineName** ha il formato seguente: **partner-name_partner-type_job-Name**.

      A questo punto l'istanza di FarmBeats dispone di un partner dati meteo attivo. È possibile eseguire processi per richiedere dati meteorologici per una posizione specifica (Latitudine e longitudine) e un intervallo di date. I tipi di processo includeranno informazioni dettagliate sui parametri necessari per eseguire i processi Meteo.

      Per DTN, ad esempio, verranno creati i tipi di processo seguenti:
   
      - **get_dtn_daily_observations**: ottenere le osservazioni giornaliere per una località e un periodo di tempo.
      - **get_dtn_daily_forecasts**: ottenere previsioni giornaliere per una località e un periodo di tempo.
      - **get_dtn_hourly_observations**: ottenere le osservazioni orarie per una località e un periodo di tempo.
      - **get_dtn_hourly_forecasts**: ottenere previsioni orarie per una località e un periodo di tempo.

6. Prendere nota dell'ID e dei parametri dei tipi di processo.

7. Passare all'API chiamata/Jobs ed effettuare una richiesta POST in/Jobs. Usare il payload di input seguente:

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

   Ad esempio, per eseguire **get_dtn_daily_observations**, usare il payload seguente:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Il passaggio precedente esegue i processi Meteo in base a quanto definito nel docker del partner e inserisce i dati meteorologici in FarmBeats. È possibile controllare lo stato del processo effettuando una richiesta GET su/Jobs. Nella risposta cercare **CurrentState**. Al termine, **CurrentState** è impostato su **succeeded**.

## <a name="query-ingested-weather-data"></a>Eseguire query sui dati meteo inseriti

Una volta completati i processi meteorologici, è possibile eseguire query sui dati meteorologici inseriti per compilare modelli o informazioni dettagliate di utilità pratica usando le API REST di FarmBeats datahub.

Per eseguire query sui dati meteorologici usando un'API REST FarmBeats:

1. In FarmBeats datahub [spavalderia](https://yourdatahub.azurewebsites.net/swagger), passare all'API/WeatherDataLocation ed effettuare una richiesta GET. La risposta include gli oggetti/WeatherDataLocation creati per il percorso (Latitudine e longitudine) specificati per l'esecuzione del processo. Prendere nota dell' **ID** e del **weatherDataModelId** degli oggetti.

2. Eseguire una richiesta GET/{ID} nell'API/WeatherDataModel per **weatherDataModelId** come in precedenza. Il modello di dati meteorologici Mostra tutti i metadati e i dettagli relativi ai dati meteorologici inseriti. Nell'oggetto modello di dati meteo, ad esempio, la misura Meteo indica le informazioni meteorologiche supportate e i tipi e le unità. Ad esempio:

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

3. Passare all'API di telemetria ed effettuare una richiesta POST. Usare il payload di input seguente:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    La risposta Mostra i dati meteo disponibili per l'intervallo di tempo specificato:

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

Nell'esempio precedente la risposta Mostra i dati per due timestamp. Mostra anche il nome della misura (temperatura) e i valori dei dati meteorologici segnalati nei due timestamp. Fare riferimento al modello di dati meteorologici associato per interpretare il tipo e l'unità dei valori segnalati.

## <a name="troubleshoot-job-failures"></a>Risolvere gli errori di processo

Per risolvere gli errori di processo, [controllare i registri dei processi](troubleshoot-azure-farmbeats.md#weather-data-job-failures).


## <a name="appendix"></a>Appendice

|        Partner   |  Dettagli   |
| ------- | -------             |
|     DockerDetails-ImageName         |          Nome dell'immagine docker. Ad esempio, docker.io/mydockerimage (Image in hub.docker.com) o myazureacr.azurecr.io/mydockerimage (Image in Azure Container Registry) e così via. Se non viene fornito alcun registro, il valore predefinito è hub.docker.com.      |
|          DockerDetails-imageTag             |         Nome del tag dell'immagine docker. Il valore predefinito è "Latest".     |
|  DockerDetails-credenziali      |  Credenziali per accedere al Docker privato. Il partner fornisce le credenziali.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch SKU della macchina virtuale. Per altre informazioni, vedere [tutte le macchine virtuali Linux disponibili](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <BR> <BR> I valori validi sono ' Small ',' overlarge ',' large ',' a8',' a9',' medium ',' a5',' a6',' a7',' STANDARD_D1',' STANDARD_D2',' STANDARD_D3',' STANDARD_D4',' STANDARD_D11',' STANDARD_D12',' STANDARD_D13',' STANDARD_D14',' a10',' a11',' STANDARD_D1_V2',' STANDARD_D2_V2',' STANDARD_D3_V2',' STANDARD_D4_V2',' STANDARD_D11_V2',' STANDARD_D12_V2',' STANDARD_D13_V2',' STANDARD_D14_V2',' STANDARD_G1',' STANDARD_G2',' STANDARD_G3',' STANDARD_G4' ,' STANDARD_G5',' STANDARD_D5_V2',' BASIC_A1',' BASIC_A2',' BASIC_A3',' BASIC_A4',' STANDARD_A1',' STANDARD_A2',' STANDARD_A3',' STANDARD_A4',' STANDARD_A5',' STANDARD_A6',' STANDARD_A7',' STANDARD_A8',' STANDARD_A9',' STANDARD_A10',' STANDARD_A11',' STANDARD_D15_V2',' STANDARD_F1',' STANDARD_F2',' STANDARD_F4',' STANDARD_F8',' STANDARD_F16',' STANDARD_NV6',' STANDARD_NV12',' STANDARD_NV24',' STANDARD_NC6',' STANDARD_NC12',' STANDARD_NC24',' STANDARD_NC24r ' ,' STANDARD_H8',' STANDARD_H8m ',' STANDARD_H16',' STANDARD_H16m ',' STANDARD_H16mr ',' STANDARD_H16r ',' STANDARD_A1_V2',' STANDARD_A2_V2',' STANDARD_A4_V2',' STANDARD_A8_V2',' STANDARD_A2m_V2',' STANDARD_A4m_V2',' STANDARD_A8m_V2',' STANDARD_M64ms ',' STANDARD_M128s ' è STANDARD_D2_V3'. *Il valore predefinito è "STANDARD_D2_V2".*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Numero di nodi computer dedicati per pool di batch. Il valore predefinito è 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    ID SKU dell'agente del nodo Azure Batch. Attualmente, è supportato solo l'agente del nodo batch "batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | Credenziali per chiamare l'API partner in docker. Il partner fornisce queste informazioni in base al meccanismo di autorizzazione supportato; ad esempio nome utente e password o chiavi API. |
| partnerType | "Weather". Altri tipi di partner in FarmBeats sono "Sensor" e "Imager".  |
|  name   |   Nome desiderato del partner nel sistema FarmBeats.   |
|  description |  Descrizione   |

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati sottoposti a query i dati del sensore dall'istanza di Azure FarmBeats, è possibile ottenere informazioni su come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le farm.
