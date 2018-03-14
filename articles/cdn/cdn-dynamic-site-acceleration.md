---
title: Accelerazione sito dinamico tramite la rete CDN di Azure
description: Approfondimento dell'accelerazione sito dinamico
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: rli
ms.openlocfilehash: 713f00f432095b7a8a19996fb7bdb7e5f8d79b63
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Accelerazione sito dinamico tramite la rete CDN di Azure

A causa dell'elevata diffusione di social media, e-commerce e Web iper-personalizzato, una percentuale sempre maggiore di contenuti resa disponibile agli utenti finali viene generata in tempo reale. Gli utenti si aspettano un'esperienza Web rapida, affidabile e personalizzata, indipendentemente da browser, posizione, dispositivo o rete. Tuttavia, le innovazioni che rendono queste esperienze così coinvolgenti rallentano anche i download delle pagine e pongono a rischio la qualità dell'esperienza utente. 

Le funzionalità standard della rete per la distribuzione di contenuti (CDN) includono la possibilità di memorizzare nella cache file in posizioni più vicine agli utenti finali per accelerare la distribuzione di file statici. Tuttavia, con le applicazioni Web dinamiche la memorizzazione nella cache di tali contenuti in posizioni periferiche non è possibile, in quanto il server genera il contenuto in risposta al comportamento degli utenti. È più difficile accelerare la distribuzione di tali contenuti rispetto alla memorizzazione nella cache perimetrale tradizionale e a questo scopo è necessaria una soluzione end-to-end in grado di ottimizzare ogni elemento insieme all'intero percorso dei dati, dalle fasi iniziali alla distribuzione. L'ottimizzazione Accelerazione sito dinamico della rete CDN di Azure migliora in modo misurabile le prestazioni delle pagine Web con contenuto dinamico.

La **rete CDN di Azure di Akamai** e la **rete CDN di Azure di Verizon** offrono entrambe l'ottimizzazione basata su Accelerazione sito dinamico tramite il menu **Ottimizzato per** durante la creazione degli endpoint.

> [!Important]
> Solo per i profili di **rete CDN di Azure di Akamai**, è possibile modificare l'ottimizzazione di un endpoint di rete CDN dopo la creazione.
>   
> Per i profili di **rete CDN di Azure di Verizon** non è possibile modificare l'ottimizzazione di un endpoint di rete CDN dopo la creazione.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configurazione di un endpoint di rete CDN per accelerare la distribuzione di file dinamici

Per configurare un endpoint di rete CDN in modo da ottimizzare la distribuzione di file dinamici è possibile usare il portale di Azure, le API REST o qualsiasi SDK client per eseguire la stessa operazione a livello di codice. 

**Per configurare un endpoint di rete CDN per l'ottimizzazione Accelerazione sito dinamico tramite il portale di Azure:**

