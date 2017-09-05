---
title: Accelerazione sito dinamico tramite la rete CDN di Azure
description: Approfondimento dell'accelerazione sito dinamico
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Accelerazione sito dinamico tramite la rete CDN di Azure

A causa dell'elevata diffusione di social media, e-commerce e Web iper-personalizzato, una percentuale sempre maggiore di contenuti resa disponibile agli utenti finali viene generata in tempo reale. Gli utenti si aspettano esperienze Web rapide, affidabili e personalizzate, indipendentemente da browser, posizione, dispositivo o rete. Tuttavia, le innovazioni che rendono queste esperienze così coinvolgenti rallentano anche i download delle pagine e pongono a rischio la qualità dell'esperienza utente. 

Le funzionalità standard della rete CDN includono la possibilità di memorizzare nella cache file in posizioni più vicine agli utenti finali per accelerare la distribuzione di file statici. Tuttavia, con le applicazioni Web dinamiche la memorizzazione nella cache di tali contenuti in posizioni periferiche non è possibile, in quanto il server genera il contenuto in risposta al comportamento degli utenti. È più difficile accelerare la distribuzione di tali contenuti rispetto alla memorizzazione nella cache perimetrale tradizionale e a questo scopo è necessaria una soluzione end-to-end in grado di ottimizzare ogni elemento insieme all'intero percorso dei dati, dalle fasi iniziali alla distribuzione. La funzionalità Accelerazione sito dinamico della rete CDN di Azure migliora in modo misurabile le prestazioni delle pagine Web con contenuto dinamico.

La rete CDN di Azure con tecnologie Akamai e Verizon offre l'ottimizzazione basata su Accelerazione sito dinamico tramite il menu **Ottimizzato per** durante la creazione degli endpoint.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configurazione di un endpoint di rete CDN per accelerare la distribuzione di file dinamici

È possibile configurare l'endpoint di rete CDN in modo da ottimizzare la distribuzione di file dinamici tramite il portale di Azure selezionando l'opzione **Accelerazione sito dinamico** nella casella di selezione delle proprietà **Ottimizzato per** durante la creazione dell'endpoint. È anche possibile usare le API REST o qualsiasi SDK client per eseguire le stesse operazioni a livello di codice. 

### <a name="probe-path"></a>Percorso probe
Il percorso probe è una caratteristica specifica della funzionalità Accelerazione sito dinamico e un percorso probe valido è necessario ai fini della creazione. La funzionalità Accelerazione sito dinamico usa un piccolo file di *percorso probe*, incluso nell'origine per ottimizzare le configurazioni di routing di rete per la rete CDN. È possibile scaricare e caricare il file di esempio nel proprio sito oppure usare un asset esistente nell'origine che sia all'incirca di 10 KB per il percorso probe, se l'asset esiste già.

> [!Note]
> Alla funzionalità Accelerazione sito dinamico si applicano costi aggiuntivi. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cdn/).

Gli screenshot seguenti mostrano il processo tramite il portale di Azure.
 
