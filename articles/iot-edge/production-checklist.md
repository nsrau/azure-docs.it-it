---
title: Preparare la distribuzione della soluzione nell'ambiente di produzione - Azure IoT EdgePrepare to deploy your solution in production - Azure IoT Edge
description: Informazioni su come portare la soluzione Azure IoT Edge dallo sviluppo alla produzione, inclusa la configurazione dei dispositivi con i certificati appropriati e la creazione di un piano di distribuzione per aggiornamenti futuri del codice.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dd24631f8e6b4f3f87438bf22654016dd7699950
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618310"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparare la distribuzione della soluzione IoT Edge alla produzione

Una volta pronti a portare la soluzione IoT Edge dallo sviluppo alla produzione, assicurarsi che sia configurata per prestazioni costanti.

Le informazioni fornite in questo articolo non sono tutte uguali. Per assicurarsi di definire le priorità, ogni sezione inizia con elenchi che dividono il lavoro in due sezioni: **importante** da completare prima di passare all'ambiente di produzione, o **utile** da sapere.

## <a name="device-configuration"></a>Configurazione del dispositivo

I dispositivi IoT Edge possono essere qualsiasi cosa, da un dispositivo Raspberry Pi a un computer portatile, a una macchina virtuale in esecuzione in un server. È possibile accedere al dispositivo fisicamente o tramite una connessione virtuale, oppure può essere isolato per lunghi periodi di tempo. In entrambi i casi, si desidera assicurarsi che sia configurato per funzionare in modo appropriato.

* **Importante**
  * Installare i certificati di produzione
  * Disporre di un piano di gestione dei dispositivi
  * Usare Moby come motore del contenitore

* **Utile**
  * Scegliere il protocollo di upstream

### <a name="install-production-certificates"></a>Installare i certificati di produzione

Ogni dispositivo IoT Edge nell'ambiente di produzione richiede un certificato di autorità di certificazione (CA) del dispositivo installato. Tale certificato della CA viene quindi dichiarato per il runtime di IoT Edge nel file config.yaml file. Per gli scenari di sviluppo e test, il runtime di IoT Edge crea certificati temporanei se non vengono dichiarati certificati nel file config.yaml. Tuttavia, questi certificati temporanei scadono dopo tre mesi e non sono sicuri per gli scenari di produzione.

Per comprendere il ruolo del certificato della CA del dispositivo, vedere [Come Azure IoT Edge usa i certificati](iot-edge-certs.md).

