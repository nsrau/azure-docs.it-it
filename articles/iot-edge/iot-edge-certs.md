---
title: Informazioni sui certificati di Azure IoT Edge | Microsoft Docs
description: Informazioni sui certificati di Azure IoT Edge e sulle relative modalità di utilizzo.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ff85f2c9ef708c7cbac9be4933541f063a6c95b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568591"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Dettagli di utilizzo dei certificati di Azure IoT Edge

I certificati di IoT Edge vengono usati per i moduli e dispositivi IoT a valle per verificare l'identità e la legittimità del modulo di runtime [Hub Edge](iot-edge-runtime.md#iot-edge-hub) a cui si connettono. Queste verifiche consentono di attivare una connessione protetta TLS tra il runtime, i moduli e i dispositivi IoT. Esattamente come hub IoT, IoT Edge richiede la presenza di una connessione protetta e crittografata con i dispositivi IoT a valle e i moduli di IoT Edge. Per stabilire una connessione TLS sicura, il modulo Hub Edge presenta una catena di certificati del server ai client che si devono connettere affinché questi possano verificarne l'identità.

Per chiarire eventuali dubbi sull'utilizzo dei certificati da parte di IoT Edge, questo articolo spiega in che modo i certificati di IoT Edge funzionano in scenari su larga scala, oltre che in scenari di sviluppo e test. I concetti per Linux e Windows sono gli stessi, anche se gli script sono diversi (Powershell e Bash).

## <a name="iot-edge-certificates"></a>Certificati di IoT Edge

Nella maggior parte dei casi, i produttori di un dispositivo Edge sono entità indipendenti dagli utenti finali. In alcuni casi, l'unica relazione esistente tra produttore e operatore è l'acquisto di un dispositivo Edge disponibile in commercio. In altri casi, il produttore fabbrica il dispositivo Edge sotto contratto per conto dell'operatore. La struttura del certificato di IoT Edge tenta di prendere in considerazione entrambi gli scenari.

Nella figura seguente viene illustrato l'uso dei certificati di IoT Edge. Tra il certificato CA radice e il certificato CA del dispositivo potrebbero essere presenti nessuno, uno o molti certificati di firma intermedi. Il numero delle entità coinvolte modifica lo scenario. Di seguito è illustrato un caso.

