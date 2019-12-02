---
title: Eseguire la migrazione da StorSimple a Sincronizzazione file di Azure
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple è un prodotto di fine vita e Sincronizzazione file di Azure è la soluzione per la migrazione a. Informazioni sul concetto di migrazione e su come contattare AzureFiles@microsoft.com per la guida alla migrazione personalizzata.
ms.openlocfilehash: edad4d1c6be2c39dbf8150b6ab8979ae3924fb53
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666663"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>Migrazione di StorSimple a Sincronizzazione file di Azure

StorSimple è un prodotto Microsoft sospeso. Il supporto esteso per questo prodotto e il relativo servizio cloud durerà fino alla fine del 2022.
È importante iniziare subito a pianificare una migrazione di StorSimple.

È possibile eseguire la migrazione delle appliance StorSimple per i servizi di Azure a lungo termine per impostazione predefinita e strategica a lungo termine, ovvero Sincronizzazione file di Azure. Si tratta di un servizio di Azure disponibile a livello generale con un set di funzionalità superate rispetto a StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Migrazione completa sul cloud con tempi di inattività limitati
Questo articolo descrive il concetto di come verrà avviata la migrazione.
È fondamentale notare che i clienti che necessitano della migrazione da StorSimple e in Sincronizzazione file di Azure non devono procedere in modo autonomo.

> [!IMPORTANT]
> Microsoft si impegna a supportare i clienti nella migrazione. AzureFiles@microsoft.com di posta elettronica per un piano di migrazione personalizzato, nonché per assistenza durante la migrazione.

## <a name="migration-approach"></a>Approccio alla migrazione
La migrazione a Sincronizzazione file di Azure avrà inizio sul cloud con un minimo effetto sul tempo di inattività locale e limitato.
Il concetto seguente è destinato alle appliance della serie StorSimple 8000.
Se è necessario eseguire la migrazione dalla serie StorSimple 7000, il primo passaggio prevede l'aggiornamento gratuito a un dispositivo di prestito serie 8000 corrispondente da Microsoft.
Contattare AzureFiles@microsoft.com per organizzare un numero appropriato di dispositivi di prestito.