Per ulteriori informazioni su come installare i certificati in un dispositivo IoT Edge e farvi riferimento dal file config.yaml, vedere Installare i certificati di [produzione in un dispositivo IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Disporre di un piano di gestione dei dispositivi

Prima di rendere disponibili tutti i dispositivi nell'ambiente di produzione è necessario sapere come si intende gestire gli aggiornamenti futuri. Per un dispositivo IoT Edge, l'elenco dei componenti da aggiornare può includere:

* Il firmware del dispositivo
* Le librerie del sistema operativo
* Motore del contenitore, ad esempio Moby
* Daemon IoT Edge
* Certificati di CA

Per ulteriori informazioni, consultate [Aggiornare il runtime di IoT Edge.](how-to-update-iot-edge.md) Gli attuali metodi per aggiornare il daemon di IoT Edge richiedono l'accesso SSH o fisico al dispositivo IoT Edge. Se si dispone di molti dispositivi da aggiornare, è consigliabile aggiungere i passaggi di aggiornamento a uno script o utilizzare uno strumento di automazione come Ansible.If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>Usare Moby come motore del contenitore

Un motore contenitore è un prerequisito per qualsiasi dispositivo IoT Edge.A container engine is a prerequisite for any IoT Edge device. Solo il motore moby è supportato nell'ambiente di produzione. Gli altri motori del contenitore, come Docker, funzionano con IoT Edge ed è comunque possibile usare questi motori per lo sviluppo. Il motore moby può essere ridistribuito quando usato con Azure IoT Edge e Microsoft fornisce assistenza per questo motore.

### <a name="choose-upstream-protocol"></a>Scegliere il protocollo di upstream

È possibile configurare il protocollo (che determina la porta utilizzata) per la comunicazione upstream all'hub IoT sia per l'agente Edge IoT che per l'hub IoT Edge. Il protocollo predefinito è AMQP, ma è possibile modificarlo a seconda della configurazione di rete.

I due moduli runtime hanno una variabile di ambiente **UpstreamProtocol**. I valori validi per la variabile sono:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configurare la variabile UpstreamProtocol per l'agente IoT Edge nel file config.yaml sul dispositivo stesso. Ad esempio, se il dispositivo Edge IoT si trova dietro un server proxy che blocca le porte AMQP, potrebbe essere necessario configurare l'agente IoT Edge per l'utilizzo di AMQP su WebSocket (AMQPWS) per stabilire la connessione iniziale all'hub IoT.

Una volta che il dispositivo IoT Edge si connette, assicurarsi di continuare a configurare la variabile UpstreamProtocol per entrambi i moduli runtime nelle distribuzioni future. È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribuzione

* **Utile**
  * Essere coerenti con il protocollo upstream
  * Configurare l'archiviazione host per i moduli di sistema
  * Ridurre lo spazio di memoria utilizzato dall'hub IoT Edge
  * Non usare le versioni di debug delle immagini del modulo

### <a name="be-consistent-with-upstream-protocol"></a>Essere coerenti con il protocollo upstream

Se l'agente IoT Edge è stato configurato nel dispositivo IoT Edge per l'utilizzo di un protocollo diverso da quello predefinito di AMQP, è necessario dichiarare lo stesso protocollo in tutte le distribuzioni future. Ad esempio, se il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP,è probabile che il dispositivo sia stato configurato per connettersi tramite AMQP su WebSocket (AMQPWS). Quando si distribuiscono moduli nel dispositivo, configurare lo stesso protocollo AMQPWS per l'agente IoT Edge e l'hub IoT Edge, altrimenti l'AMQP predefinito sostituirà le impostazioni e impedirà di connettersi nuovamente.

È sufficiente configurare la variabile di ambiente UpstreamProtocol per l'agente IoT Edge e i moduli hub IoT Edge. Tutti i moduli aggiuntivi adottano qualsiasi protocollo impostato nei moduli del runtime.

È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Configurare l'archiviazione host per i moduli di sistema

I moduli dell'hub e dell'agente IoT Edge usano l'archiviazione locale per mantenere lo stato e abilitare la messaggistica tra moduli, dispositivi e cloud. Per migliorare l'affidabilità e le prestazioni, configurare i moduli di sistema per l'utilizzo dell'archiviazione nel file system host.

Per ulteriori informazioni, consultate [Archiviazione host per i moduli di sistema.](how-to-access-host-storage-from-module.md)

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Ridurre lo spazio di memoria utilizzato dall'hub IoT Edge

Se si distribuiscono dispositivi vincolati con memoria limitata disponibile, è possibile configurare l'hub IoT Edge per l'esecuzione con una capacità più semplificata e utilizzare meno spazio su disco. Queste configurazioni limitano tuttavia le prestazioni dell'hub IoT Edge, quindi trova il giusto equilibrio adatto alla tua soluzione.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Non ottimizzare le prestazioni su dispositivi vincolati

L'hub IoT Edge è ottimizzato per le prestazioni per impostazione predefinita, pertanto tenta di allocare grandi blocchi di memoria. Questa configurazione può causare problemi di stabilità in dispositivi più piccoli come Raspberry Pi. Se si distribuiscono dispositivi con risorse vincolate, è possibile impostare la variabile di ambiente **OptimizeForPerformance** **su false** nell'hub IoT Edge.

Quando **OptimizeForPerformance** è impostato su **true**, l'intestazione del protocollo MQTT utilizza PooledByteBufferAllocator, che ha prestazioni migliori ma alloca più memoria. L'allocatore non funziona bene sui sistemi operativi a 32 bit o su dispositivi con memoria insufficiente. Inoltre, se ottimizzato per le prestazioni, RocksDb alloca più memoria per il proprio ruolo di provider di archiviazione locale.

Per altre informazioni, vedere [Problemi di stabilità nei dispositivi con risorse limitate](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Disabilitare i protocolli non usati

Un altro modo per ottimizzare le prestazioni dell'hub IoT Edge e ridurre l'utilizzo della memoria consiste nel disattivare le testine di protocollo per tutti i protocolli non utilizzati nella soluzione.

Le teste di protocollo vengono configurate impostando variabili di ambiente booleane per il modulo hub IoT Edge nei manifesti di distribuzione. Le tre variabili sono:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Tutte le tre variabili hanno *due caratteri di sottolineatura* e possono essere impostate su true o false.

#### <a name="reduce-storage-time-for-messages"></a>Ridurre il tempo di archiviazione per i messaggi

Il modulo hub IoT Edge archivia temporaneamente i messaggi se non possono essere recapitati all'hub IoT per qualsiasi motivo. È possibile configurare per quanto tempo l'hub IoT Edge mantiene i messaggi non consegnati prima di lasciarli scadere. In caso di problemi di memoria nel dispositivo, è possibile ridurre il valore **timeToLiveSecs** nel doppio del modulo hub IoT Edge.

Il valore predefinito del parametro timeToLiveSecs è 7200 secondi, che equivale a due ore.

### <a name="do-not-use-debug-versions-of-module-images"></a>Non usare le versioni di debug delle immagini del modulo

Quando si passa da scenari di test a scenari di produzione, ricordarsi di rimuovere le configurazioni di debug dai manifesti di distribuzione. Verificare che nessuna delle immagini del modulo nei manifesti di distribuzione abbia il suffisso ** \.di debug.** Se sono state aggiunte opzioni di creazione per esporre le porte nei moduli per il debug, rimuovere anche queste opzioni di creazione.

## <a name="container-management"></a>Gestione di contenitori

* **Importante**
  * Gestire l'accesso nel registro contenitori
  * Usare tag per gestire le versioni

### <a name="manage-access-to-your-container-registry-with-a-service-principal"></a>Gestire l'accesso al Registro di sistema del contenitore con un'entità servizioManage access to your container registry with a service principal

Prima di distribuire moduli nei dispositivi IoT Edge di produzione, assicurarsi di controllare l'accesso al registro contenitori in modo che gli utenti esterni non possano accedere o apportare modifiche alle immagini del contenitore. Usare un registro contenitori privato, non pubblico, per gestire le immagini del contenitore.

Nelle esercitazioni e in altri documenti, viene indicato di usare nel dispositivo IoT Edge le stesse credenziali del registro contenitori usate nel computer di sviluppo. Queste istruzioni sono destinate solamente a facilitare la configurazione degli ambienti di test e di sviluppo e non devono essere seguite in uno scenario di produzione. Azure Container Registry consiglia l'autenticazione con le entità servizio quando le applicazioni o i servizi estraggono le immagini del contenitore in modo automatico o in altro modo automatico (headless), come fanno i dispositivi IoT Edge.Azure Container Registry recommends [authenticating with service principals](../container-registry/container-registry-auth-service-principal.md) when applications or services pull container images in an automated or otherwise unattended manner (headless), as IoT Edge devices do.

Per creare un'entità servizio, eseguire i due script come descritto in [create a service principal](../container-registry/container-registry-auth-aci.md#create-a-service-principal). Questi script eseguire le attività seguenti:These scripts do the following tasks:

* Il primo script crea l'entità servizio. Restituisce l'ID dell'entità servizio e la password dell'entità servizio. Archiviare questi valori in modo sicuro nei record.

* Il secondo script crea assegnazioni di ruolo da concedere all'entità servizio, che può essere eseguita successivamente se necessario. È consigliabile applicare il ruolo utente `role` **acrPull** per il parametro. Per un elenco dei ruoli, vedere Autorizzazioni e ruoli del [Registro di sistema contenitore](../container-registry/container-registry-roles.md) di AzureFor a list of roles, see Azure Container Registry roles and permissions

Per eseguire l'autenticazione tramite un'entità servizio, specificare l'ID servizio e la password ottenuti dal primo script.

* Per il nome utente o l'ID client, specificare l'ID dell'entità servizio.

* Per la password o il segreto client, specificare la password dell'entità servizio.

Per un esempio di avvio di un'istanza del contenitore con l'interfaccia della riga di comando di Azure, vedere [Autenticazione tramite l'entità servizio.](../container-registry/container-registry-auth-aci.md#authenticate-using-the-service-principal)

### <a name="use-tags-to-manage-versions"></a>Usare tag per gestire le versioni

Un tag è un concetto di docker che è possibile usare per distinguere tra le versioni dei contenitori docker. I tag sono suffissi come **1.0** che vanno alla fine di un repository del contenitore. Ad esempio, **mcr.microsoft.com/azureiotedge-agent:1.0**. I tag sono modificabili e possono essere modificati in modo da puntare a un altro contenitore in qualsiasi momento, in modo che il team debba concordare una convenzione da seguire quando si aggiornano le immagini del modulo.

I tag consentono inoltre di applicare gli aggiornamenti sui dispositivi IoT Edge. Quando si esegue il push di una versione aggiornata di un modulo nel registro contenitori, incrementare il tag. Eseguire quindi il push di una nuova distribuzione per i dispositivi con il tag incrementato. Il motore contenitore riconoscerà il tag incrementato come una nuova versione ed eseguirà il pull della versione più recente del modulo nel dispositivo.

Per un esempio di una convenzione di tag, vedere [Aggiornare il runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) per informazioni su come IoT Edge usa i tag in sequenza e tag specifici per tenere traccia delle versioni.

## <a name="networking"></a>Rete

* **Utile**
  * Verificare la configurazione in uscita e in entrata
  * Consenti connessioni da dispositivi IoT Edge
  * Configurare la comunicazione tramite un proxy

### <a name="review-outboundinbound-configuration"></a>Verificare la configurazione in uscita e in entrata

I canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Per la maggior parte degli scenari di IoT Edge, sono necessarie solo tre connessioni. Il motore del contenitore deve connettersi con il registro contenitori (o i registri) che contiene le immagini del modulo. Il runtime IoT Edge deve connettersi all'hub IoT per recuperare le informazioni di configurazione del dispositivo e per inviare i messaggi e i dati di telemetria. E se si usa il provisioning automatico, il daemon di IoT Edge deve connettersi al servizio di provisioning di dispositivi. Per altre informazioni, vedere [Regole di configurazione del firewall e delle porte](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Consenti connessioni da dispositivi IoT Edge

Se la configurazione di rete richiede esplicitamente il passaggio esplicito delle connessioni effettuate dai dispositivi IoT Edge, esaminare l'elenco seguente dei componenti di IoT Edge:

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
   | \*.azurecr.io | 443 | Registri di container personali e di terze parti |
   | \*.blob.core.windows.net | 443 | Scaricare i delta dell'immagine del Registro di sistema del contenitore di Azure dall'archiviazione BLOBDownload Azure Container Registry image deltas from blob storage |
   | \*.azure-devices.net | 5671, 8883, 443 | Accesso hub IoT |
   | \*.docker.io  | 443 | Accesso all'hub Docker (facoltativo)Docker Hub access (optional) |

Alcune di queste regole del firewall vengono ereditate dal Registro di sistema del contenitore di Azure.Some of these firewall rules are inherited from Azure Container Registry. Per altre informazioni, vedere [Configurare le regole per accedere a un registro](../container-registry/container-registry-firewall-access-rules.md)di un contenitore di Azure dietro un firewall.

### <a name="configure-communication-through-a-proxy"></a>Configurare la comunicazione tramite un proxy

Se i dispositivi vengono distribuiti su una rete che utilizza un server proxy, devono essere in grado di comunicare attraverso il proxy per raggiungere l'hub IoT e i registri contenitori. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestione della soluzione

* **Utile**
  * Impostare i log e la diagnostica
  * Prendere in considerazione i test e le pipeline CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Impostare i log e la diagnostica

In Linux, il daemon IoT Edge utilizza i diari come driver di registrazione predefinito. È possibile usare lo strumento della riga di comando `journalctl` per eseguire una query dei log daemon. In Windows, il daemon di IoT Edge usa la diagnostica di PowerShell. Usare `Get-IoTEdgeLog` per eseguire query dei log da daemon. I moduli IoT Edge usano il driver JSON per la registrazione, che è l'impostazione predefinita.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando si esegue il test di una distribuzione di IoT Edge, è generalmente possibile accedere ai dispositivi per recuperare i log e risolvere i problemi. In uno scenario di distribuzione, quell'opzione potrebbe non essere disponibile. Considerare in che modo raccogliere informazioni sui dispositivi in produzione. Una possibilità consiste nell'usare un modulo di registrazione che raccoglie le informazioni da altri moduli e le invia al cloud. Un esempio di un modulo di registrazione [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), oppure è possibile progettare il proprio.

### <a name="place-limits-on-log-size"></a>Porre limiti alla dimensione del registro

Per impostazione predefinita, il motore contenitore Moby non imposta limiti di dimensione del log del contenitore. Nel corso del tempo questo può portare al dispositivo riempiendo con i registri e l'esaurimento dello spazio su disco. Considerare le seguenti opzioni per evitare questo problema:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opzione: Impostare limiti globali applicabili a tutti i moduli contenitoreOption: Set global limits that apply to all container modules

È possibile limitare la dimensione di tutti i file di log del contenitore nelle opzioni di log del motore del contenitore. L'esempio seguente imposta `json-file` il driver di registro su (consigliato) con limiti di dimensione e numero di file:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Aggiungi (o aggiungi) queste informazioni `daemon.json` a un file denominato e posizionale nella posizione giusta per la piattaforma del tuo dispositivo.

| Piattaforma | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Per rendere effettive le modifiche, è necessario riavviare il motore del contenitore.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opzione: Regolare le impostazioni del registro per ogni modulo contenitore

È possibile farlo in **createOptions** di ogni modulo. Ad esempio:

```yml
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
```

#### <a name="additional-options-on-linux-systems"></a>Opzioni aggiuntive nei sistemi Linux

* Configurare il motore contenitore per inviare i log al `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) impostando `journald` come driver di registrazione predefinito.

* Rimuovere periodicamente i vecchi registri dal dispositivo installando uno strumento logrotate. Usare la specifica del file seguente:

   ```txt
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
