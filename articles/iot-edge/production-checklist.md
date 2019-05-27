---
title: Preparare dispositivi e distribuzioni per la produzione - Azure IoT Edge | Microsoft Docs
description: Informazioni su come portare la soluzione Azure IoT Edge dallo sviluppo alla produzione, incluse le impostazioni dei dispositivi con i certificati appropriati e l'applicazione di un piano di distribuzione per gli aggiornamenti futuri di codice.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: cb72949c0bb543885498b1b997fa0b4a644c204a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956976"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparare la distribuzione della soluzione IoT Edge alla produzione

Una volta pronti a portare la soluzione IoT Edge dallo sviluppo alla produzione, assicurarsi che sia configurata per prestazioni costanti.

Le informazioni fornite in questo articolo non sono tutti uguali. Per assicurarsi di definire le priorità, ogni sezione inizia con elenchi che dividono il lavoro in due sezioni: **importante** da completare prima di passare all'ambiente di produzione, o **utile** da sapere.

## <a name="device-configuration"></a>Configurazione del dispositivo

I dispositivi IoT Edge possono essere qualsiasi cosa, da un dispositivo Raspberry Pi a un computer portatile, a una macchina virtuale in esecuzione in un server. È possibile accedere al dispositivo fisicamente o tramite una connessione virtuale, oppure può essere isolato per lunghi periodi di tempo. In entrambi i casi, è bene assicurarsi che sia configurato per funzionare in modo appropriato. 

* **Importante**
    * Installare i certificati di produzione
    * Disporre di un piano di gestione dei dispositivi
    * Usare Moby come motore del contenitore

* **Utile**
    * Scegliere il protocollo di upstream

### <a name="install-production-certificates"></a>Installare i certificati di produzione

Ogni dispositivo IoT Edge nell'ambiente di produzione richiede un certificato di autorità di certificazione (CA) del dispositivo installato. Tale certificato della CA viene quindi dichiarato per il runtime di IoT Edge nel file config.yaml file. Per semplificare lo sviluppo e il test, il runtime di IoT Edge consente di creare certificati temporanei se non sono presenti certificati dichiarati nel file config.yaml. Tuttavia, questi certificati temporanei scadono dopo tre mesi e non sono sicuri per gli scenari di produzione. 

Per comprendere il ruolo del certificato della CA del dispositivo, vedere [Come Azure IoT Edge usa i certificati](iot-edge-certs.md).

Per altre informazioni su come installare i certificati su un dispositivo IoT Edge e farvi riferimento dal file config.yaml, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md). I passaggi per configurare i certificati sono gli stessi nel caso in cui il dispositivo verrà essere utilizzato come gateway o meno. In questo articolo vengono forniti gli script per generare i certificati di esempio solo per il test. Non usare i certificati di esempio nell'ambiente di produzione. 

### <a name="have-a-device-management-plan"></a>Disporre di un piano di gestione dei dispositivi

Prima di rendere disponibili tutti i dispositivi nell'ambiente di produzione è necessario sapere come si intende gestire gli aggiornamenti futuri. Per un dispositivo IoT Edge, l'elenco dei componenti da aggiornare può includere:

* Il firmware del dispositivo
* Le librerie del sistema operativo
* Motore del contenitore, ad esempio Moby
* Daemon IoT Edge
* Certificati CA

Per i passaggi per aggiornare il daemon di IoT Edge, vedere [Aggiornare il runtime di IoT Edge](how-to-update-iot-edge.md). Gli attuali metodi per aggiornare il daemon di IoT Edge richiedono l'accesso SSH o fisico al dispositivo IoT Edge. Se si devono aggiornare molti dispositivi, è consigliabile aggiungere i passaggi per l'aggiornamento a uno script o usare uno strumento di automazione come Ansible per eseguire aggiornamenti su larga scala.

### <a name="use-moby-as-the-container-engine"></a>Usare Moby come motore del contenitore

Disporre di un motore del contenitore in un dispositivo è un prerequisito per qualsiasi dispositivo IoT Edge. Solo il motore moby è supportato nell'ambiente di produzione. Gli altri motori del contenitore, come Docker, funzionano con IoT Edge ed è comunque possibile usare questi motori per lo sviluppo. Il motore moby può essere ridistribuito quando usato con Azure IoT Edge e Microsoft fornisce assistenza per questo motore. Non è possibile utilizzare altri motori del contenitore in un dispositivo IoT Edge.

