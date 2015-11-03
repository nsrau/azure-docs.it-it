<properties 
	pageTitle="Come utilizzare la rete CDN | Microsoft Azure" 
	description="Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure per distribuire contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="09/01/2015" 
	ms.author="mazha"/>


# Uso della rete CDN per Azure

La rete per la distribuzione di contenuti (rete CDN) di Azure è il blocco predefinito fondamentale per la scalabilità di qualsiasi applicazione HTTP in Azure. Offre ai clienti di Azure una soluzione globale memorizzando nella cache e distribuendo contenuti vicino agli utenti finali. Invece di raggiungere ogni volta l'origine, quindi, le richieste utente vengono indirizzate in modo intelligente al POP perimetrale della rete CDN con le prestazioni migliori. Ciò migliora in modo significativo le prestazioni e l'esperienza utente. Per un elenco aggiornato delle ubicazioni dei nodi della rete CDN, vedere [Ubicazioni dei nodi della rete CDN di Azure].

L'uso della rete CDN per la memorizzazione dei dati Azure nella cache offre molti vantaggi, inclusi i seguenti:

-   Migliori prestazioni ed esperienza utente ottimale per gli utenti finali che si trovano lontano da un'origine di contenuto e usano applicazioni in cui per il caricamento del contenuto sono necessari numerosi passaggi in Internet.
-   Elevata scalabilità distribuita, per una gestione migliore dei carichi elevati istantanei, ad esempio nelle fasi iniziali di un evento quale il lancio di un prodotto.
-   Mediante la distribuzione delle richieste utente e la gestione dei contenuti da POP perimetrali globali, sarà possibile inviare meno traffico all'origine e quindi ottenere l'offload dell'origine.

I clienti esistenti della rete CDN possono ora usare la rete CDN di Azure [Portale di gestione di Azure]. La rete CDN è una caratteristica aggiuntiva della sottoscrizione e prevede un piano di fatturazione distinto.

##Passaggio 1: Creare un'origine di rete CDN in Azure

L'origine della rete CDN corrisponde alla posizione da cui la rete CDN recupera i contenuti e li memorizza nella cache nei POP perimetrali. L'origine integrata di Azure include app, Servizi cloud, Archiviazione e Servizi multimediali di Azure.

## Passaggio 2: Creare un endpoint della rete CDN che fa riferimento all'origine di Azure

Dopo la configurazione, l'origine sarà disponibile nell'elenco di origini quando si [crea un nuovo endpoint della rete CDN](cdn-create-new-endpoint.md).

> [AZURE.NOTE]La configurazione creata per l'endpoint non sarà disponibile immediatamente. Per la propagazione della registrazione nella rete CDN potrebbero essere necessari fino a 60 minuti. È possibile che gli utenti che provano a usare immediatamente il nome di dominio della rete CDN ricevano un errore con codice di stato 400 (Richiesta non valida) fino a quando il contenuto non risulterà disponibile tramite la rete CDN.

## Passaggio 3: Impostare la configurazione della rete CDN 

È possibile abilitare alcune funzionalità per l'endpoint della rete CDN, ad esempio i criteri di memorizzazione della cache, la memorizzazione nella cache delle stringhe di query e così via.

## Passaggio 4: Accedere al contenuto della rete CDN

Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. Ad esempio, l'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:: http://<*SpazioDeiNomiCDN*>.vo.msecnd.net/<*ContenitorePubblico*>/<*NomeBLOB*>



## Vedere anche

[Panoramica della Rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md)
 

<!---HONumber=Nov15_HO1-->