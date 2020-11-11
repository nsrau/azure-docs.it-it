---
title: Connettere dispositivi IoT Edge downstream-Azure IoT Edge | Microsoft Docs
description: Come configurare un dispositivo IoT Edge per connettersi ai dispositivi Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: d5da6576258d3e33296781bbc262494220140ddc
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489285"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Connettere un dispositivo di IoT Edge downstream a un gateway di Azure IoT Edge (anteprima)

Questo articolo fornisce le istruzioni per stabilire una connessione trusted tra un gateway IoT Edge e un dispositivo IoT Edge downstream.

>[!NOTE]
>Questa funzionalità richiede IoT Edge versione 1,2, disponibile in anteprima pubblica, che esegue contenitori Linux.

In uno scenario di gateway, un dispositivo IoT Edge può essere sia un gateway che un dispositivo downstream. È possibile sovrapporre più gateway IoT Edge per creare una gerarchia di dispositivi. I dispositivi downstream o figlio possono autenticare e inviare o ricevere messaggi tramite il dispositivo gateway (o padre).

Esistono due diverse configurazioni per i dispositivi IoT Edge in una gerarchia del gateway e questo articolo si rivolge a entrambe. Il primo è il **livello superiore** IOT Edge dispositivo. Quando più dispositivi IoT Edge si connettono tra loro, tutti i dispositivi che non dispongono di un dispositivo padre ma che si connette direttamente all'hub Internet è considerato nel livello superiore. Questo dispositivo è responsabile della gestione delle richieste da tutti i dispositivi sottostanti. L'altra configurazione si applica a qualsiasi dispositivo IoT Edge in un **livello inferiore** della gerarchia. Questi dispositivi possono essere un gateway per altri dispositivi downstream e IoT Edge, ma devono anche instradare le comunicazioni attraverso i propri dispositivi padre.

Per alcune architetture di rete è necessario che solo il primo dispositivo IoT Edge in una gerarchia possa connettersi al cloud. In questa configurazione tutti i dispositivi IoT Edge nei livelli inferiori di una gerarchia possono comunicare solo con il dispositivo gateway (o padre) ed eventuali dispositivi downstream (o figlio).

Tutti i passaggi descritti in questo articolo si basano su quelli di [configurare un dispositivo IOT Edge come gateway trasparente](how-to-create-transparent-gateway.md), che configura un dispositivo IOT Edge come gateway per i dispositivi downstream. Gli stessi passaggi di base si applicano a tutti gli scenari di gateway:

* **Autenticazione** : creare le identità dell'hub Internet per tutti i dispositivi nella gerarchia del gateway.
* **Autorizzazione** : configurare la relazione padre/figlio nell'hub degli elementi per autorizzare i dispositivi figlio a connettersi al dispositivo padre come se si connettessero all'hub.
* **Individuazione gateway** : assicurarsi che il dispositivo figlio possa trovare il dispositivo padre nella rete locale.
* **Connessione protetta** : stabilire una connessione protetta con certificati attendibili che fanno parte della stessa catena.

## <a name="prerequisites"></a>Prerequisiti

* Un hub delle cose standard o gratuito.
* Almeno due **dispositivi IOT Edge** , uno come dispositivo di livello superiore e uno o più dispositivi di livello inferiore. Se non si dispone di IoT Edge dispositivi disponibili, è possibile [eseguire Azure IOT Edge su macchine virtuali Ubuntu](how-to-install-iot-edge-ubuntuvm.md).
* Se si usa l'interfaccia della riga di comando di Azure per creare e gestire i dispositivi, fare in modo che l'interfaccia della riga di comando di Azure v 2.3.1 sia installata con l'estensione Azure 0.10.6

Questo articolo fornisce le opzioni e i passaggi dettagliati per creare la gerarchia di gateway corretta per lo scenario. Per un'esercitazione guidata, vedere [creare una gerarchia di dispositivi IOT Edge usando i gateway](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Creare una gerarchia del gateway

Si crea una gerarchia del gateway IoT Edge definendo relazioni padre/figlio per i dispositivi IoT Edge nello scenario. È possibile impostare un dispositivo padre quando si crea una nuova identità del dispositivo oppure è possibile gestire l'elemento padre e gli elementi figlio di un'identità del dispositivo esistente.

