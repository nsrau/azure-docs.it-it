---
title: Configurare il lead del progetto come Lab con Azure Lab Services
description: Informazioni su come configurare i Lab per insegnare a Project Lead il modo in cui le classi.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024620"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurare i Lab per il lead del progetto come le classi

[Project Lead The Way (pltw)](https://www.pltw.org/) è un'organizzazione no profit che fornisce un &ndash; curriculum PreK 12 nei Stati Uniti in informatica, progettazione e scienza biomedica.  In ogni classe PLTW gli studenti usano un'ampia gamma di applicazioni software come parte dell'esperienza di apprendimento pratica.  Molte applicazioni software richiedono una CPU veloce oppure, in alcuni casi, una GPU.  Questo articolo illustra come configurare i Lab per le classi PLTW seguenti, che in genere sono offerte agli studenti di livello 9 &ndash; 12:

- **Introduzione alla progettazione della progettazione**

    Gli studenti sono introdotti al processo di progettazione ingegneristico, che include l'uso del software di [Autodesk Inventory (computer-aided design)](https://www.autodesk.com/products/inventor/new-features) per la modellazione 3D.

- **Principi della progettazione**
    
    Gli studenti imparano a conoscere meccanismi di progettazione, complessità strutturale e materiale e automazione.  Questa classe usa il software, ad esempio i [solidi MD](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), la [finestra di progettazione Bridge di punti occidentali](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [la simulazione dell'esercito di America](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Ingegneria civile e architettura**

    Gli studenti imparano a sviluppare e progettare e sviluppare siti usando [Autodesk Revit](https://www.autodesk.com/products/revit/overview) architecture design software per 3D Building Information Modeling (BIM).

- **Produzione integrata del computer**

    Gli studenti esplorano i processi di produzione moderni che coinvolgono robotica e automazione.   In questa classe gli studenti usano il software di produzione assistita di [Autodesk Inventory CAD](https://www.autodesk.com/products/inventor/new-features) e [Autodesk Inventory](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Elettronica digitale**

    Gli studenti studiano circuiti e dispositivi per la logica elettronica usando il software per la simulazione [Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) e la progettazione dei circuiti.

- **Progettazione e sviluppo di progettazione**

    Gli studenti contribuiscono a una soluzione end-to-end combinando ricerca, progettazione e test che presentano a un pannello di tecnici.  In questa classe gli studenti usano il software [Autodesk Inventory CAD](https://www.autodesk.com/products/inventor/new-features) .

- **Informatica Essentials**

    Gli studenti sono introdotti in concetti e strumenti di calcolo.  Iniziano con la programmazione basata su blocchi e quindi passano alla codifica basata su testo usando gli ambienti di codifica, ad esempio i [blocchi VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principi di informatica**
    
    Gli studenti crescono le proprie competenze di programmazione con [Python](https://www.python.org/) usando l' [ambiente di sviluppo del codice Microsoft Visual Studio](https://code.visualstudio.com/). 

- **Informatica A**

    Gli studenti espandono la propria competenza di programmazione in questa classe imparando lo sviluppo di app per dispositivi mobili.  In questa classe imparano [Java](https://www.java.com/) usando l'ambiente di [sviluppo del codice Microsoft Visual Studio](https://code.visualstudio.com/).  Gli studenti usano anche un emulatore che consente di eseguire e testare il codice dell'app per dispositivi mobili.  Per informazioni su come configurare un emulatore in Azure Lab Services, [contattare Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com).

Per un elenco completo del software della classe, visitare il [sito di pltw](https://www.pltw.org/pltw-software) per ogni classe.

## <a name="lab-configuration"></a>Configurazione del lab

Per iniziare a configurare Labs per PLTW, è necessario disporre di una sottoscrizione di Azure e di un account Lab. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per altre informazioni sulla creazione di un nuovo account Lab, vedere [configurare un account Lab](./tutorial-setup-lab-account.md). È anche possibile usare un account lab esistente.

Dopo aver configurato un account Lab, è necessario creare un Lab separato per ogni sessione della classe PLTW offerta dalla scuola.  Si consiglia inoltre di creare immagini separate per ogni tipo di classe PLTW.  Per altre informazioni su come strutturare i Lab e le immagini, vedere il post di Blog relativo al [passaggio da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni dell'account Lab come descritto nella tabella seguente. Per altre informazioni su come abilitare le immagini di Azure Marketplace, vedere [specificare le immagini di Azure Marketplace disponibili per gli autori di Lab](./specify-marketplace-images.md).

| Impostazione dell'account lab | Istruzioni |
| -------------------- | ----- |
| Immagine del Marketplace | Abilitare l'immagine Windows 10 Pro da usare nell'account Lab. |

<br>

### <a name="lab-settings"></a>Impostazioni del lab
Le dimensioni della macchina virtuale (VM) che si consiglia di usare per le classi PLTW dipendono dai tipi di carichi di lavoro che gli studenti eseguono nella classe.  Per le classi elencate in precedenza, è consigliabile usare una piccola GPU (visualizzazione) e dimensioni di macchine virtuali di grandi dimensioni. Quando si configurano i Lab per le classi PLTW, fare riferimento alle linee guida riportate nella tabella seguente:

| Impostazione Lab | Valore e descrizione | Raccomandazione della classe |
| ------------ | ------------------ | --- |
| Dimensioni della macchina virtuale | **GPU small (visualizzazione)**<br>Ideale per visualizzazione remota, streaming, giochi e codifica con Framework come OpenGL e DirectX. | È consigliabile usare questa dimensione per le classi PLTW seguenti: ingegneria civile e architettura, elettronica digitale, produzione integrata del computer e progettazione e sviluppo di ingegneria.
| Dimensioni della macchina virtuale | **large**<br>Ideale per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale, database di grandi dimensioni e cache di memoria di grandi dimensioni. | È consigliabile usare questa dimensione per le classi PLTW seguenti: Introduzione alla progettazione ingegneristica, principi di progettazione, informatica Essentials, principi di informatica e informatica A. |

<br>

### <a name="license-server"></a>Server licenze
La maggior parte del software usato nelle classi PLTW citate in precedenza *non richiede l'* accesso a un server licenze.  Tuttavia, è necessario accedere a un server licenze se si prevede di utilizzare il modello di licenza di rete Autodesk per il software seguente:
-   Revit
-   Inventore
-   CAMMA Inventory

Per usare la gestione delle licenze di rete con il software Autodesk, [pltw fornisce i passaggi dettagliati](https://www.pltw.org/pltw-software) per installare Autodesk Network License Manager nel server licenze.  Questo server licenze si trova in genere nella rete locale o ospitata in una macchina virtuale (VM) di Azure all'interno di una rete virtuale di Azure.

Dopo aver configurato il server licenze, sarà necessario eseguire [il peering della rete virtuale](./how-to-connect-peer-virtual-network.md) con l' [account Lab](./tutorial-setup-lab-account.md). È necessario eseguire il peering di rete *prima* di creare il Lab in modo che le macchine virtuali del Lab possano accedere al server licenze e viceversa.

I file di licenza generati da Autodesk incorporano l'indirizzo MAC del server licenze.  Se si decide di ospitare il server licenze tramite una macchina virtuale di Azure, è importante assicurarsi che l'indirizzo MAC del server licenze non cambi. Se l'indirizzo MAC viene modificato, sarà necessario rigenerare i file di licenza. Per impedire la modifica dell'indirizzo MAC, effettuare le operazioni seguenti:

- [Impostare un indirizzo IP e un indirizzo Mac privato statico](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) per la macchina virtuale di Azure che ospita il server licenze.
- Assicurarsi di configurare sia l'account Lab che la rete virtuale del server licenze in un'area o in una località con capacità VM sufficiente, in modo da non dover spostare queste risorse in una nuova area o in una nuova posizione in un secondo momento.

Per altre informazioni, vedere [configurare un server licenze come risorsa condivisa](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Computer modello
Alcuni dei file di installazione necessari per PLTW sono di grandi dimensioni. Quando si scaricano i file in una macchina virtuale del modello Lab, la copia potrebbe richiedere molto tempo.

Anziché scaricare i file di installazione nel computer modello e installare tutti gli elementi, è consigliabile creare le immagini PLTW nell'ambiente fisico.  È quindi possibile importare le immagini personalizzate nella raccolta immagini condivise in modo da poterle usare per creare i Lab.  Per altre informazioni, vedere [caricare un'immagine personalizzata nella raccolta di immagini condivise](./upload-custom-image-shared-image-gallery.md).

Quando si segue questa raccomandazione, tenere presenti le principali attività per la configurazione di un Lab:

1. Nell'ambiente fisico creare l'immagine per la classe.

    a.  Usare la procedura dettagliata di PLTW per scaricare i file di installazione e installare il software necessario.

    > [!NOTE]    
    > Quando si installano le applicazioni Autodesk, il computer in cui si sta installando deve essere in grado di comunicare con il server licenze. Tramite l'installazione guidata di Autodesk verrà richiesto di specificare il nome del computer in cui è ospitato il server licenze.  Se si ospita il server licenze in una macchina virtuale di Azure, potrebbe essere necessario attendere per installare Autodesk nella macchina virtuale del modello lab in modo che l'installazione guidata possa accedere al server licenze.

    b.  [Installare e configurare OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) o altre opzioni di backup che potrebbero essere usate dalla scuola.
    
    c.  [Installare e configurare gli aggiornamenti di Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Caricare l'immagine personalizzata nella [raccolta di immagini condivise collegata all'account Lab](./how-to-attach-detach-shared-image-gallery.md).

1.  Creare un Lab, quindi selezionare l'immagine personalizzata caricata nel passaggio precedente.

1.  Dopo aver creato il Lab, avviare e connettersi alla macchina virtuale modello per verificare che l'immagine funzioni come previsto.

1.  Infine, pubblicare la macchina virtuale modello per creare le macchine virtuali degli studenti.

## <a name="student-devices"></a>Dispositivi per studenti
Gli studenti possono connettersi alle macchine virtuali del Lab da computer Windows, Mac e Chromebook. Per le istruzioni, vedere

- [Eseguire la connessione da Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Connetti da Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Connetti da Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Cost
Viene ora trattata una stima dei costi di esempio per le classi PLTW.  Questa stima non include il costo per l'esecuzione di un server licenze o l'uso di una raccolta di immagini condivise. Si supponga di disporre di una classe di 25 studenti, ognuno dei quali ha 20 ore di tempo di classe pianificato.  Ogni studente ha anche 10 ore di quota aggiuntive per il lavoro o le assegnazioni al di fuori dell'ora di classe pianificata.  Ecco i costi stimati:

- **VM di grandi dimensioni**

    25 studenti &times; (20 ore pianificate + 10 ore di quota) &times; 70 unità Lab da &times; USD 0,01 all'ora = USD 525.00

- **GPU piccola (visualizzazione)**

    25 studenti &times; (20 ore pianificate + 10 ore di quota) &times; 160 unità Lab da &times; USD 0,01 all'ora = USD 1200.00

> [!IMPORTANT] 
> La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Molte delle classi PLTW usano le applicazioni a cui si accede tramite un browser, ad esempio MIT app Inventory.  Queste applicazioni basate su browser non richiedono una CPU o una GPU veloce ed è possibile accedervi da qualsiasi dispositivo che disponga di una connessione Internet.  Quando gli studenti usano questi tipi di applicazioni, è consigliabile usare il browser sul dispositivo fisico anziché sul browser nella macchina virtuale del Lab. Gli studenti possono contribuire a ridurre i costi usando la VM Lab solo per le applicazioni che richiedono una CPU veloce o GPU.

## <a name="next-steps"></a>Passaggi successivi

Quando si configura il Lab, vedere gli articoli seguenti:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare quote](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users) 
