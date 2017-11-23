---
title: Distribuire i moduli per Azure IoT Edge | Microsoft Docs
description: "Informazioni sulle modalità di distribuzione dei moduli nei dispositivi perimetrali"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d8688ab2daefd400e9c0948853459dd238fa0d43
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala - Anteprima

I dispositivi Azure IoT Edge seguono un [ciclo di vita del dispositivo][lnk-lifecycle] simile a quello di altri tipi di dispositivi IoT:

1. I dispositivi IoT Edge sono sottoposti a provisioning, operazione che prevede la creazione di un'immagine del dispositivo con un sistema operativo e l'installazione del [runtime di IoT Edge][lnk-runtime].
1. I dispositivi vengono configurati per l'esecuzione di [moduli IoT Edge][lnk-modules] e quindi ne viene monitorata l'integrità. 
1. Infine, è possibile ritirare i dispositivi quando vengono sostituiti o diventano obsoleti.  

Azure IoT Edge offre due modi per configurare i moduli da eseguire su dispositivi IoT Edge: uno per lo sviluppo e le iterazioni veloce in un singolo dispositivo (quello usato nelle esercitazioni per Azure IoT Edge) e uno per la gestione di grandi quantità di dispositivi IoT Edge. Entrambi gli approcci sono disponibili nel portale di Azure e a livello di codice.

Questo articolo è incentrato sulle fasi di configurazione e monitoraggio per grandi quantità di dispositivi, collettivamente indicate come distribuzioni IoT Edge. I passaggi per la distribuzione nel suo complesso sono i seguenti:   

1. Un operatore definisce una distribuzione che descrive un set di moduli, nonché i dispositivi di destinazione. Per ogni distribuzione esiste un manifesto della distribuzione che riporta queste informazioni. 
1. Il servizio Hub IoT comunica con tutti i dispositivi di destinazione per configurarli con i moduli desiderati. 
1. Il servizio Hub IoT recupera informazioni sullo stato dei dispositivi IoT Edge e li espone per consentirne il monitoraggio da parte dell'operatore.  Ad esempio, un operatore può vedere quando un dispositivo periferico non è configurato correttamente o se un modulo genera errori in fase di esecuzione. 
1. In qualsiasi momento, i nuovi dispositivi IoT Edge che soddisfano le condizioni di destinazione vengono configurati per la distribuzione. Ad esempio, una distribuzione destinata a tutti i dispositivi IoT Edge in Lombardia configura automaticamente un nuovo dispositivo IoT Edge dopo il provisioning e l'aggiunta al gruppo di dispositivi della Lombardia. 
 
Questo articolo descrive ogni componente coinvolto nella configurazione e nel monitoraggio di una distribuzione. Per istruzioni dettagliate sulla creazione e l'aggiornamento di una distribuzione, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala][lnk-howto].

## <a name="deployment"></a>Distribuzione

Una distribuzione assegna immagini di moduli IoT Edge per l'esecuzione come istanze in un set di destinazione di dispositivi IoT Edge. Il funzionamento è basato sulla configurazione di un manifesto per la distribuzione IoT Edge, che include un elenco di moduli con i parametri di inizializzazione corrispondenti. Una distribuzione può essere assegnata a un singolo dispositivo (in genere in base all'ID del dispositivo) o a un gruppo di dispositivi (in base a tag). Quando un dispositivo IoT Edge riceve un manifesto di distribuzione, scarica e installa le immagini dei contenitori dei moduli dai repository corrispondenti, quindi li configura come specificato. Dopo aver creato una distribuzione, un operatore può monitorare lo stato di distribuzione per verificare se i dispositivi di destinazione sono configurati correttamente.   

È necessario eseguire il provisioning dei dispositivi come dispositivi IoT Edge perché sia possibile configurarli con una distribuzione. Di seguito sono riportati i prerequisiti, non inclusi nella distribuzione:
* Sistema operativo di base
* Docker 
* Provisioning del runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifesto della distribuzione

Un manifesto di distribuzione è un documento JSON che descrive i moduli da configurare nei dispositivi IoT Edge di destinazione e contiene i metadati di configurazione per tutti i moduli, inclusi i moduli di sistema richiesti, in particolare l'agente IoT Edge e l'hub IoT Edge.  

I metadati di configurazione per ogni modulo includono: 
* Versione 
* Tipo 
* Stato (ad esempio, in esecuzione o arrestato) 
* Criteri di riavvio 
* Repository di immagini e contenitori 
* Route per l'input e l'output dei dati 

### <a name="target-condition"></a>Condizione di destinazione

Le condizioni di destinazione specificano se un dispositivo IoT Edge rientra nell'ambito di una distribuzione. Le condizioni di destinazione sono basate su tag di dispositivo gemello. 

### <a name="priority"></a>Priorità