Il passaggio di configurazione delle relazioni padre/figlio autorizza i dispositivi figlio a connettersi al dispositivo padre come se si connettessero all'hub.

Solo i dispositivi IoT Edge possono essere dispositivi padre, ma sia i dispositivi IoT Edge che i dispositivi Internet possono essere elementi figlio. Un elemento padre può avere molti elementi figlio, ma un elemento figlio può avere un solo elemento padre. Una gerarchia del gateway viene creata concatenando i set padre/figlio in modo che l'elemento figlio di un dispositivo sia l'elemento padre di un altro.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel portale di Azure è possibile gestire la relazione padre/figlio quando si creano nuove identità del dispositivo o modificando i dispositivi esistenti.

Quando si crea un nuovo dispositivo di IoT Edge, è possibile scegliere i dispositivi padre e figlio dall'elenco dei dispositivi IoT Edge esistenti nell'hub.

1. Nella [portale di Azure](https://portal.azure.com)passare all'hub Internet.
1. Selezionare **IOT Edge** dal menu di navigazione.
1. Selezionare **Aggiungi un dispositivo IoT Edge**.
1. Oltre a impostare l'ID dispositivo e le impostazioni di autenticazione, è possibile **impostare un dispositivo padre** o **scegliere dispositivi figlio**.
1. Scegliere il dispositivo o i dispositivi desiderati come padre o figlio.

È anche possibile creare o gestire relazioni padre/figlio per i dispositivi esistenti.

1. Nella [portale di Azure](https://portal.azure.com)passare all'hub Internet.
1. Selezionare **IOT Edge** dal menu di navigazione.
1. Selezionare il dispositivo che si vuole gestire dall'elenco dei **dispositivi IOT Edge**.
1. Selezionare **imposta un dispositivo padre** o **Gestisci dispositivi figlio**.
1. Aggiungere o rimuovere eventuali dispositivi padre o figlio.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L'estensione [Azure-](/cli/azure/ext/azure-iot) Internet per l'interfaccia della riga di comando di Azure fornisce comandi per la gestione delle risorse Internet. È possibile gestire la relazione padre/figlio dei dispositivi Internet e IoT Edge quando si creano nuove identità dei dispositivi o si modificano i dispositivi esistenti.

Il gruppo [AZ Internet Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set of commands consente di gestire le relazioni padre/figlio per un determinato dispositivo.

Il `create` comando include i parametri per l'aggiunta di dispositivi figlio e l'impostazione di un dispositivo padre al momento della creazione del dispositivo.

Altri comandi di identità del dispositivo, tra cui `add-children` , `list-children` e `remove-children` o `get-parent` e `set-parent` , consentono di gestire le relazioni padre/figlio per i dispositivi esistenti.

---

## <a name="prepare-certificates"></a>Preparare i certificati

Una catena di certificati coerente deve essere installata tra i dispositivi nella stessa gerarchia del gateway per stabilire una comunicazione sicura tra di essi. Ogni dispositivo della gerarchia, indipendentemente dal fatto che sia un dispositivo IoT Edge o un dispositivo foglia di un sacco, necessita di una copia dello stesso certificato CA radice. Ogni dispositivo IoT Edge nella gerarchia usa quindi il certificato CA radice come radice per il certificato della CA del dispositivo.

Con questa configurazione, ogni dispositivo IoT Edge a valle o il dispositivo foglia di un sacco può verificare l'identità del proprio padre verificando che il edgeHub a cui si connettano disponga di un certificato del server firmato dal certificato CA radice condiviso.

<!-- TODO: certificate graphic -->

Creare i certificati seguenti:

* Un **certificato CA radice** , ovvero il certificato condiviso superiore per tutti i dispositivi in una determinata gerarchia del gateway. Questo certificato è installato in tutti i dispositivi.
* Tutti i **certificati intermedi** che si desidera includere nella catena di certificati radice.
* Un **certificato CA del dispositivo** e la relativa **chiave privata** , generati dai certificati radice e intermedi. È necessario un certificato della CA univoco per ogni dispositivo IoT Edge nella gerarchia del gateway.

>[!NOTE]
>Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo.

È possibile usare un'autorità di certificazione autofirmata o acquistarne una da un'autorità di certificazione commerciale attendibile, ad esempio Baltimore, Verisign, DigiCert o GlobalSign.

Se non si dispone di certificati personalizzati da usare, è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md). Attenersi alla procedura descritta in questo articolo per creare un set di certificati radice e intermedi, quindi per creare IoT Edge certificati della CA del dispositivo per ogni dispositivo.

## <a name="configure-iot-edge-on-devices"></a>Configurare IoT Edge nei dispositivi

I passaggi per la configurazione di IoT Edge come gateway sono molto simili ai passaggi per la configurazione di IoT Edge come dispositivo downstream.

Per abilitare l'individuazione del gateway, ogni dispositivo IoT Edge Gateway deve essere configurato con un **nome host** che i dispositivi figlio utilizzeranno per trovarlo nella rete locale. Ogni dispositivo IoT Edge downstream deve essere configurato con una **parent_hostname** a cui connettersi. Se un singolo dispositivo IoT Edge è sia un dispositivo padre che un dispositivo figlio, sono necessari entrambi i parametri.

Per abilitare connessioni sicure, ogni dispositivo IoT Edge in uno scenario di gateway deve essere configurato con un certificato della CA del dispositivo univoco e una copia del certificato CA radice condiviso da tutti i dispositivi nella gerarchia del gateway.

Nel dispositivo dovrebbe essere già installato IoT Edge. In caso contrario, seguire i passaggi per [installare il runtime di Azure IOT Edge](how-to-install-iot-edge.md) e quindi effettuare il provisioning del dispositivo con [l'autenticazione con chiave simmetrica](how-to-manual-provision-symmetric-key.md) o con [l'autenticazione del certificato X. 509](how-to-manual-provision-x509.md).

I passaggi descritti in questa sezione fanno riferimento al certificato della **CA radice** e al **certificato della CA del dispositivo e alla chiave privata** descritti in precedenza in questo articolo. Se questi certificati sono stati creati in un altro dispositivo, è necessario che siano disponibili nel dispositivo. È possibile trasferire i file fisicamente, ad esempio con un'unità USB, con un servizio come [Azure Key Vault](../key-vault/general/overview.md)o con una funzione come la [copia protetta del file](https://www.ssh.com/ssh/scp/).

Usare la procedura seguente per configurare IoT Edge nel dispositivo.

In Linux assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati e le chiavi.

1. Installare il **certificato CA radice** in questo dispositivo IOT Edge.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Aggiornare l'archivio certificati.

   ```bash
   sudo update-ca-certificates
   ```

   Questo comando dovrebbe restituire l'aggiunta di un certificato a/etc/ssl/certs.

1. Aprire il file di configurazione del daemon di sicurezza IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione **Certificates** nel file config. yaml. Aggiornare i tre campi del certificato in modo che puntino ai certificati. Specificare i percorsi URI del file, che hanno il formato `file:///<path>/<filename>` .

   * **device_ca_cert** : percorso URI del file del certificato della CA del dispositivo univoco per questo dispositivo.
   * **device_ca_pk** : percorso URI del file della chiave privata della CA del dispositivo univoco per questo dispositivo.
   * **trusted_ca_certs** : percorso URI del file del certificato CA radice condiviso da tutti i dispositivi nella gerarchia del gateway.

1. Trovare il parametro **hostname** nel file config. yaml. Aggiornare il nome host in modo che sia il nome di dominio completo (FQDN) o l'indirizzo IP del dispositivo IoT Edge.

   Il valore di questo parametro è quello che i dispositivi downstream utilizzeranno per connettersi a questo gateway. Il nome host prende il nome del computer per impostazione predefinita, ma il nome di dominio completo o l'indirizzo IP è necessario per connettere i dispositivi downstream.

   Usare un nome host più breve di 64 caratteri, che corrisponde al limite di caratteri per un nome comune del certificato server.

   Essere coerenti con il modello di nome host in una gerarchia del gateway. Usare nomi di dominio completi o indirizzi IP, ma non entrambi.

1. **Se il dispositivo è un dispositivo figlio** , trovare il parametro **parent_hostname** . Aggiornare il campo **parent_hostname** in modo che corrisponda al nome di dominio completo o all'indirizzo IP del dispositivo padre, corrispondente a quello fornito come nome host nel file config. YAML del padre.

1. Anche se questa funzionalità è in anteprima pubblica, è necessario configurare il dispositivo IoT Edge per usare la versione di anteprima pubblica dell'agente IoT Edge all'avvio.

   Trovare la sezione YAML dell' **agente** e aggiornare il valore dell'immagine all'immagine di anteprima pubblica:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. Salvare ( `Ctrl+O` ) e chiudere ( `Ctrl+X` ) il file config. yaml.

1. Se sono stati usati altri certificati per IoT Edge prima, eliminare i file nelle due directory seguenti per assicurarsi che vengano applicati i nuovi certificati:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Riavviare il servizio IoT Edge per applicare le modifiche.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Verificare la presenza di eventuali errori nella configurazione.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Lo strumento di controllo IoT Edge usa un contenitore per eseguire parte del controllo di diagnostica. Se si vuole usare questo strumento nei dispositivi IoT Edge downstream, assicurarsi che possano accedere `mcr.microsoft.com/azureiotedge-diagnostics:latest` o avere l'immagine del contenitore nel registro contenitori privato.

## <a name="configure-runtime-modules-for-public-preview"></a>Configurare i moduli di runtime per l'anteprima pubblica

Anche se questa funzionalità è in anteprima pubblica, è necessario configurare il dispositivo IoT Edge per usare le versioni di anteprima pubblica dei moduli IoT Edge Runtime. La sezione precedente descrive i passaggi per la configurazione di edgeAgent all'avvio. È anche necessario configurare i moduli di runtime nelle distribuzioni per il dispositivo.

1. Configurare il modulo edgeHub per l'uso dell'immagine di anteprima pubblica: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` .

1. Configurare le variabili di ambiente seguenti per il modulo edgeHub:

   | Nome | Valore |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Configurare il modulo edgeAgent per l'uso dell'immagine di anteprima pubblica: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` .

## <a name="network-isolate-downstream-devices"></a>Isolamento rete di dispositivi downstream

I passaggi descritti in questo articolo configurano IoT Edge dispositivi come un gateway o un dispositivo downstream e creare una connessione trusted tra di essi. Il dispositivo gateway gestisce le interazioni tra il dispositivo downstream e l'hub Internet, inclusi l'autenticazione e il routing dei messaggi. I moduli distribuiti a downstream IoT Edge i dispositivi possono comunque creare connessioni personalizzate ai servizi cloud.

Alcune architetture di rete, come quelle che seguono lo standard ISA-95, cercano di ridurre al minimo il numero di connessioni Internet. In questi scenari è possibile configurare i dispositivi IoT Edge downstream senza connessione diretta a Internet. Oltre al routing delle comunicazioni dell'hub Internet tramite il dispositivo gateway, i dispositivi IoT Edge downstream possono basarsi sul dispositivo gateway per tutte le connessioni cloud.

Questa configurazione di rete richiede che solo il dispositivo IoT Edge nel livello superiore di una gerarchia del gateway abbia connessioni dirette al cloud. IoT Edge dispositivi nei livelli inferiori possono comunicare solo con il dispositivo padre o con i dispositivi figlio. I moduli speciali nei dispositivi gateway consentono questo scenario, tra cui:

* Il **modulo proxy API** è obbligatorio in qualsiasi IOT Edge Gateway con un altro dispositivo IOT Edge al di sotto di esso. Ciò significa che deve trovarsi a *ogni livello* di una gerarchia del gateway, ad eccezione del livello inferiore. Questo modulo usa un proxy inverso [nginx](https://nginx.org) per indirizzare i dati HTTP attraverso i livelli di rete su una singola porta. È altamente configurabile tramite il modulo gemello e le variabili di ambiente, quindi può essere adattato ai requisiti dello scenario del gateway.

* Il **modulo Docker Registry** può essere distribuito nel gateway IOT Edge al *livello superiore* di una gerarchia del gateway. Questo modulo è responsabile del recupero e della memorizzazione nella cache delle immagini del contenitore per conto di tutti i dispositivi IoT Edge nei livelli inferiori. L'alternativa alla distribuzione di questo modulo al livello superiore consiste nell'usare un registro di sistema locale o caricare manualmente le immagini del contenitore sui dispositivi e impostare i criteri di pull del modulo su **Never**.

* L' **archiviazione BLOB di Azure in IOT Edge** può essere distribuita nel gateway di IOT Edge al *livello superiore* di una gerarchia del gateway. Questo modulo è responsabile del caricamento di BLOB per conto di tutti i dispositivi IoT Edge nei livelli inferiori. La possibilità di caricare BLOB consente anche di ottenere funzionalità utili per la risoluzione dei problemi per IoT Edge dispositivi nei livelli inferiori, ad esempio il caricamento del log del modulo e il caricamento del bundle del supporto

### <a name="network-configuration"></a>Configurazione di rete

Per ogni dispositivo gateway nel livello superiore, gli operatori di rete devono:

* Specificare un indirizzo IP statico o un nome di dominio completo (FQDN).
* Autorizzare le comunicazioni in uscita da questo indirizzo IP al nome host dell'hub Azure Internet sulle porte 443 (HTTPS) e 5671 (AMQP).
* Autorizzare le comunicazioni in uscita da questo indirizzo IP al nome host Container Registry di Azure sulla porta 443 (HTTPS).

  Il modulo proxy API può gestire solo le connessioni a un registro contenitori alla volta. Si consiglia di disporre di tutte le immagini del contenitore, incluse le immagini pubbliche fornite da Microsoft Container Registry (mcr.microsoft.com), archiviate nel registro contenitori privato.

Per ogni dispositivo gateway in un livello inferiore, gli operatori di rete devono:

* Specificare un indirizzo IP statico.
* Autorizzare le comunicazioni in uscita da questo indirizzo IP all'indirizzo IP del gateway padre sulle porte 443 (HTTPS) e 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Distribuire i moduli ai dispositivi di livello superiore

Il dispositivo IoT Edge al livello superiore di una gerarchia del gateway ha un set di moduli necessari che devono essere distribuiti, oltre ai moduli del carico di lavoro che è possibile eseguire nel dispositivo.

Il modulo proxy API è stato progettato per essere personalizzato per la gestione dei più comuni scenari di gateway. Questo articolo fornisce ed esempio per configurare i moduli in una configurazione di base. Per informazioni ed esempi più dettagliati, vedere [configurare il modulo proxy API per lo scenario della gerarchia del gateway](how-to-configure-api-proxy-module.md) .

1. Nella [portale di Azure](https://portal.azure.com)passare all'hub Internet.
1. Selezionare **IOT Edge** dal menu di navigazione.
1. Selezionare il dispositivo di livello superiore che si sta configurando dall'elenco dei **dispositivi IOT Edge**.
1. Selezionare **Imposta moduli**.
1. Nella sezione **moduli IOT Edge** scegliere **Aggiungi** , quindi **modulo Marketplace**.
1. Cercare e selezionare il modulo **proxy API IOT Edge** .
1. Selezionare il nome del modulo proxy API dall'elenco dei moduli distribuiti e aggiornare le impostazioni del modulo seguenti:
   1. Nella scheda **variabili di ambiente** , aggiornare il valore di **NGINX_DEFAULT_PORT** a `443` .
   1. Nella scheda **contenitore crea opzioni** aggiornare i binding della porta alla porta di riferimento 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Queste modifiche consentono di configurare il modulo proxy API per l'ascolto sulla porta 443. Per evitare conflitti di binding della porta, è necessario configurare il modulo edgeHub in modo che non sia in ascolto sulla porta 443. Al contrario, il modulo proxy API instraderà il traffico edgeHub sulla porta 443.

1. Selezionare le **impostazioni di runtime** e trovare le opzioni di creazione del modulo edgeHub. Eliminare il binding della porta per la porta 443, lasciando le associazioni per le porte 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Selezionare **Save (Salva** ) per salvare le modifiche apportate alle impostazioni di Runtime.
1. Selezionare di nuovo **Aggiungi** , quindi scegliere **IOT Edge modulo**.
1. Specificare i valori seguenti per aggiungere il modulo Docker Registry alla distribuzione:
   1. **Nome del modulo IOT Edge** : `registry`
   1. Nella scheda **Impostazioni modulo** , **URI immagine** : `registry:latest`
   1. Nella scheda **variabili di ambiente** aggiungere le variabili di ambiente seguenti:

      * **Nome** : `REGISTRY_PROXY_REMOTEURL` **valore** : URL del registro contenitori a cui si vuole associare questo modulo del registro di sistema. Ad esempio, `https://myregistry.azurecr`

        Il modulo del registro di sistema può essere mappato a un solo registro contenitori, quindi è consigliabile avere tutte le immagini del contenitore in un unico registro contenitori privato.

      * **Nome** : `REGISTRY_PROXY_USERNAME` **valore** : nomeutente per l'autenticazione nel registro contenitori.

      * **Nome** : `REGISTRY_PROXY_PASSWORD` **valore** : password per l'autenticazione nel registro contenitori.

   1. Nella scheda **Crea opzioni del contenitore** incollare:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Selezionare **Aggiungi** per aggiungere il modulo alla distribuzione.
1. Selezionare **Avanti: Route** per andare al passaggio successivo.
1. Per abilitare i messaggi da dispositivo a cloud dai dispositivi downstream per raggiungere l'hub Internet, includere una route che passa tutti i messaggi all'hub Internet. Ad esempio:
    1. **Nome** : `Route`
    1. **Valore** : `FROM /messages/* INTO $upstream`
1. Selezionare **Verifica + crea** per andare al passaggio finale.
1. Selezionare **Crea** per eseguire la distribuzione nel dispositivo.

### <a name="deploy-modules-to-lower-layer-devices"></a>Distribuire moduli a dispositivi di livello inferiore

IoT Edge dispositivi nei livelli inferiori di una gerarchia del gateway hanno un modulo obbligatorio che deve essere distribuito, oltre ai moduli del carico di lavoro che è possibile eseguire nel dispositivo.

#### <a name="route-container-image-pulls"></a>Pull dell'immagine del contenitore di route

Prima di esaminare il modulo proxy richiesto per IoT Edge dispositivi nelle gerarchie del gateway, è importante comprendere come IoT Edge i dispositivi nei livelli inferiori ottengano le immagini dei moduli.

Se i dispositivi di livello inferiore non possono connettersi al cloud, ma si vuole eseguire il pull delle immagini del modulo come di consueto, è necessario configurare il dispositivo di livello superiore della gerarchia del gateway per gestire queste richieste. Il dispositivo di livello superiore deve eseguire un modulo Docker **Registry** mappato al registro contenitori. Configurare quindi il modulo proxy API per instradare le richieste del contenitore. Questi dettagli sono descritti nelle sezioni precedenti di questo articolo. In questa configurazione, i dispositivi di livello inferiore non devono puntare a registri contenitori cloud, ma al registro di sistema in esecuzione nel livello superiore.

Ad esempio, anziché chiamare `mcr.microsoft.com/azureiotedge-api-proxy:latest` , i dispositivi di livello inferiore devono chiamare `$upstream:443/azureiotedge-api-proxy:latest` .

Il parametro **$upstream** punta all'elemento padre di un dispositivo di livello inferiore, in modo che la richiesta venga indirizzata attraverso tutti i livelli fino a raggiungere il livello superiore con un ambiente proxy che indirizza le richieste del contenitore al modulo del registro di sistema. La `:443` porta in questo esempio deve essere sostituita con qualsiasi porta su cui è in ascolto il modulo proxy API sul dispositivo padre.

Il modulo proxy API può essere indirizzato solo a un modulo del registro di sistema e ogni modulo del registro di sistema può essere mappato solo a un registro contenitori. Pertanto, le immagini che i dispositivi di livello inferiore devono eseguire il pull devono essere archiviate in un unico registro contenitori.

Se non si vuole che i dispositivi di livello inferiore facciano richieste pull del modulo tramite una gerarchia del gateway, un'altra opzione consiste nel gestire una soluzione del registro di sistema locale. In alternativa, eseguire il push delle immagini del modulo sui dispositivi prima di creare le distribuzioni, quindi impostare **imagePullPolicy** su **Never**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Bootstrap dell'agente di IoT Edge

L'agente di IoT Edge è il primo componente di runtime da avviare in qualsiasi dispositivo IoT Edge. È necessario assicurarsi che tutti i dispositivi IoT Edge downstream possano accedere all'immagine del modulo edgeAgent all'avvio, quindi possono accedere alle distribuzioni e avviare le altre immagini del modulo.

Quando si accede al file config. YAML in un dispositivo IoT Edge per fornire le informazioni di autenticazione, i certificati e il nome host padre, aggiornare anche l'immagine del contenitore edgeAgent.

Se il dispositivo gateway di primo livello è configurato per gestire le richieste di immagini del contenitore, sostituire `mcr.microsoft.com` con il nome host padre e la porta di ascolto del proxy API. Nel manifesto di distribuzione è possibile usare `$upstream` come collegamento, ma ciò richiede che il modulo edgeHub gestisca il routing e che il modulo non sia stato avviato a questo punto. Ad esempio:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc1"
    auth: {}
```

Se si usa un registro contenitori locale o si specificano le immagini del contenitore manualmente nel dispositivo, aggiornare di conseguenza il file config. yaml.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Configurare il runtime e distribuire il modulo proxy

Il **modulo proxy API** è necessario per il routing di tutte le comunicazioni tra il cloud e qualsiasi dispositivo IOT Edge downstream. Questo modulo non è necessario per un dispositivo IoT Edge nel livello inferiore della gerarchia, senza dispositivi IoT Edge downstream.

Il modulo proxy API è stato progettato per essere personalizzato per la gestione dei più comuni scenari di gateway. Questo articolo illustra brevemente la procedura per configurare i moduli in una configurazione di base. Per informazioni ed esempi più dettagliati, vedere [configurare il modulo proxy API per lo scenario della gerarchia del gateway](how-to-configure-api-proxy-module.md) .

1. Nella [portale di Azure](https://portal.azure.com)passare all'hub Internet.
1. Selezionare **IOT Edge** dal menu di navigazione.
1. Selezionare il dispositivo di livello inferiore che si sta configurando dall'elenco dei **dispositivi IOT Edge**.
1. Selezionare **Imposta moduli**.
1. Nella sezione **moduli IOT Edge** scegliere **Aggiungi** , quindi **modulo Marketplace**.
1. Cercare e selezionare il modulo **proxy API IOT Edge** .
1. Selezionare il nome del modulo proxy API dall'elenco dei moduli distribuiti e aggiornare le impostazioni del modulo seguenti:
   1. Nella scheda **Impostazioni modulo** aggiornare il valore dell'URI dell' **immagine**. Sostituire `mcr.microsoft.com` con `$upstream:443`.
   1. Nella scheda **variabili di ambiente** , aggiornare il valore di **NGINX_DEFAULT_PORT** a `443` .
   1. Nella scheda **contenitore crea opzioni** aggiornare i binding della porta alla porta di riferimento 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Queste modifiche consentono di configurare il modulo proxy API per l'ascolto sulla porta 443. Per evitare conflitti di binding della porta, è necessario configurare il modulo edgeHub in modo che non sia in ascolto sulla porta 443. Al contrario, il modulo proxy API instraderà il traffico edgeHub sulla porta 443.

1. Selezionare **le impostazioni di runtime**.
1. Aggiornare le impostazioni del modulo edgeHub:

   1. Nel campo **Image** sostituire `mcr.microsoft.com` con `$upstream:443` .
   1. Nel campo **Crea opzioni** eliminare il binding di porta per la porta 443, lasciando i binding per le porte 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Aggiornare le impostazioni del modulo edgeAgent:
   1. Nel campo **Image** sostituire `mcr.microsoft.com` con `$upstream:443` .

1. Selezionare **Save (Salva** ) per salvare le modifiche apportate alle impostazioni di Runtime.
1. Selezionare **Avanti: Route** per andare al passaggio successivo.
1. Per abilitare i messaggi da dispositivo a cloud dai dispositivi downstream per raggiungere l'hub Internet, includere una route che passa tutti i messaggi a `$upstream` . Il parametro upstream punta al dispositivo padre nel caso di dispositivi di livello inferiore. Ad esempio:
    1. **Nome** : `Route`
    1. **Valore** : `FROM /messages/* INTO $upstream`
1. Selezionare **Verifica + crea** per andare al passaggio finale.
1. Selezionare **Crea** per eseguire la distribuzione nel dispositivo.

## <a name="next-steps"></a>Passaggi successivi

[Come usare un dispositivo Azure IoT Edge come gateway](iot-edge-as-gateway.md)

[Configurare il modulo proxy API per lo scenario della gerarchia del gateway](how-to-configure-api-proxy-module.md)