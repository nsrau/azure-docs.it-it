
<properties 
    pageTitle="Uso previsto della larghezza di banda di rete di Azure RemoteApp | Microsoft Azure"
	description="Informazioni sui requisiti relativi alla larghezza di banda di rete per le raccolte e le app Azure RemoteApp."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/27/2016" 
    ms.author="elizapo" />

# Uso previsto della larghezza di banda di rete di Azure RemoteApp 

Azure RemoteApp usa il protocollo RDP (Remote Desktop Protocol) per la comunicazione tra applicazioni in esecuzione nel cloud di Azure e gli utenti. In questo articolo vengono offerte alcune linee guida di base che è possibile usare per stimare l'utilizzo della rete e valutare potenzialmente l'utilizzo della larghezza di banda di rete per ogni utente di Azure RemoteApp.

La stima dell'utilizzo della larghezza di banda per utente è molto complessa e richiede l'esecuzione di più applicazioni contemporaneamente all'interno di scenari multitasking in cui le applicazioni possono influire sulle prestazioni le une delle altre in base alla richiesta di larghezza di banda di ognuna. Anche il tipo di client desktop remoto (ad esempio client Mac rispetto a client HTML5) può produrre risultati diversi dal punto di vista della larghezza di banda. Per agevolare il lavoro nonostante queste complicazioni, è utile replicare scenari reali suddividendo gli scenari di utilizzo in alcune categorie comuni. Uno scenario reale, ovviamente, è una combinazione di categorie e differisce a seconda dell'utente.

Prima di continuare, si tenga conto del presupposto che il protocollo RDP garantisca un'esperienza da buona a eccellente per la maggior parte degli scenari di utilizzo con le reti con latenza inferiore a 120 ms e larghezza di banda oltre 5 MB. Questo presupposto si basa sulla capacità di regolazione dinamica del protocollo RDP tramite la larghezza di banda di rete disponibile e le esigenze stimate di larghezza di banda delle applicazioni. Questo articolo va oltre "la maggior parte degli scenari di utilizzo" per esaminare i margini, dove gli scenari iniziano a perdere definizione e l'esperienza utente inizia a degradarsi.

Per i dettagli, tra cui i fattori da considerare, consigli di base ed elementi non considerati nelle stime, è consigliabile leggere gli articoli seguenti.

- [In che modo la larghezza di banda di rete influisce sulla qualità dell'esperienza?](remoteapp-bandwidthexperience.md)
- [Scenari comuni di test relativi all'utilizzo della larghezza di banda di rete](remoteapp-bandwidthtests.md)
- [Linee guida rapide se non è possibile o non si ha tempo di effettuare test](remoteapp-bandwidthguidelines.md)


## Cosa non è considerato

Quando si esaminano i test proposti e i consigli generali, effettivamente piuttosto generici, tenere presente che alcuni fattori non vengono considerati, ad esempio le complicazioni dell'esperienza utente dovute all'asimmetria della larghezza di banda di download rispetto a quella di upload. La natura asimmetrica della maggior parte delle reti Wi-Fi, inoltre, influisce sulle prestazioni e sulla percezione dell'esperienza utente. Per gli scenari interattivi il traffico downstream potrebbe avere una priorità inferiore rispetto al traffico upstream. Ciò potrebbe aumentare il numero di fotogrammi video o audio persi e pertanto influire sulla percezione dell'utente dell'esperienza di streaming. È possibile eseguire esperimenti personalizzati per conoscere i fattori in grado di migliorare l'esperienza utente nel proprio caso specifico e con la propria rete.

Verrà esaminato il reindirizzamento dei dispositivi, ma non verrà preso in considerazione l'impatto sulla larghezza di banda del traffico di rete causato da dispositivi collegati, ad esempio risorse di archiviazione, stampanti, scanner, fotocamere Web e altri dispositivi USB. Tali dispositivi di solito causano picchi temporanei nella richiesta di larghezza di banda, che si ridimensionano al termine di ciascuna attività. Se questo accade di frequente, tuttavia, la mancanza di larghezza di banda potrebbe rendersi evidente.

Inoltre non verrà esaminato in che modo un utente possa influire sugli altri utenti all'interno della stessa rete. Ad esempio, un utente che usa video 4K in una rete a 100 MB/s può influire in modo significativo sugli altri utenti della stessa rete che tentano di eseguire la stessa attività. Purtroppo diventa sempre più difficile determinare l'impatto dell'uso simultaneo per offrire una soluzione comune o esaustiva per le prestazioni del sistema nel complesso. È possibile affermare solo che la tecnologia del protocollo sottostante usa al meglio la larghezza di banda disponibile, ma presenta comunque alcune limitazioni.

<!---HONumber=AcomDC_0629_2016-->