### <a name="general-approach"></a>Approccio generale
![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Descrizione della migrazione sul lato cloud tramite un'appliance virtuale temporanea e Windows Server in una nuova istanza locale di Windows Server che sostituisce l'appliance StorSimple locale")

1. Eseguire un clone del volume dell'appliance StorSimple locale e montarlo in un'appliance virtuale StorSimple temporanea.
2. Connettere l'appliance virtuale tramite iSCSI a una macchina virtuale di Azure temporanea.
3. Installare Sincronizzazione file di Azure nella VM temporanea di Windows Server: è necessario impostare anche una chiave del registro di sistema specifica per questa migrazione prima di configurare la sincronizzazione nel server.
    * A seconda del numero di condivisioni nel volume StorSimple, distribuire così tante condivisioni file di Azure. È consigliabile distribuire una condivisione file di Azure per account di archiviazione.
    * Configurare la sincronizzazione tra la singola condivisione nella macchina virtuale Windows Server Cloud e una condivisione file di Azure. (mapping 1:1)
    * Facoltativamente, aggiungere un server locale come cache delle prestazioni locale con la suddivisione in livelli nel cloud abilitata. Questo passaggio è necessario se si vuole sostituire il StorSimple locale con una cache locale più ricca di funzionalità, basata su Windows Server e la suddivisione in livelli cloud Sincronizzazione file di Azure. Windows Server locale può essere un computer fisico o un cluster o una macchina virtuale. Non è necessario che la quantità di spazio di archiviazione sia distribuita come dimensione del set di dati. Richiede solo spazio di archiviazione sufficiente per memorizzare nella cache locale i file a cui si accede più di frequente.

## <a name="minimizing-downtime"></a>Riduzione del tempo di inattività
Dopo il passaggio 3, l'appliance locale StorSimple viene ancora usata attivamente dagli utenti e dalle applicazioni. Il set di file sincronizzati dal clone del volume iniziale è quindi leggermente obsoleto al termine della sincronizzazione.
L'approccio per ridurre al minimo i tempi di inattività consiste nel ripetere la sincronizzazione dal processo di clonazione del volume, in modo che la sincronizzazione venga completata più velocemente e più velocemente con ogni iterazione, che a sua volta è abilitata dalle modifiche tra i cloni del volume.
È possibile ripetere questo processo, fino a quando la sincronizzazione da un clone del volume non può terminare con la quantità di tempo che risulta accettabile per i tempi di inattività.
In tal caso, impedire a utenti e applicazioni di apportare modifiche al dispositivo StorSimple. Inizio del tempo di inattività.
Eseguire un altro clone del volume e consentire la sincronizzazione ai server connessi.
Consente di stabilire l'accesso agli utenti e alle applicazioni al nuovo server Windows Sincronizzazione file di Azure supportato.
Prendere in considerazione la distribuzione o la modifica di uno spazio dei nomi DFS per eseguire il ritaglio dal vecchio Appliance StorSimple al nuovo Windows Server trasparente per le app e gli utenti.
La migrazione è stata completata.

## <a name="migration-goal"></a>Obiettivo della migrazione
Al termine della migrazione, è possibile effettuare il deprovisioning dell'appliance virtuale StorSimple temporanea e della VM di Azure.

Inoltre, è possibile effettuare il deprovisioning dell'appliance locale di StorSimple perché gli utenti e le applicazioni accedono già al server Windows.
Ciò che si sta lasciando è illustrato nell'immagine seguente. Una distribuzione di Sincronizzazione file di Azure standard offre un numero di condivisioni file di Azure e server Windows connessi tramite Sincronizzazione file di Azure. Tenere presente che un singolo server è in grado di connettere diverse cartelle locali a condivisioni file diverse nello stesso momento.
Inoltre, una condivisione file di Azure può essere sincronizzata con molti server diversi, nel caso in cui siano necessari dati memorizzati nella cache nelle succursali. Controllare anche se è possibile ottimizzare i criteri di suddivisione in livelli nel cloud per un uso più efficiente dello spazio di archiviazione locale.

![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Illustrazione che mostra l'obiettivo al termine della migrazione. Viene illustrato un numero di condivisioni file che si sincronizzano con un server Windows locale con utenti e applicazioni che accedono ai file nel cloud o in Windows Server.")

## <a name="next-steps"></a>Passaggi successivi
Acquisire familiarità con File di Azure e Sincronizzazione file di Azure. È importante comprendere la terminologia Sincronizzazione file di Azure e il modello di distribuzione per una migrazione corretta. Per ogni passaggio di questo articolo introduttivo sono disponibili informazioni più dettagliate. Assicurarsi di contattare Microsoft per ottenere assistenza personalizzata durante la pianificazione e l'esecuzione della migrazione.

> [!IMPORTANT]
> Microsoft si impegna a supportare i clienti nella migrazione. AzureFiles@microsoft.com di posta elettronica per un piano di migrazione personalizzato, nonché per assistenza durante la migrazione.

## <a name="additional-resources"></a>Risorse aggiuntive
Sincronizzazione file di Azure, come servizio di destinazione, dispone di due documenti fondamentali che è consigliabile leggere, se non si ha familiarità con Sincronizzazione file di Azure.
* [Sincronizzazione file di Azure-Panoramica](storage-sync-files-planning.md)
* [Guida alla distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

File di Azure è un servizio di archiviazione in Azure, che offre condivisioni file come servizio. Non è necessario pagare o gestire una macchina virtuale o un archivio VM associato.
* [File di Azure-Panoramica](storage-files-introduction.md)
* [File di Azure-come distribuire una condivisione file di Azure](storage-how-to-create-file-share.md)