![Aggiunta di un nuovo endpoint di rete CDN](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Figura 1: Aggiunta di un nuovo endpoint di rete CDN dal profilo di rete CDN*
 
![Creazione di un nuovo endpoint di rete CDN con Accelerazione sito dinamico](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Figura 2: Creazione di un endpoint di rete CDN tramite la selezione dell'ottimizzazione basata su Accelerazione sito dinamico*

Una volta creato l'endpoint di rete CDN, questo applica le ottimizzazioni basate su Accelerazione sito dinamico per tutti i file che soddisfano determinati criteri. La sezione seguente descrive in dettaglio l'ottimizzazione basata su Accelerazione sito dinamico.

## <a name="dsa-optimization-using-azure-cdn"></a>Ottimizzazione basata su Accelerazione sito dinamico tramite la rete CDN di Azure

La funzionalità Accelerazione sito dinamico nella rete CDN di Azure accelera la distribuzione di asset dinamici tramite le tecniche seguenti:

-   Ottimizzazione delle route
-   Ottimizzazioni del protocollo TCP
-   Prelettura degli oggetti (solo Akamai)
-   Compressione di immagini mobile (solo Akamai)

### <a name="route-optimization"></a>Ottimizzazione delle route

L'ottimizzazione delle route è importante perché Internet è un ambiente dinamico, in cui le interruzioni temporanee e di traffico influiscono costantemente sulla topologia di rete. Border Gateway Protocol (BGP) è il protocollo di routing di Internet, ma possono essere disponibili route più veloci tramite server POP (Point of Presence) intermediari. 

L'ottimizzazione delle route sceglie il percorso ottimale verso l'origine, in modo che un sito sia costantemente accessibile e il contenuto dinamico venga distribuito agli utenti finali tramite la route più veloce e affidabile possibile. 

La rete Akamai usa alcune tecniche per raccogliere dati in tempo reale e confrontare i vari percorsi nei diversi nodi nel server Akamai, insieme alla route BGP predefinita nella rete Internet aperta, per determinare la route più veloce tra l'origine e il server perimetrale della rete CDN. Queste tecniche permettono di evitare lunghe route e punti di congestione in Internet. 

Analogamente, la rete Verizon usa una combinazione di DNS Anycast, server POP di supporto a capacità elevata e controlli di integrità per determinare i gateway migliori per instradare al meglio i dati dal client all'origine.
 
Di conseguenza, i contenuti completamente dinamici e transazionali vengono distribuiti in modo più rapido e affidabile agli utenti finali, anche quando non sono memorizzabili nella cache. 

### <a name="tcp-optimizations"></a>Ottimizzazioni del protocollo TCP

Transmission Control Protocol (TCP) è il protocollo standard della suite di protocolli Internet e viene usato per distribuire informazioni tra applicazioni in una rete IP.  Per impostazione predefinita, sono necessarie diverse richieste nelle due direzioni per configurare una connessione TCP e vengono applicati limiti per evitare congestioni di rete, che producono inefficienze su larga scala. La rete CDN di Azure con tecnologia Akamai gestisce tutti questi aspetti tramite ottimizzazioni in tre aree: 

 - Eliminazione della lentezza di avvio
 - Uso di connessioni persistenti
 - Ottimizzazione dei parametri dei pacchetti TCP (solo Akamai)

#### <a name="eliminating-slow-start"></a>Eliminazione della lentezza di avvio

La *lentezza di avvio* è una caratteristica del protocollo TCP, che impedisce la congestione di rete limitando la quantità di dati inviati in rete. La lentezza di avvio inizia con finestre di congestione di dimensioni ridotte tra il mittente e il destinatario, fino a raggiungere le dimensioni massime o a rilevare una perdita di pacchetti.

La rete CDN di Azure con tecnologie Akamai e Verizon elimina la lentezza di avvio in tre passaggi:

1.  La rete Akamai e la rete Verizon usano entrambe il monitoraggio dell'integrità e della larghezza di banda per misurare la larghezza di banda delle connessioni tra i server POP perimetrali.
2. Le metriche vengono condivise tra i server POP perimetrali in modo che ogni server sia in grado di determinare le condizioni di rete e l'integrità degli altri server POP nelle vicinanze.  
3. I server perimetrali della rete CDN sono ora in grado di prevedere alcuni parametri di trasmissione, ad esempio le dimensioni ottimali delle finestre di congestione durante la comunicazione con altri server perimetrali della rete CDN nelle vicinanze. Questo significa che è possibile aumentare le dimensioni iniziali della finestra di congestione se l'integrità della connessione tra i server perimetrali della rete CDN è in grado di supportare trasferimenti di dati di pacchetto di dimensioni maggiori.  

#### <a name="leveraging-persistent-connections"></a>Uso di connessioni persistenti

Con una rete CDN, un numero minore di computer univoci si connette direttamente al server di origine rispetto agli utenti che si connettono direttamente all'origine. La rete CDN di Azure con tecnologie Akamai e Verizon crea inoltre pool di richieste degli utenti per stabilire un numero minore di connessioni all'origine.

Come indicato in precedenza, le connessioni TCP comportano diverse richieste nelle due direzioni in un handshake per stabilire una nuova connessione. Le connessioni persistenti, chiamate anche "HTTP keep-alive", riutilizzano le connessioni TCP esistenti per più richieste HTTP, in modo da ottimizzare i tempi di round trip e accelerare la distribuzione. 

La rete Verizon invia pacchetti "keep-alive" tramite la connessione TCP anche per impedire la chiusura di una connessione aperta.

#### <a name="tuning-tcp-packet-parameters"></a>Ottimizzazione dei parametri dei pacchetti TCP

La rete CDN di Azure con tecnologia Akamai ottimizza anche i parametri che determinano le connessioni da server a server e riduce la quantità di round trip a lungo termine necessari per recuperare il contenuto incorporato nel sito tramite le tecniche seguenti:

1.  Aumentando la finestra di congestione iniziale in modo da inviare più pacchetti senza attendere un acknowledgement.
2.  Riducendo il timeout di ritrasmissione iniziale in modo da rilevare le perdite e accelerare la ritrasmissione.
3.  Riducendo i timeout di ritrasmissione minimo e massimo per ridurre i tempi di attesa prima di supporre una perdita dei pacchetti durante la trasmissione.

### <a name="object-prefetch-akamai-only"></a>Prelettura degli oggetti (solo Akamai)

La maggior parte dei siti Web è costituita da una pagina HTML che fa riferimento a diverse altre risorse, come immagini e script. In genere, quando un client richiede una pagina Web, il browser scarica e analizza prima di tutto l'oggetto HTML, quindi effettua richieste aggiuntive agli asset collegati necessari per caricare completamente la pagina. 

La *prelettura* è una tecnica per recuperare le immagini e gli script incorporati nella pagina HTML quando il file HTML viene reso disponibile al browser e addirittura prima che il browser effettui le richieste di questi oggetti. 

Attivando l'opzione di **prelettura** quando la rete CDN rende disponibile la pagina HTML di base al browser del client, analizza il file HTML ed effettua richieste aggiuntive di tutte le risorse collegate, archiviandole nella cache. Quando il client effettua le richieste degli asset collegati, il server perimetrale della rete CDN include già gli oggetti richiesti e può renderli disponibili immediatamente senza un round trip all'origine. Questa ottimizzazione è utile per contenuti memorizzabili e non memorizzabili nella cache.

### <a name="adaptive-image-compression-akamai-only"></a>Compressione di immagini adattiva (solo Akamai)

Alcuni dispositivi, in particolare quelli mobili, riscontrano occasionalmente velocità di rete minori. In questi scenari è più utile per l'utente ricevere nella propria pagina Web immagini di dimensioni minori più rapidamente anziché attendere più a lungo le immagini a risoluzione completa.

Questa funzionalità monitora automaticamente la qualità della rete e usa metodi di compressione JPEG standard per migliorare i tempi di ripristino quando le velocità di rete sono inferiori.

Compressione di immagini adattiva | Estensioni di file  
--- | ---  
Compressione JPEG | JPG, JPEG, JPE, JIG, JGIG, JGI

## <a name="caching"></a>Memorizzazione nella cache

Con la funzionalità Accelerazione sito dinamico, la memorizzazione nella cache è disattivata per impostazione predefinita nella rete CDN, anche quando l'origine include intestazioni Cache-Control/Expires nella risposta. Questa impostazione è disattivata automaticamente perché la funzionalità Accelerazione sito dinamico viene in genere usata per asset dinamici che non devono essere memorizzati nella cache in quanto univoci per ogni client e la sua attivazione per impostazione predefinita può interrompere questo comportamento.

Se il sito Web include una combinazione di asset statici e dinamici, è preferibile adottare un approccio ibrido per ottenere prestazioni ottimali. 

Se si usa ADN con Verizon Premium, è possibile riattivare la memorizzazione nella cache per casi specifici tramite il motore regole.  

Un'alternativa consiste nell'usare due endpoint di rete CDN, uno con Accelerazione sito dinamico per distribuire asset dinamici e un altro con un'ottimizzazione di tipo statico, ad esempio la distribuzione Web generica, per distribuire asset memorizzabili nella cache. Per adottare questa alternativa, è necessario modificare gli URL delle pagine Web per il collegamento diretto all'asset nell'endpoint di rete CDN che si prevede di usare. 

Ad esempio: `mydynamic.azureedge.net/index.html` è una pagina dinamica e viene caricata dall'endpoint con Accelerazione sito dinamico.  La pagina HTML fa riferimento a più asset statici, tra cui librerie JavaScript o immagini caricate dall'endpoint di rete CDN statico, come `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 

[Qui](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) è disponibile un esempio di come usare controller in un'applicazione Web ASP.NET per rendere disponibili contenuti tramite un URL di rete CDN specifico.





