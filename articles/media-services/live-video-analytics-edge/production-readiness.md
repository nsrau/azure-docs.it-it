---
title: Conformità e procedure consigliate per la produzione-Azure
description: Questo articolo fornisce indicazioni su come configurare e distribuire l'analisi video in tempo reale sul modulo IoT Edge in ambienti di produzione.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a5a2ff78d456d4423facdf5f3533ee94bc25bfc4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260374"
---
# <a name="production-readiness-and-best-practices"></a>Idoneità e procedure consigliate per la produzione

Questo articolo fornisce indicazioni su come configurare e distribuire l'analisi video in tempo reale sul modulo IoT Edge in ambienti di produzione. Per preparare la soluzione di IoT Edge, vedere anche [preparare la distribuzione dell'IoT Edge soluzione in produzione](https://docs.microsoft.com/azure/iot-edge/production-checklist) . 

> [!NOTE]
> È necessario consultare i reparti IT delle organizzazioni per gli aspetti correlati alla sicurezza.

## <a name="running-the-module-as-a-local-user"></a>Esecuzione del modulo come utente locale

Per impostazione predefinita, quando si distribuisce l'analisi video in tempo reale sul modulo IoT Edge in un dispositivo perimetrale, questa viene eseguita con privilegi elevati. Quando si esegue questa operazione, se si controllano i log nel modulo ( `sudo iotedge logs {name-of-module}` ), verrà visualizzato quanto segue:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

Le sezioni seguenti illustrano come è possibile risolvere l'avviso sopra riportato.

### <a name="creating-and-using-a-local-user-account"></a>Creazione e utilizzo di un account utente locale

È possibile ed eseguire l'analisi video in tempo reale sul modulo IoT Edge in produzione usando un account con il minor numero di privilegi possibile. I comandi seguenti, ad esempio, mostrano come è possibile creare un account utente locale in una VM Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Successivamente, nel manifesto di distribuzione è possibile impostare le variabili di ambiente LOCAL_USER_ID e LOCAL_GROUP_ID su tale utente e gruppo non radice:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Concessione di autorizzazioni per l'archiviazione del dispositivo

Il modulo di analisi video live in IoT Edge richiede la possibilità di scrivere file nel file system locale quando:

* Usare una proprietà del modulo gemello [[applicationDataDirectory](module-twin-configuration-schema.md#module-twin-properties)], in cui è necessario specificare una directory nella file system locale per archiviare i dati di configurazione.
* Usando un grafico multimediale per registrare video nel cloud, il modulo richiede l'uso di una directory sul dispositivo perimetrale come cache. per ulteriori informazioni, vedere l'articolo relativo alla [registrazione video continua](continuous-video-recording-concept.md) .
* [Registrazione in un file locale, in](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)cui è necessario specificare un percorso di file per il video registrato.

Se si prevede di usare uno dei precedenti, è necessario assicurarsi che l'account utente precedente abbia accesso alla directory pertinente. Si consideri ad esempio applicationDataDirectory. È possibile creare una directory sul dispositivo perimetrale e collegare l'archiviazione del dispositivo all'archiviazione dei moduli. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Successivamente, nel manifesto di distribuzione delle opzioni di creazione per il modulo perimetrale è possibile aggiungere un'impostazione di binding che esegue il mapping della directory ("var/local/MediaServices/") precedente a una directory nel modulo (ad esempio "/var/lib/azuremediaservices/"). E si userà la seconda directory come valore per applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Se si osservano i grafici multimediali di esempio per la Guida introduttiva e le esercitazioni, ad esempio la [registrazione video continua](continuous-video-recording-tutorial.md), si noterà che la directory della cache multimediale (localMediaCachePath) usa una sottodirectory in applicationDataDirectory. Si tratta dell'approccio consigliato, perché la cache contiene dati temporanei.

### <a name="naming-video-assets-or-files"></a>Denominazione di asset o file video

I grafici multimediali consentono di creare asset nel cloud o in file MP4 sul perimetro. Le risorse multimediali possono essere generate dalla [registrazione video continua](continuous-video-recording-tutorial.md) o dalla [registrazione video basata su eventi](event-based-video-recording-tutorial.md). Sebbene questi asset e file possano essere denominati come si desidera, la struttura di denominazione consigliata per asset di contenuti multimediali basati su registrazione video continua è " &lt; anytext &gt; -$ {System. GraphTopologyName}-$ {System. GraphInstanceName}". Ad esempio, è possibile impostare assetNamePattern sul sink di asset nel modo seguente:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Per gli asset generati dalla registrazione video basata su eventi, il modello di denominazione consigliato è " &lt; anytext &gt; -$ {System. DateTime}". La variabile di sistema garantisce che gli asset non vengano sovrascritti se gli eventi si verificano nello stesso momento. Ad esempio, è possibile impostare assetNamePattern sul sink di asset nel modo seguente:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Se si eseguono più istanze dello stesso grafico, è possibile usare il nome della topologia del grafico e il nome dell'istanza per distinguerli. Ad esempio, è possibile impostare assetNamePattern sul sink di asset nel modo seguente:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

Per i clip video MP4 generati dalla registrazione video basati su eventi sul perimetro, il modello di denominazione consigliato deve includere DateTime e per più istanze dello stesso grafico è consigliabile usare le variabili di sistema GraphTopologyName e GraphInstanceName. Ad esempio, è possibile impostare filePathPattern sul sink di file come indicato di seguito: 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Oppure 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>Pulizia della macchina virtuale

La macchina virtuale Linux usata come dispositivo perimetrale può smettere di rispondere se non viene gestita periodicamente. È essenziale evitare la pulizia delle cache, eliminare i pacchetti non necessari e rimuovere anche i contenitori non usati dalla macchina virtuale. Per eseguire questa operazione, è disponibile un set di comandi consigliati, che è possibile usare nella macchina virtuale perimetrale.

1. `sudo apt-get clean`

    Il comando apt-get clean cancella il repository locale dei file del pacchetto recuperato che sono rimasti in/var/cache. Le directory che puliscono sono/var/cache/apt/archives/e/var/cache/apt/archives/partial/. Gli unici file che lascia in/var/cache/apt/archives sono il file di blocco e la sottodirectory parziale. Il comando apt-get clean viene in genere usato per cancellare lo spazio su disco in base alle esigenze, in genere come parte della manutenzione pianificata regolarmente. Per ulteriori informazioni, vedere la pagina relativa alla [pulizia con apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    L'opzione apt-get autoclean, ad esempio apt-get clean, cancella il repository locale dei file del pacchetto recuperato, ma rimuove solo i file che non possono più essere scaricati e non sono utili. Consente di evitare che la cache cresca troppo grande.
1. `sudo apt-get autoremove1`

    L'opzione di rimozione automatica rimuove i pacchetti che sono stati installati automaticamente perché è necessario un altro pacchetto, ma, con gli altri pacchetti rimossi, non sono più necessari.
1. `sudo docker image ls`: Fornisce un elenco di immagini Docker nel sistema perimetrale
1. `sudo docker system prune `

    Docker usa un approccio conservativo per pulire gli oggetti non usati (spesso definiti "Garbage Collection"), ad esempio immagini, contenitori, volumi e reti. questi oggetti in genere non vengono rimossi, a meno che non si chieda esplicitamente a Docker di eseguire questa operazione. Questo può causare l'uso di spazio su disco aggiuntivo in docker. Per ogni tipo di oggetto, Docker fornisce un comando di eliminazione. Inoltre, è possibile usare l'eliminazione del sistema Docker per pulire contemporaneamente più tipi di oggetti. Per altre informazioni, vedere l'articolo relativo alla [potatura degli oggetti Docker inutilizzati](https://docs.docker.com/config/pruning/).
1. `sudo docker rmi REPOSITORY:TAG`

    Quando gli aggiornamenti vengono eseguiti nel modulo perimetrale, è possibile che in Docker siano ancora presenti versioni precedenti del modulo perimetrale. In tal caso, è consigliabile usare il comando Docker RMI per rimuovere immagini specifiche identificate dal tag della versione dell'immagine.

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Introduzione: analisi di video live in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