Una priorità definisce se un tipo di distribuzione deve essere applicato a un dispositivo di destinazione rispetto ad altre distribuzioni. La priorità di distribuzione è un numero intero positivo crescente per indicare una priorità più alta. Se un dispositivo IoT Edge è la destinazione di più di una distribuzione, si applica la distribuzione con priorità più alta.  Le distribuzioni con priorità inferiore non vengono applicate, né unite.  Se due distribuzioni con uguale priorità vengono assegnate a un dispositivo, si applica la distribuzione creata più di recente, in base al timestamp di creazione.

### <a name="labels"></a>Etichette 

Le etichette sono coppie di stringhe chiave/valore che è possibile usare per filtrare e raggruppare le distribuzioni. Una distribuzione può avere più etichette. Le etichette sono facoltative e non influiscono sull'effettiva configurazione dei dispositivi IoT Edge. 

### <a name="deployment-status"></a>Stato della distribuzione

Una distribuzione può essere monitorata per determinare se è stata applicata correttamente per qualsiasi dispositivo IoT Edge di destinazione.  Un dispositivo IoT Edge comparirà in una o più delle categorie di stato seguenti: 
* **Target** (Destinazione): mostra i dispositivi IoT Edge che soddisfano la condizione di destinazione della distribuzione.
* **Actual** (Effettivo): mostra i dispositivi IoT Edge che non rappresentano la destinazione di un'altra distribuzione con priorità più alta.
* **Healthy** (Integro): mostra i dispositivi IoT Edge che confermano al servizio la corretta distribuzione dei moduli. 
* **Unhealthy** (Non integro): mostra i dispositivi IoT Edge che segnalano al servizio che uno o più moduli non sono stati distribuiti correttamente. Per esaminare ulteriormente questo errore, connettersi in remoto a tali dispositivi e visualizzare i file di log.
* **Unknown** (Sconosciuto): mostra i dispositivi IoT Edge che non hanno segnalato alcuno stato in relazione alla distribuzione. Per ulteriori indagini, vedere le informazioni sul servizio e i file di log.

## <a name="phased-rollout"></a>Implementazione graduale 

Un'implementazione graduale è un processo in base al quale un operatore distribuisce le modifiche a un set più ampio di dispositivi IoT Edge. L'obiettivo è apportare le modifiche gradualmente per ridurre il rischio di introdurre modifiche che causano disservizi su larga scala.  

Un'implementazione graduale viene eseguita in base alle fasi e ai passaggi seguenti: 
1. Definire un ambiente di test di dispositivi IoT Edge eseguendone il provisioning e impostando un tag di dispositivo gemello come `tag.environment='test'`. L'ambiente di test deve rispecchiare l'ambiente di produzione a cui sarà infine destinata la distribuzione. 
1. Creare una distribuzione, inclusi i moduli e le configurazioni desiderate. La condizione di destinazione deve essere destinata all'ambiente di test dei dispositivi IoT Edge.   
1. Convalidare la nuova configurazione dei moduli nell'ambiente di test.
1. Aggiornare la distribuzione per includere un sottoinsieme di dispositivi IoT Edge di produzione, aggiungendo un nuovo tag alla condizione di destinazione. Assicurarsi inoltre che la priorità per la distribuzione sia superiore rispetto alle altre distribuzioni attualmente destinate ai dispositivi. 
1. Verificare che la distribuzione venga applicata correttamente nei dispositivi IoT di destinazione visualizzando lo stato di distribuzione.
1. Aggiornare la distribuzione per destinarla a tutti i dispositivi IoT Edge di produzione rimanenti.

## <a name="rollback"></a>Rollback

È possibile eseguire il rollback delle distribuzioni in caso di errori o di problemi di configurazione.  Dato che una distribuzione definisce la configurazione assoluta dei moduli per un dispositivo IoT Edge, è necessario destinare una distribuzione aggiuntiva allo stesso dispositivo con una priorità inferiore, anche se l'obiettivo è quello di rimuovere tutti i moduli.  

Eseguire i rollback con la sequenza seguente: 
1. Verificare che esista una seconda distribuzione destinata allo stesso set di dispositivi. Se l'obiettivo del rollback è rimuovere tutti i moduli, la seconda distribuzione non deve includere alcun modulo. 
1. Modificare o rimuovere l'espressione della condizione di destinazione della distribuzione di cui si vuole eseguire il rollback in modo che i dispositivi non soddisfino più la condizione di destinazione.
1. Verificare che il rollback sia stato eseguito correttamente visualizzando lo stato di distribuzione.
   * La distribuzione di cui viene eseguito il rollback non deve più visualizzare lo stato per i dispositivi inclusi nel rollback.
   * La seconda distribuzione dovrebbe ora includere lo stato di distribuzione per i dispositivi di cui è stato eseguito il rollback.


## <a name="next-steps"></a>Passaggi successivi

* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala][lnk-howto].
* Ulteriori informazioni su altri concetti di IoT Edge come il [runtime di IoT Edge][lnk-runtime] e i [moduli di IoT Edge][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md