![Diagramma tipico delle relazioni tra certificati](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autorità di certificazione

L'autorità di certificazione, o CA, è un'entità che emette i certificati digitali. Un'autorità di certificazione funziona come una terza parte attendibile tra il proprietario e il destinatario del certificato. Un certificato digitale fornisce un'attestazione della proprietà di una chiave pubblica al destinatario del certificato. La catena di certificati attendibili funziona generando inizialmente un certificato radice, che rappresenta la base per la relazione di trust di tutti i certificati rilasciati dall'autorità. Successivamente, il proprietario può usare il certificato radice per emettere altri certificati intermedi (certificati "foglia").

### <a name="root-ca-certificate"></a>Certificato CA radice

Un certificato CA radice è la base per l'attendibilità dell'intero processo. Negli scenari di produzione, in genere è presente un certificato CA acquistato da un'autorità di certificazione commerciale attendibile, ad esempio Baltimore Verisign, DigiCert, e così via. Chi ha il controllo completo dei dispositivi che si connettono ai dispositivi Edge, può usare un'autorità di certificazione di livello aziendale. In entrambi i casi, l'intera catena di certificati di Hub Edge fanno capo a questo, pertanto i dispositivi IoT foglia devono considerare attendibile il certificato radice. È possibile archiviare il certificato CA radice nell'archivio dell'autorità per i certificati radice attendibili o specificare i dettagli del certificato CA radice nel codice quando il client IoT si connette a IoT Edge.

### <a name="intermediate-certificates"></a>Certificati intermedi

I certificati CA radice sono un modo tipico per rendere sicuri i dispositivi e raramente vengono usati direttamente, principalmente a causa del rischio di perdita o di esposizione. In genere, il certificato CA radice crea e firma digitalmente uno o più certificati CA intermedi. Può trattarsi di un solo certificato o di una catena di certificati intermedi. Gli scenari che richiedono una catena di certificati intermedi includono:

* Una gerarchia di reparti all'interno del sistema di un produttore.

* Più aziende coinvolte in serie nella produzione di un dispositivo.

* Un cliente acquista un certificato CA radice e ne deriva un certificato di firma con cui il produttore firma i dispositivi prodotti per conto del cliente.

In ogni caso, il produttore usa un certificato CA intermedio alla fine della catena per firmare il certificato CA del dispositivo finale. In genere, questi certificati intermedi sono custoditi con cura presso l'impianto di produzione. Il loro utilizzo è subordinato a rigorosi processi sia fisici che elettronici.

### <a name="device-ca-certificate"></a>Certificato CA del dispositivo

Il certificato CA del dispositivo è generato e firmato dal certificato intermedio finale del processo. Questo certificato viene installato nel dispositivo Edge stesso, preferibilmente in un archivio protetto, ad esempio un modulo di protezione hardware. Inoltre, un certificato CA del dispositivo identifica in modo univoco un dispositivo IoT Edge. Per IoT Edge, il certificato CA del dispositivo è in grado di emettere altri certificati. Ad esempio, il certificato CA del dispositivo emette certificati foglia del dispositivo che vengono usati per autenticare i dispositivi per [il servizio Azure IoT Device Provisioning](..\iot-dps\about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Certificato CA del carico di lavoro di IoT Edge

Questo certificato, il primo sul lato operatore del processo, viene generato dal [Gestore sicurezza di IoT Edge](iot-edge-security-manager.md) al primo avvio di IoT Edge. Questo certificato è generato e firmato dal "certificato CA del dispositivo". Questo certificato, ovvero un altro certificato di firma intermedio, viene usato per generare e firmare altri certificati usati dal runtime di IoT Edge. Al momento, si tratta principalmente del certificato del server Hub Edge descritto nella sezione seguente, ma in futuro potrebbe trattarsi di altri certificati per autenticare i componenti di IoT Edge.

### <a name="edge-hub-server-certificate"></a>Certificato del server Hub Edge

Il certificato del server Hub Edge è il certificato vero e proprio presentato ai dispositivi foglia e ai moduli che si occupano della verifica delle identità durante la creazione delle connessioni TLS richieste da IoT Edge. Questo certificato presenta l'intera catena di certificati di firma usati per generarlo fino al certificato CA radice, che il dispositivo foglia IoT deve considerare attendibile. Quando è generato dal Gestore sicurezza di IoT Edge, il nome comune di questo certificato Hub Edge viene impostato sul nome specificato per la proprietà "hostname" nel file config.yaml dopo la conversione in lettere minuscole. Questa è una comune causa di confusione in IoT Edge.

## <a name="production-implications"></a>Implicazioni di produzione

Potrebbe essere ragionevole chiedersi "Perché IoT Edge ha bisogno del certificato CA del carico di lavoro aggiuntivo? Non potrebbe usare il certificato CA del dispositivo per generare direttamente il certificato del server Hub Edge? ". Tecnicamente, potrebbe. Tuttavia, lo scopo di questo certificato di "carico di lavoro" intermedio è quello di separare i compiti tra il produttore del dispositivo e l'operatore del dispositivo. Si immagini uno scenario in cui un dispositivo IoT Edge è venduto o trasferito da un cliente a un altro. È opportuno che il certificato CA del dispositivo fornito dal produttore non venga modificato. Tuttavia, i certificati del "carico di lavoro" specifici del dispositivo devono essere cancellati e ricreati per il nuovo impiego.

Dal momento che i processi del produttore e dell'operatore sono separati, esistono alcune implicazioni da prendere in considerazione in merito all'implementazione di IoT Edge per i dispositivi di produzione.

* Per qualsiasi processo basato su certificato, il certificato CA radice e tutti i certificati CA intermedi devono essere protetti e monitorati durante l'intero processo di implementazione di un dispositivo IoT Edge. Il produttore del dispositivo IoT Edge deve predisporre processi rigorosi per l'archiviazione e l'utilizzo dei certificati intermedi. Inoltre, il certificato CA del dispositivo deve essere conservato in un archivio sicuro nel dispositivo stesso, preferibilmente in un modulo di protezione hardware.

* Poiché il certificato del server Hub Edge verrà presentato da Hub Edge ai dispositivi client e ai moduli con cui deve connettersi, il nome comune del certificato CA del dispositivo **non può** coincidere al valore "hostname" che verrà usato nel file config.yaml nel dispositivo IoT Edge. Il nome usato dai client per connettersi a IoT Edge, ad esempio tramite il parametro GatewayHostName della stringa di connessione o il comando CONNECT in MQTT, **non può essere** lo stesso nome comune utilizzato nel certificato CA del dispositivo. Questo perché Hub Edge presenta l'intera catena di certificati per la verifica da parte dei client. Se il certificato del server Hub Edge e il certificato CA del dispositivo condividono lo stesso nome comune, si entra in un ciclo continuo di verifica che invalida il certificato.

* Poiché viene usato dal daemon di sicurezza di IoT Edge per generare i certificati IoT Edge finali, il certificato CA del dispositivo deve essere un certificato di firma, cioè deve avere funzionalità di firma del certificato. Per trasformarlo in un certificato CA di firma, è sufficiente aggiungere la stringa "V3 Basic constraints CA:True" al certificato del dispositivo CA per impostare automaticamente le proprietà di utilizzo della chiave necessarie.

>[!Tip]
> Se IoT Edge è già stato configurato come gateway trasparente in uno scenario di sviluppo/test con i cosiddetti "script rapidi", descritti nella sezione successiva, e per la creazione del certificato CA del dispositivo è stato utilizzato lo stesso nome host usato in config.yaml, è lecito chiedersi perché l'operazione abbia funzionato. Allo scopo di semplificare l'esperienza di sviluppo, negli script rapidi viene aggiunta la stringa ".ca"" alla fine del nome passato allo script. Quindi, ad esempio, se è stato usato "mygateway" sia per il nome del dispositivo nello script che per il nome host in config.yaml, il primo sarà diventato mygateway.ca prima di essere utilizzato come nome comune del certificato CA del dispositivo.

## <a name="devtest-implications"></a>Implicazioni di sviluppo/test

Per facilitare la creazione di scenari di sviluppo e test, Microsoft mette a disposizione un set di [script rapidi](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) per la generazione di certificati non di produzione adatti per IoT Edge nello scenario gateway trasparente. Per esempi del funzionamento degli script, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).

Questi script generano certificati che ricalcano la struttura della catena di certificati descritta in questo articolo. I comandi seguenti generano il "certificato CA radice" e un singolo "certificato CA intermedio".

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Analogamente, questi comandi generano il "certificato CA del dispositivo".

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* Il **\<nome del dispositivo gateway\>** passato negli script **non deve** coincidere con il parametro "hostname" di config.yaml. Come accennato, gli script consentono di evitare eventuali problemi aggiungendo la stringa ".ca" al **\<nome del dispositivo gateway\>** per evitare un conflitto di nomi nel caso in cui l'utente configuri Edge con lo stesso nome per entrambi. Tuttavia, è buona norma evitare di usare lo stesso nome.

>[!Tip]
> Per connettere le applicazioni e i dispositivi "foglia" IoT che usano l'SDK per dispositivi IoT tramite IoT Edge, è necessario aggiungere il parametro facoltativo GatewayHostName alla fine della stringa di connessione del dispositivo. Il certificato del server Hub Edge che viene generato si basa su una versione in lettere minuscole del nome host di config.yaml, pertanto, per far coincidere i nomi e fare in modo che la verifica dei certificati TLS abbia esito positivo, immettere il parametro GatewayHostName in lettere minuscole.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Esempio di gerarchia dei certificati di IoT Edge

Per illustrare questo percorso dei certificati, viene proposto lo screenshot seguente preso da un dispositivo IoT Edge in esecuzione configurato come gateway trasparente. OpenSSL viene utilizzato per connettersi a Hub Edge, convalidare ed eseguire il dump dei certificati.

![Screenshot della gerarchia di certificati a ogni livello](./media/iot-edge-certs/iotedge-cert-chain.png)

La gerarchia di profondità dei certificati rappresentata nello screenshot è la seguente:

| Certificato CA radice         | Solo test certificato CA di hub IoT di Azure                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificato CA intermedio | Solo test certificato intermedio di hub IoT di Azure                                                                 |
| Certificato CA del dispositivo       | iotgateway.ca ("iotgateway" è stato passato come < nome host gateway > agli script rapidi)      |
| Certificato CA carico di lavoro     | iotedge workload ca                                                                                       |
| Certificato del server Hub Edge | iotedgegw.local (coincide con 'hostname' di config.yaml)                                                |

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)

[Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)