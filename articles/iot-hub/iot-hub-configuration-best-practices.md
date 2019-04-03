---
title: Procedure consigliate per la configurazione di dispositivi per l'hub IoT di Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate per configurare i dispositivi IoT su larga scala
author: chrisgre
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: c97395981ea3af90c7b0c590cb049fccc7392304
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863067"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Procedure consigliate per la configurazione di dispositivi in una soluzione IoT

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse per la gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. Questo articolo definisce molte delle procedure consigliate per i vari ruoli coinvolti nello sviluppo e nel funzionamento di una soluzione IoT.

* **Integratore/produttore di hardware IoT:** I produttori di hardware IoT, gli integratori di assemblaggio di hardware da produttori diversi, oppure di fornitori per la distribuzione IoT prodotta o integrata da altri fornitori. Coloro che svolgono questo ruolo sono coinvolti nello sviluppo e nell'integrazione di firmware, sistemi operativi incorporati e software incorporato.

* **Sviluppatore di soluzioni IoT:** Lo sviluppo di una soluzione IoT viene in genere eseguito da uno sviluppatore di soluzioni. che può far parte di un team interno o essere un integratore di sistemi specializzato in questa attività. Lo sviluppatore di soluzioni IoT può realizzare vari componenti della soluzione IoT partendo da zero, integrare vari componenti standard o open source oppure personalizzare un [acceleratore di soluzioni IoT](/azure/iot-accelerators/).

* **Operatore di soluzioni IoT:** Dopo aver distribuita la soluzione IoT, richiede operazioni a lungo termine, il monitoraggio, aggiornamenti e manutenzione. Queste operazioni possono essere eseguite da un team interno costituito da esperti di tecnologie informatiche, da team che svolgono operazioni hardware e attività di manutenzione, nonché da specialisti di dominio che monitorano il corretto funzionamento dell'intera infrastruttura IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Informazioni sulla gestione automatica dei dispositivi per la configurazione di dispositivi IoT su larga scala

La gestione automatica dei dispositivi include i numerosi vantaggi offerti dai [dispositivi gemelli](iot-hub-devguide-device-twins.md) e dai [moduli gemelli](iot-hub-devguide-module-twins.md) per sincronizzare gli stati desiderati e segnalati tra il cloud e i dispositivi. [Le configurazioni del dispositivo automatica](iot-hub-auto-device-config.md) aggiornare grandi insiemi di gemelli automaticamente e riepiloga lo stato di avanzamento e la conformità. I seguenti passaggi generali descrivono come viene sviluppata e usata la gestione automatica dei dispositivi:

* Il **produttore/integratore di hardware IoT** implementa le funzionalità di gestione dei dispositivi all'interno di un'applicazione incorporata usando [dispositivi gemelli](iot-hub-devguide-device-twins.md). Queste funzionalità possono includere gli aggiornamenti del firmware, l'installazione e l'aggiornamento del software e la gestione delle impostazioni.

* Lo **sviluppatore di soluzioni IoT** implementa il livello di gestione per le operazioni di gestione dei dispositivi usando [dispositivi gemelli](iot-hub-devguide-device-twins.md) e [configurazioni automatiche dei dispositivi](iot-hub-auto-device-config.md). La soluzione deve includere la definizione di un'interfaccia per operatore per eseguire le attività di gestione dei dispositivi.

* L'**operatore di soluzioni IoT** usa la soluzione IoT per eseguire attività di gestione dei dispositivi, in particolare per raggruppare i dispositivi, avviare modifiche di configurazione, ad esempio aggiornamenti al firmware, monitorare lo stato di avanzamento e risolvere i problemi che si verificano.

## <a name="iot-hardware-manufacturerintegrator"></a>Produttore/integratore di hardware IoT

Di seguito sono presentate le procedure consigliate per i produttori e gli integratori di hardware che si occupano dello sviluppo di software incorporato:

* **Implementare [sui dispositivi gemelli](iot-hub-devguide-device-twins.md):** Dispositivi gemelli abilita la sincronizzazione configurazione desiderata dal cloud e creare report di configurazione corrente e le proprietà del dispositivo. Il modo migliore per implementare dispositivi gemelli all'interno di applicazioni incorporate consiste nell'uso degli [SDK per IoT di Azure](https://github.com/Azure/azure-iot-sdks). I dispositivi gemelli sono particolarmente adatti alla configurazione per i motivi seguenti:

    * Supportano le comunicazioni bidirezionali.
    * Consentono lo stato connesso e disconnesso dei dispositivi.
    * Seguono il principio della coerenza finale.
    * Sono completamente disponibili per query nel cloud.

* **Struttura del dispositivo gemello per la gestione dei dispositivi:** Il dispositivo gemello debba essere strutturato in modo che le proprietà di gestione di dispositivi sono raggruppate logicamente in sezioni. In questo modo, le modifiche di configurazione possono essere isolate senza alcun impatto sulle altre sezioni del dispositivo gemello. È ad esempio possibile creare una sezione all'interno delle proprietà desiderate per il firmware, un'altra sezione per il software e una terza sezione per le impostazioni di rete. 

* **Attributi del dispositivo report utili per la gestione dei dispositivi:** Marca e modello, firmware, sistema operativo, il numero di serie di attributi come dispositivo fisico e altri identificatori sono utili per il reporting e come parametri per specificare come destinazione le modifiche alla configurazione.

* **Definire gli stati principali per la segnalazione dello stato e lo stato di avanzamento:** Stati di livello principali devono essere enumerati in modo che essi possono essere segnalati all'operatore. Un aggiornamento del firmware può ad esempio essere segnalato come corrente, in fase di download, in fase di applicazione, in corso e in stato di errore. Definire campi aggiuntivi per altre informazioni su ogni stato.

## <a name="iot-solution-developer"></a>Sviluppatore di soluzioni IoT

Di seguito sono presentate le procedure consigliate per gli sviluppatori di soluzioni IoT che creano sistemi basati su Azure:

* **Implementare [sui dispositivi gemelli](iot-hub-devguide-device-twins.md):** Dispositivi gemelli abilita la sincronizzazione configurazione desiderata dal cloud e creare report di configurazione corrente e le proprietà del dispositivo. Il modo migliore per implementare dispositivi gemelli all'interno di applicazioni di soluzioni cloud consiste nell'uso degli [SDK per IoT di Azure](https://github.com/Azure/azure-iot-sdks). I dispositivi gemelli sono particolarmente adatti alla configurazione per i motivi seguenti:

    * Supportano le comunicazioni bidirezionali.
    * Consentono lo stato connesso e disconnesso dei dispositivi.
    * Seguono il principio della coerenza finale.
    * Sono completamente disponibili per query nel cloud.

* **Organizzare i dispositivi tramite tag del dispositivo gemello:** La soluzione deve consentire l'operatore da definire gli anelli di qualità o altri set di dispositivi in base a varie strategie di distribuzione, ad esempio canary. L'organizzazione dei dispositivi può essere implementata all'interno della soluzione usando tag dei dispositivi gemelli e [query](iot-hub-devguide-query-language.md). Questa organizzazione è necessaria per un'implementazione precisa e sicura delle configurazioni.

* **Implementare [configurazioni del dispositivo automatica](iot-hub-auto-device-config.md):** Distribuire le configurazioni del dispositivo automatica e modifiche di configurazione di monitoraggio per grandi set di dispositivi IoT tramite i dispositivi gemelli. Le configurazioni automatiche dei dispositivi vengono assegnate a set di dispositivi gemelli tramite la **condizione di destinazione**, che consiste in una query sulle proprietà segnalate o sui tag dei dispositivi gemelli. Il **contenuto di destinazione** è il set di proprietà desiderate che verrà impostato all'interno dei dispositivi gemelli di destinazione. Il contenuto di destinazione deve essere allineato con la struttura dei dispositivi gemelli definita dal produttore/integratore dell'hardware IoT.

   Le **metriche** sono query sulle proprietà segnalate dei dispositivi gemelli e devono anch'esse essere allineate con la struttura dei dispositivi gemelli definita dal produttore/integratore dell'hardware IoT. Le configurazioni automatiche dei dispositivi offrono anche il vantaggio di configurare l'hub IoT in modo da eseguire operazioni sui dispositivi gemelli a una velocità che non superi mai le [limitazioni](iot-hub-devguide-quotas-throttling.md) relative alle letture e agli aggiornamenti di tali dispositivi.

* **Usare la [il servizio Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** Gli sviluppatori di soluzioni devono usare il servizio Device Provisioning per assegnare tag del dispositivo gemello a nuovi dispositivi, in modo che verranno configurati automaticamente da **configurazioni del dispositivo automatica** che sono destinate ai dispositivi gemelli con tale tag. 

## <a name="iot-solution-operator"></a>Operatore di soluzioni IoT

Di seguito sono presentate le procedure consigliate per gli operatori di soluzioni IoT che usano una soluzione IoT basata su Azure:

* **Organizzare i dispositivi per la gestione:** La soluzione IoT deve definire o consentire la creazione di fasi di qualità o altri set di dispositivi in base a varie strategie di distribuzione, ad esempio canary. I set di dispositivi verranno usati per implementare le modifiche di configurazione ed eseguire altre operazioni di gestione dei dispositivi su larga scala.

* **Eseguire modifiche di configurazione usando un'implementazione a fasi:**  Un'implementazione a fasi è un processo in base al quale un operatore distribuisce le modifiche a un set più ampio di dispositivi IoT. L'obiettivo è apportare le modifiche gradualmente per ridurre il rischio di introdurre modifiche che causano disservizi su larga scala.  L'operatore deve usare l'interfaccia della soluzione per creare una [configurazione automatica dei dispositivi](iot-hub-auto-device-config.md) e la condizione di destinazione deve essere assegnata a un set iniziale di dispositivi, ad esempio un gruppo canary. L'operatore deve quindi convalidare la modifica della configurazione nel set iniziale di dispositivi.

   Al termine della convalida, l'operatore aggiornerà la configurazione automatica dei dispositivi in modo da includere un set di dispositivi più ampio. L'operatore deve inoltre impostare la priorità per la configurazione in modo che sia superiore alle altre configurazioni attualmente assegnate a tali dispositivi. L'implementazione può essere monitorata tramite le metriche definite dalla configurazione automatica dei dispositivi.

* **Eseguire i rollback in caso di errori o problemi di configurazione:**  Una configurazione automatica dei dispositivi che causa errori o problemi di configurazione può essere eseguito il rollback modificando la **condizione di destinazione** in modo che i dispositivi non soddisfino più la condizione di destinazione. Assicurarsi che un'altra configurazione automatica dei dispositivi con priorità inferiore rimanga assegnata a tali dispositivi. Verificare che il rollback ha avuto esito positivo, visualizzare le metriche: La configurazione di rollback non deve più visualizzare lo stato per i dispositivi di divulgazione e le metriche della seconda configurazione dovrebbero ora includere i conteggi per i dispositivi ancora interessati.

## <a name="next-steps"></a>Passaggi successivi

* Leggere altre informazioni sull'implementazione di dispositivi gemelli in [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

* Eseguire i passaggi per creare, aggiornare o eliminare una configurazione automatica dei dispositivi in [Configurare e monitorare i dispositivi IoT su larga scala](iot-hub-auto-device-config.md).

* Implementare un modello di aggiornamento del firmware usando i dispositivi gemelli e configurazioni automatica dei dispositivi in [esercitazione: Implementare un processo di aggiornamento del firmware dispositivo](tutorial-firmware-update.md).
