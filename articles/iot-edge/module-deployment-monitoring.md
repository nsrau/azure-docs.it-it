---
title: Distribuzione automatica di gruppi di dispositivi - Azure IoT Edge | Microsoft Docs
description: Usare le distribuzioni automatiche in Azure IoT Edge per gestire gruppi di dispositivi basati su tag condivisi
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456740"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Informazioni sulle distribuzioni automatiche IoT Edge per singoli dispositivi o su vasta scala

I dispositivi Azure IoT Edge seguono un [ciclo di vita del dispositivo](../iot-hub/iot-hub-device-management-overview.md) simile a quello di altri tipi di dispositivi IoT:

1. Per effettuare il provisioning di dispositivi IoT Edge, effettuare l’imaging di un dispositivo con un sistema operativo e installare il [runtime di IoT Edge](iot-edge-runtime.md).
2. Configurare i dispositivi per l'esecuzione di [moduli IoT Edge](iot-edge-modules.md), quindi monitorarli per determinarne l'integrità. 
3. Infine, ritirare i dispositivi quando vengono sostituiti o diventano obsoleti.  

Azure IoT Edge consente di adottare due approcci per configurare i moduli da eseguire su dispositivi IoT Edge: uno per lo sviluppo e le iterazioni veloci su un singolo dispositivo (ovvero l'approccio usato nelle [esercitazioni](tutorial-deploy-function.md) per Azure IoT Edge) e uno per la gestione di grandi quantità di dispositivi IoT Edge. Entrambi gli approcci sono disponibili nel portale di Azure e a livello di codice. Per i gruppi o per un numero elevato di dispositivi, è possibile specificare i dispositivi a cui distribuire i moduli usando i [tag](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) nel dispositivo gemello. La procedura seguente riguarda una distribuzione in un gruppo di dispositivi nello stato di Washington identificato tramite la proprietà dei tag. 

Questo articolo è incentrato sulle fasi di configurazione e monitoraggio per grandi quantità di dispositivi, collettivamente indicate come distribuzioni automatiche IoT Edge. The overall deployment steps are as follows: 

1. Un operatore definisce una distribuzione che descrive un set di moduli, nonché i dispositivi di destinazione. Each deployment has a deployment manifest that reflects this information. 
2. Il servizio Hub IoT comunica con tutti i dispositivi di destinazione per configurarli con i moduli desiderati. 
3. Il servizio Hub IoT recupera informazioni sullo stato dai dispositivi IoT Edge e li rende disponibili all’operatore.  For example, an operator can see when an Edge device is not configured successfully or if a module fails during runtime. 
4. In qualsiasi momento, i nuovi dispositivi IoT Edge che soddisfano le condizioni di destinazione vengono configurati per la distribuzione. Ad esempio, una distribuzione destinata a tutti i dispositivi IoT Edge in Lombardia configura automaticamente un nuovo dispositivo IoT Edge dopo il provisioning e l'aggiunta al gruppo di dispositivi della Lombardia. 
 
Questo articolo descrive ogni componente coinvolto nella configurazione e nel monitoraggio di una distribuzione. Per istruzioni dettagliate sulla creazione e l'aggiornamento di una distribuzione, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribuzione

Una distribuzione automatica IoT Edge assegna immagini di moduli IoT Edge per l'esecuzione come istanze in un set di destinazione di dispositivi IoT Edge. Il funzionamento è basato sulla configurazione di un manifesto per la distribuzione IoT Edge, che include un elenco di moduli con i parametri di inizializzazione corrispondenti. A deployment can be assigned to a single device (based on Device ID) or to a group of devices (based on tags). Once an IoT Edge device receives a deployment manifest, it downloads and installs the container images from the respective container repositories, and configures them accordingly. Once a deployment is created, an operator can monitor the deployment status to see whether targeted devices are correctly configured.

Solo i dispositivi IoT Edge possono essere configurati con una distribuzione. Prima di poter ricevere la distribuzione, il dispositivo deve soddisfare i prerequisiti seguenti:

* Sistema operativo di base
* Sistema di gestione dei contenitori, come Moby o Docker
* Provisioning del runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifesto della distribuzione

Un manifesto di distribuzione è un documento JSON che descrive i moduli da configurare nei dispositivi IoT Edge di destinazione e contiene i metadati di configurazione per tutti i moduli, inclusi i moduli di sistema richiesti, in particolare l'agente IoT Edge e l'hub IoT Edge.  

I metadati di configurazione per ogni modulo includono: 

* Versione 
* Type 
* Stato (ad esempio, in esecuzione o arrestato) 
* Criterio di riavvio 
* Registro di immagini e contenitori
* Route per l'input e l'output dei dati 

Se l'immagine del modulo è archiviata in una registro contenitori privato, le credenziali del registro sono contenute nell'agente di IoT Edge. 

### <a name="target-condition"></a>Condizione di destinazione

The target condition is continuously evaluated throughout the lifetime of the deployment. Vengono inclusi tutti i nuovi dispositivi che soddisfano i requisiti e i dispositivi che non li soddisfano più vengono rimossi. Se il servizio rileva qualsiasi variazione della condizione di destinazione, la distribuzione viene riattivata. 

Si supponga ad esempio di avere una distribuzione A con una condizione di destinazione tags.environment = 'prod'. Quando viene avviata la distribuzione, sono disponibili 10 dispositivi di produzione. I moduli vengono installati correttamente in questi 10 dispositivi. Lo stato dell'agente di IoT Edge riporta 10 dispositivi totali, 10 risposte corrette, 0 risposte con esito negativo e 0 risposte in sospeso. Si aggiungono ora altri cinque dispositivi con tags.environment = 'prod'. Il servizio rileva la modifica e lo stato dell'agente di IoT Edge riporta 15 dispositivi totali, 10 risposte corrette, 0 risposte con esito negativo e 5 risposte in sospeso quando tenta di eseguire la distribuzione nei cinque nuovi dispositivi.

Usare qualsiasi condizione booleana sui tag dei dispositivi gemelli o deviceId per selezionare i dispositivi di destinazione. Se si intende usare una condizione con tag, è necessario aggiungere la sezione "tags"{} nel dispositivo gemello allo stesso livello delle proprietà. [Altre informazioni sui tag nel dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md)

Esempi di condizione di destinazione:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'

Di seguito sono riportati alcuni vincoli validi quando si crea una condizione di destinazione:

* In un dispositivo gemello è solo possibile creare una condizione di destinazione tramite tags o deviceId.
* Le virgolette doppie non sono consentite in qualsiasi parte della condizione di destinazione. Usare le virgolette singole.
* Le virgolette singole rappresentano i valori della condizione di destinazione. Pertanto, è necessario eseguire l'escape della virgoletta singola con un'altra virgoletta singola se fa parte del nome del dispositivo. Ad esempio, per un dispositivo chiamato `operator'sDevice`, scrivere `deviceId='operator''sDevice'`.
* Nei valori della condizione di destinazione sono consentiti i numeri, le lettere e i caratteri seguenti: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Priorità

Una priorità definisce se un tipo di distribuzione deve essere applicato a un dispositivo di destinazione rispetto ad altre distribuzioni. La priorità di distribuzione è un numero intero positivo crescente per indicare una priorità più alta. Se un dispositivo IoT Edge è la destinazione di più di una distribuzione, si applica la distribuzione con priorità più alta.  Deployments with lower priorities are not applied, nor are they merged.  If a device is targeted with two or more deployments with equal priority, the most recently created deployment (determined by the creation timestamp) applies.

### <a name="labels"></a>Etichette 

Le etichette sono coppie di stringhe chiave/valore che è possibile usare per filtrare e raggruppare le distribuzioni. A deployment may have multiple labels. Le etichette sono facoltative e non influiscono sull'effettiva configurazione dei dispositivi IoT Edge. 

### <a name="deployment-status"></a>Stato della distribuzione

Una distribuzione può essere monitorata per determinare se è stata applicata correttamente per qualsiasi dispositivo IoT Edge di destinazione.  A targeted Edge device will appear in one or more of the following status categories: 

* **Target** (Destinazione): mostra i dispositivi IoT Edge che soddisfano la condizione di destinazione della distribuzione.
* **Actual** (Effettivo): mostra i dispositivi IoT Edge che non rappresentano la destinazione di un'altra distribuzione con priorità più alta.
* **Healthy** (Integro): mostra i dispositivi IoT Edge che confermano al servizio la corretta distribuzione dei moduli. 
* **Unhealthy** (Non integro): mostra i dispositivi IoT Edge che segnalano al servizio che uno o più moduli non sono stati distribuiti correttamente. Per esaminare ulteriormente questo errore, connettersi in remoto a tali dispositivi e visualizzare i file di log.
* **Unknown** (Sconosciuto): mostra i dispositivi IoT Edge che non hanno segnalato alcuno stato in relazione alla distribuzione. Per ulteriori indagini, vedere le informazioni sul servizio e i file di log.

## <a name="phased-rollout"></a>Implementazione graduale 

Un'implementazione graduale è un processo in base al quale un operatore distribuisce le modifiche a un set più ampio di dispositivi IoT Edge. L'obiettivo è apportare le modifiche gradualmente per ridurre il rischio di introdurre modifiche che causano disservizi su larga scala.  

Un'implementazione graduale viene eseguita in base alle fasi e ai passaggi seguenti: 

1. Definire un ambiente di test di dispositivi IoT Edge eseguendone il provisioning e impostando un tag di dispositivo gemello come `tag.environment='test'`. The test environment should mirror the production environment that the deployment will eventually target. 
2. Creare una distribuzione, inclusi i moduli e le configurazioni desiderate. La condizione di destinazione deve essere destinata all'ambiente di test dei dispositivi IoT Edge.   
3. Convalidare la nuova configurazione dei moduli nell'ambiente di test.
4. Aggiornare la distribuzione per includere un sottoinsieme di dispositivi IoT Edge di produzione, aggiungendo un nuovo tag alla condizione di destinazione. Assicurarsi inoltre che la priorità per la distribuzione sia superiore rispetto alle altre distribuzioni attualmente destinate ai dispositivi. 
5. Verificare che la distribuzione venga applicata correttamente nei dispositivi IoT di destinazione visualizzando lo stato di distribuzione.
6. Aggiornare la distribuzione per destinarla a tutti i dispositivi IoT Edge di produzione rimanenti.

## <a name="rollback"></a>Ripristino dello stato precedente

È possibile eseguire il rollback delle distribuzioni in caso di errori o di problemi di configurazione.  Because a deployment defines the absolute module configuration for an IoT Edge device, an additional deployment must also be targeted to the same device at a lower priority even if the goal is to remove all modules.  

Eseguire i rollback con la sequenza seguente: 

1. Verificare che esista una seconda distribuzione destinata allo stesso set di dispositivi. Se l'obiettivo del rollback è rimuovere tutti i moduli, la seconda distribuzione non deve includere alcun modulo. 
2. Modificare o rimuovere l'espressione della condizione di destinazione della distribuzione di cui si vuole eseguire il rollback in modo che i dispositivi non soddisfino più la condizione di destinazione.
3. Verificare che il rollback sia stato eseguito correttamente visualizzando lo stato di distribuzione.
   * La distribuzione di cui viene eseguito il rollback non deve più visualizzare lo stato per i dispositivi inclusi nel rollback.
   * La seconda distribuzione dovrebbe ora includere lo stato di distribuzione per i dispositivi di cui è stato eseguito il rollback.


## <a name="next-steps"></a>Passaggi successivi

* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
* Per approfondire altri concetti relativi a IoT Edge, vedere [Informazioni sul runtime di IoT Edge](iot-edge-runtime.md) e [Informazioni sui moduli IoT Edge](iot-edge-modules.md).

