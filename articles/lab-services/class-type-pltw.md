---
title: Configurare il lead del progetto come Lab con Azure Lab Services
description: Informazioni su come configurare i Lab per insegnare a Project Lead il modo in cui le classi.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496889"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurare i Lab per il lead del progetto come le classi

[Project Lead The Way (pltw)](https://www.pltw.org/) è un'organizzazione no profit che fornisce un curriculum Prek-12 attraverso le Stati Uniti in informatica, progettazione e scienza biomedica.  In ogni classe PLTW gli studenti usano un'ampia gamma di applicazioni software come parte dell'esperienza di apprendimento pratica.  Molte applicazioni software richiedono una CPU veloce oppure, in alcuni casi, una GPU.  Questo articolo illustra come configurare i Lab per le classi PLTW seguenti, generalmente offerte agli studenti nei voti 9-12:

- **Introduzione alla progettazione della progettazione**

    Gli studenti sono stati introdotti nel processo di progettazione ingegneristico, che include l'utilizzo del software di [Autodesk Inventory computer-aided design (CAD)](https://www.autodesk.com/products/inventor/new-features) per la modellazione 3D.

- **Principi della progettazione**
    
    Gli studenti imparano a conoscere i meccanismi di progettazione, la forza structural\material e l'automazione.  Questa classe usa il software, ad esempio i [solidi MD](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), la [finestra di progettazione Bridge di punti occidentali](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [la simulazione dell'esercito di America](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Ingegneria civile e architettura**

    Gli studenti hanno insegnato la progettazione e lo sviluppo di progetti e siti usando [il software di progettazione dell'architettura Revit di Autodesk](https://www.autodesk.com/products/revit/overview) per la modellazione delle informazioni di compilazione 3D (BIM).

- **Produzione integrata del computer**

    Gli studenti esplorano i processi di produzione moderni che coinvolgono robotica e automazione.   In questa classe gli studenti usano il software di produzione assistita con l'inventore di [Autodesk](https://www.autodesk.com/products/inventor/new-features) e l' [inventore di Autodesk](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Elettronica digitale**

    Gli studenti studiano i circuiti e i dispositivi della logica elettronica usando il software per la simulazione e [il circuito di Multisim dello strumento nazionale](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Progettazione e sviluppo di progettazione**

    Gli studenti contribuiscono a una soluzione end-to-end che combina ricerca, progettazione e test che presentano a un panel di tecnici.  In questa classe gli studenti usano [il software CAD Inventory di Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Informatica Essentials**

    Gli studenti sono introdotti in concetti e strumenti di calcolo.  Iniziano con la programmazione basata su blocchi e quindi passano a usare la codifica basata su testo usando gli ambienti di codifica, ad esempio i [blocchi VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principi di informatica**
    
    Gli studenti crescono le proprie competenze di programmazione con [Python](https://www.python.org/) usando [l'ambiente di sviluppo Visual Studio Code Microsoft](https://code.visualstudio.com/). 

- **Informatica A**

    Gli studenti sviluppano la propria esperienza di programmazione in questa classe imparando lo sviluppo di app per dispositivi mobili.  In questa classe imparano [Java](https://www.java.com/) usando [l'ambiente di sviluppo Visual Studio Code Microsoft](https://code.visualstudio.com/).  Gli studenti usano anche un emulatore che consente di eseguire e testare il codice dell'app per dispositivi mobili.  Per informazioni su come configurare un emulatore in Azure Labs, contattare Microsoft all'indirizzo azlabspilot@microsoft.com .

Per l' [elenco completo del software](https://www.pltw.org/pltw-software) per ogni classe, vedere il sito di pltw.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare Labs per PLTW, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere l'esercitazione su [come configurare un account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). È anche possibile usare un account lab esistente.

Quando si dispone di un account Lab, è necessario creare laboratori distinti per ogni sessione di una classe PLTW offerta dalla scuola.  Si consiglia inoltre di creare immagini separate per ogni tipo di classe PLTW.  Per altri dettagli su come strutturare i Lab e le immagini, vedere il post di Blog relativo [al passaggio da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab
Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere l'articolo su [come specificare le immagini del Marketplace disponibili per gli autori di Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Impostazione dell'account lab | Istruzioni |
| -------------------- | ----- |
| Immagine del Marketplace | Abilitare l'immagine Windows 10 Pro da usare nell'account Lab. |

### <a name="lab-settings"></a>Impostazioni del lab
Le dimensioni della macchina virtuale consigliata per le classi PLTW dipendono dai tipi di carichi di lavoro che gli studenti eseguono nella classe.  Per le classi precedenti, è consigliabile usare dimensioni della macchina virtuale di grandi dimensioni e piccole dimensioni (visualizzazione).  Per la configurazione dei Lab per le classi PLTW, vedere le indicazioni nella tabella seguente.

| Impostazione Lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| **Piccola GPU (visualizzazione)**.  Questa macchina virtuale è ideale per la visualizzazione remota, lo streaming, i giochi e la codifica tramite Framework come OpenGL e DirectX.  È consigliabile usare questa dimensione per le classi PLTW seguenti: ingegneria civile e architettura, elettronica digitale, produzione integrata del computer; e progettazione e sviluppo di progettazione.
|Dimensioni della macchina virtuale| **Grande**.  Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate.  È consigliabile usare questa dimensione per le classi PLTW seguenti: Introduzione alla progettazione ingegneristica, principi di progettazione, informatica Essentials, principi di informatica e informatica A.

### <a name="licensing-server"></a>Server licenze
La maggior parte del software usato nelle classi PLTW precedenti do * *_not_* _ richiede l'accesso a un server licenze.  Tuttavia, sarà necessario accedere a un server licenze se si prevede di utilizzare il modello di licenza di rete di Autodesk per il software seguente:
-   Revit
-   Inventore
-   CAMMA Inventory

Per utilizzare le licenze di rete con il software di Autodesk, [pltw fornisce i passaggi dettagliati](https://www.pltw.org/pltw-software) per l'installazione di gestione licenze di Autodesk nel server licenze.  Questo server licenze si trova in genere nella rete locale o ospitata in una macchina virtuale (VM) di Azure all'interno di una rete virtuale di Azure (VNet).

Dopo aver configurato il server licenze, è necessario eseguire [il peering di VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) all' [account Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Il peering di rete deve essere eseguito _before * creando il Lab in modo che le macchine virtuali del Lab possano accedere al server licenze e viceversa.

I file di licenza generati da Autodesk incorporano l'indirizzo MAC del server licenze.  Se si decide di ospitare il server licenze tramite una macchina virtuale di Azure, è importante assicurarsi che l'indirizzo MAC del server licenze non cambi.   In caso contrario, quando l'indirizzo MAC cambia, sarà necessario rigenerare i file di licenza.  Per evitare la modifica dell'indirizzo MAC, seguire questi suggerimenti:

- [Impostare un indirizzo IP e un indirizzo Mac privato statico](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) per la macchina virtuale di Azure che ospita il server licenze.
- Assicurarsi di configurare l'account Lab e il VNet del server licenze in un region\location con capacità VM sufficiente, in modo da non dover spostare queste risorse in un nuovo region\location in un secondo momento.

Leggere anche l'articolo su [come configurare un server licenze come risorsa condivisa](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) per altre informazioni.

### <a name="template-machine"></a>Computer modello
Alcuni dei file di installazione necessari per PLTW sono di grandi dimensioni e richiedono molto tempo per la copia quando vengono scaricati nel computer modello di un Lab.

Anziché scaricare i file di installazione nel computer modello e installare tutti gli elementi, è consigliabile creare le immagini PLTW nell'ambiente fisico.  Quindi, è possibile importare le immagini nella raccolta immagini condivise in modo da poter usare queste immagini personalizzate per creare i Lab.  Per informazioni dettagliate, leggere l'articolo seguente: [caricare un'immagine personalizzata nella raccolta di immagini condivise](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

Seguendo questa raccomandazione, di seguito sono riportate le principali attività per la configurazione di un Lab:

1. Nell'ambiente fisico creare l'immagine per la classe.

    a.  Usare la procedura dettagliata di PLTW per scaricare i file di installazione e installare il software necessario.

    > [!NOTE]    
    > Quando si installano le applicazioni di Autodesk, il computer in cui si installa Autodesk deve essere in grado di comunicare con il server licenze (l'installazione guidata di Autodesk richiede di specificare il nome del computer in cui è ospitato il server licenze).  Se si ospita il server licenze in una macchina virtuale di Azure, potrebbe essere necessario attendere per installare Autodesk nel computer modello del Lab in modo che l'installazione guidata di Autodesk possa accedere al server licenze

    b.  [Installare e configurare OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (o altre opzioni di backup che possono essere usate dalla scuola).
    
    c.  [Installare e configurare gli aggiornamenti di Windows](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Caricare l'immagine personalizzata nella [raccolta di immagini condivise collegata all'account Lab](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery).

1.  Creare un Lab e selezionare l'immagine personalizzata caricata nel passaggio precedente.

1.  Dopo aver creato il Lab, avviare e connettersi al computer modello per convalidare l'immagine funziona come previsto.

1.  Infine, pubblicare il computer modello per creare le macchine virtuali degli studenti.

## <a name="student-devices"></a>Dispositivi per studenti
Gli studenti possono connettersi alle macchine virtuali del Lab da computer Windows\Mac e Chromebook.  Ecco i collegamenti alle istruzioni per ognuna di queste opzioni:

- [Eseguire la connessione da Windows](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Connetti da Mac](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Connetti da Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Cost
Si tratterà di una possibile stima dei costi per le classi PLTW precedenti.  Questa stima non include il costo per l'esecuzione di un server licenze o per l'uso di raccolta immagini condivise.  Verrà usata una classe di 25 studenti.  Sono previste 20 ore di tempo della classe pianificata.  Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori del tempo di classe pianificato.  Vedere le stime dei costi inferiori per le dimensioni della GPU ( **grandi** e **piccole)** .

- **VM di grandi dimensioni**

    25 studenti x (20 ore pianificate + 10 ore di quota) x 70 unità Lab x 0,01 USD all'ora = 525,00 USD

- **GPU small (visualizzazione)**

    25 studenti x (20 ore pianificate + 10 ore di quota) x 160 unità Lab x 0,01 USD all'ora = 1200,00 USD

> [!IMPORTANT] 
> La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Molte delle classi PLTW usano le applicazioni a cui si accede tramite un browser, ad esempio MIT app Inventory.  Queste applicazioni basate su browser non richiedono una CPU o una GPU veloce ed è possibile accedervi da qualsiasi dispositivo che disponga di una connessione Internet.  Quando gli studenti usano questi tipi di applicazioni, è consigliabile usare il browser sul dispositivo fisico invece di usare il browser nelle VM del Lab.  Ciò consentirà di ridurre i costi usando solo le macchine virtuali del Lab per le applicazioni che richiedono una CPU veloce o GPU.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Inviare i collegamenti di registrazione agli studenti tramite posta elettronica](how-to-configure-student-usage.md#send-invitations-to-users). 