### <a name="choose-upstream-protocol"></a>Scegliere il protocollo di upstream

Il protocollo (e pertanto la porta utilizzata) per le comunicazioni upstream all'hub IoT possono essere configurate per l'agente IoT Edge e l'hub Edge. Il protocollo predefinito è AMQP, ma è possibile modificarlo a seconda della configurazione di rete. 

I due moduli runtime hanno una variabile di ambiente **UpstreamProtocol**. I valori validi per la variabile sono: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configurare la variabile UpstreamProtocol per l'agente Edge nel file config.yaml nel dispositivo stesso. Ad esempio, se il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP, potrebbe essere necessario configurare l'agente Edge per usare AMQP su WebSocket (AMQPWS) per stabilire la connessione iniziale all'hub IoT. 

Una volta che il dispositivo IoT Edge si connette, assicurarsi di continuare a configurare la variabile UpstreamProtocol per entrambi i moduli runtime nelle distribuzioni future. È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribuzione

* **Utile**
    * Essere coerenti con il protocollo upstream
    * Ridurre lo spazio di memoria usato dall'hub Edge
    * Non usare le versioni di debug delle immagini del modulo

### <a name="be-consistent-with-upstream-protocol"></a>Essere coerenti con il protocollo upstream

Se è stato configurato l'agente Edge nel dispositivo IoT Edge per usare un protocollo diverso rispetto al valore AMQP predefinito, è necessario dichiarare lo stesso protocollo in tutte le distribuzioni successive. Ad esempio, se il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP,è probabile che il dispositivo sia stato configurato per connettersi tramite AMQP su WebSocket (AMQPWS). Quando si distribuiscono i moduli nel dispositivo, se non si configura lo stesso protocollo APQPWS per l'agente Edge e l'hub Edge, il valore predefinito AMQP sovrascriverà le impostazioni e impedirà di connettersi nuovamente. 

È necessario configurare solo la variabile di ambiente UpstreamProtocol per l'agente Edge e i moduli di hub Edge. Tutti i moduli aggiuntivi adottano qualsiasi protocollo impostato nei moduli del runtime. 

È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Ridurre lo spazio di memoria usato dall'hub Edge

Se si distribuiscono dispositivi vincolati con memoria limitata disponibile, è possibile configurare l'hub Edge in modo che venga eseguito in una capacità più semplificata e che usi meno spazio su disco. Tuttavia, queste configurazioni limitano le prestazioni dell'hub Edge, quindi occorre trovare il giusto equilibrio adatto per la soluzione specifica. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Non ottimizzare le prestazioni su dispositivi vincolati

L'hub Edge è ottimizzato in termini di prestazioni per impostazione predefinita, perciò tenta di allocare blocchi di memoria di grandi dimensioni. Questa configurazione può causare problemi di stabilità in dispositivi più piccoli come Raspberry Pi. Se si distribuiscono dispositivi con risorse limitate, è consigliabile impostare la variabile di ambiente **OptimizeForPerformance** su **false** nell'hub Edge. 