1. Nella pagina **Profilo rete CDN** selezionare**Endpoint**.

   ![Aggiungere un nuovo endpoint di rete CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Verrà visualizzato il riquadro **Aggiungi un endpoint**.

2. In **Ottimizzato per** selezionare **Accelerazione sito dinamico**.

    ![Creare un nuovo endpoint di rete CDN con Accelerazione sito dinamico](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Per **Percorso probe** immettere il percorso valido di un file.

    Il percorso probe è una caratteristica specifica della funzionalità Accelerazione sito dinamico e ai fini della creazione è necessario un percorso valido. La funzionalità Accelerazione sito dinamico usa un piccolo file di *percorso probe*, incluso nel server di origine per ottimizzare le configurazioni di routing di rete per la rete CDN. Per il file del percorso probe è possibile scaricare e caricare il file di esempio nel proprio sito oppure usare un asset esistente nell'origine che sia all'incirca di 10 KB.

4. Immettere le altre opzioni necessarie per l'endpoint (per altre informazioni, vedere [Creare un nuovo endpoint della rete CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), quindi selezionare **Aggiungi**.

   Una volta creato l'endpoint di rete CDN, questo applica le ottimizzazioni basate su Accelerazione sito dinamico per tutti i file che soddisfano determinati criteri. 


**Per configurare un endpoint esistente per Accelerazione sito dinamico (solo profili di rete CDN di Azure di Akamai):**

1. Nella pagina **Profilo rete CDN** selezionare l'endpoint che si vuole modificare.

2. Selezionare **Ottimizzazione** nel riquadro sinistro. 

   Viene visualizzata la pagina **Ottimizzazione**.

3. In **Ottimizzato per** selezionare **Accelerazione sito dinamico**, quindi selezionare **Salva**.

> [!Note]
> Alla funzionalità Accelerazione sito dinamico si applicano costi aggiuntivi. Per altre informazioni, vedere [Prezzi della rete per la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Ottimizzazione basata su Accelerazione sito dinamico tramite la rete CDN di Azure

La funzionalità Accelerazione sito dinamico nella rete CDN di Azure accelera la distribuzione di asset dinamici tramite le tecniche seguenti:

-   [Ottimizzazione delle route](#route-optimization)
-   [Ottimizzazioni del protocollo TCP](#tcp-optimizations)
-   [Prelettura degli oggetti (solo rete CDN di Azure con tecnologia Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compressione di immagini adattiva (solo rete CDN di Azure con tecnologia Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Ottimizzazione delle route

L'ottimizzazione delle route è importante perché Internet è un ambiente dinamico, in cui le interruzioni temporanee e di traffico influiscono costantemente sulla topologia di rete. Border Gateway Protocol (BGP) è il protocollo di routing di Internet, ma possono essere disponibili route più veloci tramite server POP (Point of Presence) intermediari. 

L'ottimizzazione delle route sceglie il percorso ottimale verso l'origine, in modo che un sito sia costantemente accessibile e il contenuto dinamico venga distribuito agli utenti finali tramite la route più veloce e affidabile possibile. 

La rete Akamai usa alcune tecniche per raccogliere dati in tempo reale e confrontare i vari percorsi nei diversi nodi nel server Akamai, insieme alla route BGP predefinita nella rete Internet aperta, per determinare la route più veloce tra l'origine e il server perimetrale della rete CDN. Queste tecniche permettono di evitare lunghe route e punti di congestione in Internet. 

Analogamente, la rete Verizon usa una combinazione di DNS Anycast, server POP di supporto a capacità elevata e controlli di integrità per determinare i gateway migliori per instradare al meglio i dati dal client all'origine.
 
Di conseguenza, i contenuti completamente dinamici e transazionali vengono distribuiti in modo più rapido e affidabile agli utenti finali, anche quando non sono memorizzabili nella cache. 

### <a name="tcp-optimizations"></a>Ottimizzazioni del protocollo TCP

Transmission Control Protocol (TCP) è il protocollo standard della suite di protocolli Internet e viene usato per distribuire informazioni tra applicazioni in una rete IP.  Per impostazione predefinita, sono necessarie diverse richieste nelle due direzioni per configurare una connessione TCP e vengono applicati limiti per evitare congestioni di rete, che producono inefficienze su larga scala. **La rete CDN di Azure con tecnologia Akamai** gestisce questo problema tramite ottimizzazioni in tre aree: 

 - [Eliminazione della lentezza di avvio TCP](#eliminating-tcp-slow-start)
 - [Uso di connessioni persistenti](#leveraging-persistent-connections)
 - [Ottimizzazione dei parametri dei pacchetti TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminazione della lentezza di avvio TCP

*Slow start* (lentezza di avvio) è un algoritmo del protocollo TCP che impedisce la congestione di rete limitando la quantità di dati inviati in rete. La lentezza di avvio inizia con finestre di congestione di dimensioni ridotte tra il mittente e il destinatario, fino a raggiungere le dimensioni massime o a rilevare una perdita di pacchetti.

 Sia la **rete CDN di Azure fornita da Akamai** che la **rete CDN di Azure fornita da Verizon** eliminano la lentezza di avvio TCP in tre passaggi:

1. Viene usato il monitoraggio dell'integrità e della larghezza di banda per misurare la larghezza di banda delle connessioni tra i server POP perimetrali.
    
2. Le metriche vengono condivise tra i server POP perimetrali in modo che ogni server sia in grado di determinare le condizioni di rete e l'integrità degli altri server POP nelle vicinanze.  
    
3. I server perimetrali della rete CDN fanno ipotesi su alcuni parametri di trasmissione, ad esempio le dimensioni ottimali delle finestre di congestione durante la comunicazione con altri server perimetrali della rete CDN nelle vicinanze. Questo significa che è possibile aumentare le dimensioni iniziali della finestra di congestione se l'integrità della connessione tra i server perimetrali della rete CDN è in grado di supportare trasferimenti di dati di pacchetto di dimensioni maggiori.  

#### <a name="leveraging-persistent-connections"></a>Uso di connessioni persistenti

Con una rete CDN, un numero minore di computer univoci si connette direttamente al server di origine rispetto agli utenti che si connettono direttamente all'origine. La rete CDN di Azure crea inoltre pool di richieste degli utenti per stabilire un numero minore di connessioni all'origine.

Come accennato in precedenza, sono necessarie più richieste di handshake per stabilire una connessione TCP. Le connessioni persistenti, implementate dall'intestazione HTTP `Keep-Alive`, riutilizzano le connessioni TCP esistenti per più richieste HTTP, in modo da ottimizzare i tempi di round trip e accelerare la distribuzione. 

La **rete CDN di Azure con tecnologia Verizon** invia pacchetti "keep-alive" tramite la connessione TCP anche per impedire la chiusura di una connessione aperta.

#### <a name="tuning-tcp-packet-parameters"></a>Ottimizzazione dei parametri dei pacchetti TCP

La **rete CDN di Azure con tecnologia Akamai** ottimizza i parametri che determinano le connessioni da server a server e riduce la quantità di round trip a lungo termine necessari per recuperare il contenuto incorporato nel sito tramite le tecniche seguenti:

- Aumentando la finestra di congestione iniziale in modo da inviare più pacchetti senza attendere un acknowledgement.
- Riducendo il timeout di ritrasmissione iniziale in modo da rilevare le perdite e accelerare la ritrasmissione.
- Riducendo i timeout di ritrasmissione minimo e massimo per ridurre i tempi di attesa prima di supporre una perdita dei pacchetti durante la trasmissione.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Prelettura degli oggetti (solo rete CDN di Azure con tecnologia Akamai)

La maggior parte dei siti Web è costituita da una pagina HTML che fa riferimento a diverse altre risorse, come immagini e script. In genere, quando un client richiede una pagina Web, il browser scarica e analizza prima di tutto l'oggetto HTML, quindi effettua richieste aggiuntive agli asset collegati necessari per caricare completamente la pagina. 

La *prelettura* è una tecnica per recuperare le immagini e gli script incorporati nella pagina HTML quando il file HTML viene reso disponibile al browser e addirittura prima che il browser effettui le richieste di questi oggetti. 

Attivando l'opzione di prelettura quando la rete CDN rende disponibile la pagina HTML di base al browser del client, la rete analizza il file HTML ed effettua richieste aggiuntive di tutte le risorse collegate, archiviandole nella cache. Quando il client effettua le richieste degli asset collegati, il server perimetrale della rete CDN include già gli oggetti richiesti e può renderli disponibili immediatamente senza un round trip all'origine. Questa ottimizzazione è utile per contenuti memorizzabili e non memorizzabili nella cache.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compressione di immagini adattiva (solo rete CDN di Azure con tecnologia Akamai)

Alcuni dispositivi, in particolare quelli mobili, riscontrano occasionalmente velocità di rete minori. In questi scenari è più utile per l'utente ricevere nella propria pagina Web immagini di dimensioni minori più rapidamente anziché attendere più a lungo le immagini a risoluzione completa.

Questa funzionalità monitora automaticamente la qualità della rete e usa metodi di compressione JPEG standard per migliorare i tempi di ripristino quando le velocità di rete sono inferiori.

Compressione di immagini adattiva | Estensioni di file  
--- | ---  
Compressione JPEG | JPG, JPEG, JPE, JIG, JGIG, JGI

## <a name="caching"></a>Memorizzazione nella cache

Con la funzionalità Accelerazione sito dinamico, la memorizzazione nella cache è disattivata per impostazione predefinita nella rete CDN, anche quando l'origine include intestazioni `Cache-Control` o `Expires` nella risposta. La funzionalità Accelerazione sito dinamico viene in genere usata per asset dinamici che non devono essere memorizzati nella cache in quanto univoci per ogni client. La memorizzazione nella cache può interrompere questo comportamento.

Se il sito Web include una combinazione di asset statici e dinamici, è preferibile adottare un approccio ibrido per ottenere prestazioni ottimali. 

Per i profili di **rete CDN di Azure di Verizon Premium** è possibile attivare la memorizzazione nella cache per casi specifici tramite il [motore regole](cdn-rules-engine.md) per gli endpoint con Accelerazione sito dinamico. Le regole create interessano solo gli endpoint del profilo che sono ottimizzati per Accelerazione sito dinamico. 

Per accedere al motore regole per gli endpoint con Accelerazione sito dinamico:
    
1. Nella pagina **Profilo rete CDN **selezionare** Gestisci**.  
    
    ![Pulsante di gestione del profilo della rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

    Si aprirà il portale di gestione della rete CDN.

2. Nel portale di gestione della rete CDN selezionare **ADN** e quindi **Motore regole**. 

    ![Motore regole per Accelerazione sito dinamico](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)


In alternativa, e possibile usare due endpoint di rete CDN, uno ottimizzato con Accelerazione sito dinamico per distribuire asset dinamici e un altro con un'ottimizzazione di tipo statico, ad esempio la distribuzione Web generica, per distribuire asset memorizzabili nella cache. Modificare gli URL delle pagine Web per il collegamento diretto all'asset nell'endpoint di rete CDN che si prevede di usare. 

Ad esempio: `mydynamic.azureedge.net/index.html` è una pagina dinamica e viene caricata dall'endpoint con Accelerazione sito dinamico.  La pagina HTML fa riferimento a più asset statici, tra cui librerie JavaScript o immagini caricate dall'endpoint di rete CDN statico, come `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 

Per un esempio su come usare i controller in un'applicazione Web ASP.NET per rendere disponibili contenuti tramite un URL di rete CDN specifico, vedere [Gestire il contenuto dalle azioni del controller attraverso la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller).