Per altre informazioni, vedere [Problemi di stabilità nei dispositivi con risorse limitate](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Disabilitare i protocolli non usati

Un altro modo per ottimizzare le prestazioni dell'hub Edge e ridurre l'utilizzo della memoria consiste nel disattivare gli head di protocollo per tutti i protocolli in cui non si usa la soluzione. 

Gli head di protocollo vengono configurati impostando le variabili di ambiente booleane per il modulo dell'hub Edge nei manifesti di distribuzione. Le tre variabili sono:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Tutte le tre variabili hanno *due caratteri di sottolineatura* e possono essere impostate su true o false. 

#### <a name="reduce-storage-time-for-messages"></a>Ridurre il tempo di archiviazione per i messaggi

Il modulo hub Edge memorizza temporaneamente i messaggi che per qualsiasi motivo non possono essere consegnati all'hub IoT. È possibile configurare il tempo di permanenza dell'hub Edge sui messaggi non consegnati prima di lasciarli scadere. In caso di problemi di memoria sul dispositivo, è possibile abbassare il valore **timeToLiveSecs** nel modulo gemello dell'hub Edge. 

Il valore predefinito del parametro timeToLiveSecs è 7200 secondi, che equivale a due ore. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Non usare le versioni di debug delle immagini del modulo

Quando si passa da scenari di test a scenari di produzione, ricordarsi di rimuovere le configurazioni di debug dai manifesti di distribuzione. Verificare che nessuna delle immagini dei moduli nei manifesti di distribuzione abbia il suffisso  **\.debug**. Se sono state aggiunte opzioni di creazione per esporre le porte nei moduli per il debug, rimuovere anche queste opzioni di creazione. 

## <a name="container-management"></a>Gestione di contenitori

* **Importante**
    * Gestire l'accesso nel registro contenitori
    * Usare tag per gestire le versioni

### <a name="manage-access-to-your-container-registry"></a>Gestire l'accesso nel registro contenitori

Prima di distribuire moduli nei dispositivi IoT Edge di produzione, assicurarsi di controllare l'accesso al registro contenitori in modo che gli utenti esterni non possano accedere o apportare modifiche alle immagini del contenitore. Usare un registro contenitori privato, non pubblico, per gestire le immagini del contenitore. 

Nelle esercitazioni e in altri documenti, viene indicato di usare nel dispositivo IoT Edge le stesse credenziali del registro contenitori usate nel computer di sviluppo. Queste istruzioni sono destinate solamente a facilitare la configurazione degli ambienti di test e di sviluppo e non devono essere seguite in uno scenario di produzione. Registro Azure Container consiglia [l'autenticazione con le entità servizio](../container-registry/container-registry-auth-service-principal.md) quando le applicazioni o i servizi eseguono il pull delle immagini del contenitore in modo automatico o automatizzato, così come i dispositivi IoT Edge. Creare un'entità servizio con accesso in lettura al registro contenitori e specificare tale nome utente e password nel manifesto di distribuzione.

### <a name="use-tags-to-manage-versions"></a>Usare tag per gestire le versioni

Un tag è un concetto di docker che è possibile usare per distinguere tra le versioni di contenitori docker. I tag sono suffissi come **1.0** che vanno alla fine di un repository del contenitore. Ad esempio, **mcr.microsoft.com/azureiotedge-agent:1.0**. I tag sono modificabili e possono essere modificati in modo da puntare a un altro contenitore in qualsiasi momento, in modo che il team debba concordare una convenzione da seguire quando si aggiornano le immagini del modulo. 

I tag consentono inoltre di applicare gli aggiornamenti sui dispositivi IoT Edge. Quando si esegue il push di una versione aggiornata di un modulo nel registro contenitori, incrementare il tag. Eseguire quindi il push di una nuova distribuzione per i dispositivi con il tag incrementato. Il motore contenitore riconoscerà il tag incrementato come una nuova versione ed eseguirà il pull della versione più recente del modulo nel dispositivo. 

Per un esempio di una convenzione di tag, vedere [Aggiornare il runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) per informazioni su come IoT Edge usa i tag in sequenza e tag specifici per tenere traccia delle versioni. 

## <a name="networking"></a>Rete

* **Utile**
    * Verificare la configurazione in uscita e in entrata
    * Connessioni degli elementi consentiti
    * Configurare la comunicazione tramite un proxy

### <a name="review-outboundinbound-configuration"></a>Verificare la configurazione in uscita e in entrata

I canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Per la maggior parte degli scenari di IoT Edge, sono necessarie solo tre connessioni. Il motore del contenitore deve connettersi con il registro contenitori (o i registri) che contiene le immagini del modulo. Il runtime IoT Edge deve connettersi all'hub IoT per recuperare le informazioni di configurazione del dispositivo e per inviare i messaggi e i dati di telemetria. E se si usa il provisioning automatico, il daemon di IoT Edge deve connettersi al servizio di provisioning di dispositivi. Per altre informazioni, vedere [Regole di configurazione del firewall e delle porte](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Connessioni degli elementi consentiti

Se la configurazione di rete richiede di elencare in modo esplicito le connessioni effettuate dai dispositivi IoT Edge, esaminare il seguente elenco di componenti IoT Edge:

* **Agente IoT Edge** apre una connessione MQTT/AMQP permanente all'hub IoT, possibilmente tramite WebSockets. 
* **Hub di IoT Edge** apre una singola connessione AMQP permanente o più connessioni MQTT all'hub IoT, probabilmente tramite WebSockets. 
* **Daemon di IoT Edge** effettua chiamate HTTPS intermittenti all'hub IoT. 

In tutti i tre casi, il nome DNS dovrebbe corrisponde al modello \*.azure-devices.net. 

Inoltre, il **motore del contenitore** effettua chiamate ai registri contenitori tramite HTTPS. Per recuperare le immagini del contenitore del runtime IoT Edge, il nome DNS è mcr.microsoft.com. Il motore del contenitore si connette ad altri registri in base alla configurazione della distribuzione. 

Questo elenco di controllo è un punto di partenza per le regole del firewall:

   | URL (\* = carattere jolly) | Porte TCP in uscita | Uso |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro contenitori Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Accesso DPS (facoltativo) |
   | \*.azurecr.io | 443 | Registri contenitori personali e di terze parti |
   | \*.blob.core.windows.net | 443 | Download dei delta delle immagini | 
   | \*.azure-devices.net | 5671, 8883, 443 | Accesso hub IoT |
   | \*.docker.io  | 443 | Accesso dell'Hub docker (facoltativo) |

### <a name="configure-communication-through-a-proxy"></a>Configurare la comunicazione tramite un proxy

Se i dispositivi vengono distribuiti su una rete che utilizza un server proxy, devono essere in grado di comunicare attraverso il proxy per raggiungere l'hub IoT e i registri contenitori. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestione della soluzione

* **Utile**
    * Impostare i log e la diagnostica
    * Prendere in considerazione i test e le pipeline CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Impostare i log e la diagnostica

In Linux, il daemon di IoT Edge Usa diari come impostazione predefinita la registrazione del driver. È possibile usare lo strumento della riga di comando `journalctl` per eseguire una query dei log daemon. In Windows, il daemon di IoT Edge usa la diagnostica di PowerShell. Usare `Get-IoTEdgeLog` per eseguire query dei log da daemon. Moduli di IoT Edge usano il driver JSON per la registrazione, ovvero l'impostazione predefinita.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando si esegue il test di una distribuzione di IoT Edge, è generalmente possibile accedere ai dispositivi per recuperare i log e risolvere i problemi. In uno scenario di distribuzione, quell'opzione potrebbe non essere disponibile. Considerare in che modo raccogliere informazioni sui dispositivi in produzione. Una possibilità consiste nell'usare un modulo di registrazione che raccoglie le informazioni da altri moduli e le invia al cloud. Un esempio di un modulo di registrazione [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), oppure è possibile progettare il proprio. 

### <a name="place-limits-on-log-size"></a>Impone limiti per le dimensioni del log

Per impostazione predefinita il motore contenitore Moby non imposta limiti di dimensioni del registro contenitori. Nel corso del tempo questo può causare il dispositivo riempia con i log e lo spazio su disco insufficiente. Prendere in considerazione le opzioni seguenti per evitare questo problema:

**Opzione: Impostare i limiti globali che si applicano a tutti i moduli del contenitore**

È possibile limitare le dimensioni di tutti i file di log contenitore nelle opzioni di log del contenitore del motore. L'esempio seguente imposta il driver di log `json-file` (scelta consigliata) con i limiti sulle dimensioni e numero di file:

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

Aggiungere (o accodare) queste informazioni in un file denominato `daemon.json` e lo posiziona la località corretta per la piattaforma del dispositivo.

| Piattaforma | Località |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Il motore del contenitore deve essere riavviato rendere effettive le modifiche.

**Opzione: Modificare le impostazioni di log per ogni modulo del contenitore**

È possibile eseguire questa operazione nel **createOptions** di ciascun modulo. Ad esempio:

    "createOptions": {
        "HostConfig": {
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "10m",
                    "max-file": "3"
                }
            }
        }
    }


**Opzioni aggiuntive nei sistemi Linux**

* Configurare il motore di contenitori per inviare log ad `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) impostando `journald` come il driver di registrazione predefinito. 

* Consente di rimuovere periodicamente i log precedenti dal dispositivo tramite l'installazione di uno strumento logrotate. Usare la specifica del file seguente: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Prendere in considerazione i test e le pipeline CI/CD

Per uno scenario di distribuzione di IoT Edge più efficiente, provare a integrare la distribuzione di produzione nel test e nelle pipeline CI/CD. Azure IoT Edge supporta più piattaforme di integrazione CI/CD, tra cui Azure DevOps. Per altre informazioni, vedere [Integrazione e distribuzione continue in Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [distribuzione automatica di IoT Edge](module-deployment-monitoring.md).
* Vedere come IoT Edge supporta l'[integrazione e la distribuzione continue](how-to-ci-cd.md).
