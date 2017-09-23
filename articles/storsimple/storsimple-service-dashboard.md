---
title: Dashboard del servizio StorSimple Manager | Microsoft Docs
description: "Viene descritto il dashboard del servizio StorSimple Manager e si illustra come utilizzarlo per monitorare l'integrità della soluzione StorSimple."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="use-the-storsimple-manager-service-dashboard"></a>Utilizzare il dashboard del servizio StorSimple Manager
## <a name="overview"></a>Panoramica
La pagina dashboard del servizio StorSimple Manager fornisce una visualizzazione di riepilogo di tutti i dispositivi connessi al servizio StorSimple Manager, evidenziando quelli che richiedono attenzione da parte dell'amministratore di sistema. Questa esercitazione introduce la pagina dashboard, illustra il contenuto e la funzione del dashboard e descrive le attività che si possono eseguire da questa pagina.

![Dashboard del servizio](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

Nel dashboard del servizio StorSimple Manager sono visualizzate le informazioni seguenti:

* Nell’area **grafico** è possibile visualizzare il grafico delle metriche pertinenti per i dispositivi. È possibile visualizzare l'archiviazione primaria (aggiunta in locale e a livelli) usata in tutti i dispositivi, nonché l'archiviazione cloud utilizzata dai dispositivi in un periodo di tempo. Utilizzare i controlli nell'angolo in alto a destra del grafico per specificare una scala temporale di 1 settimana, 1 mese, 3 mesi o 1 anno.
* La **Panoramica sull'utilizzo** illustra l'archiviazione primaria fornita e utilizzata da tutti i dispositivi rispetto all'archiviazione totale disponibile tra tutti i dispositivi. **Fornito** si riferisce alla quantità di spazio di archiviazione preparata e allocata per l'uso, mentre **Utilizzato** si riferisce all'uso dei volumi come visualizzato dagli iniziatori connessi ai dispositivi.
* L’area **avvisi** fornisce uno snapshot di tutti gli avvisi attivi per tutti i dispositivi, raggruppati in base alla gravità dell'avviso. Cliccando sul livello di gravità si apre la pagina **avvisi** , la quale è stata programmata per mostrare gli avvisi. Sulla pagina **avvisi** , è possibile fare clic su un singolo avviso per visualizzare ulteriori dettagli su tale avviso, incluse tutte le operazioni consigliate. È inoltre possibile cancellare l'avviso se il problema è stato risolto.
* L’area **processi** fornisce uno snapshot dei processi recenti tra tutti i dispositivi connessi al servizio. Sono disponibili dei collegamenti che possono essere utilizzati per esaminare i processi attualmente in corso, quelli non riusciti nelle ultime 24 ore oppure quelli pianificati per l'esecuzione nelle 24 ore successive.
* L’area di **riepilogo rapido** fornisce informazioni utili, ad esempio stato del servizio, numero di dispositivi connessi al servizio, il percorso del servizio e i dettagli della sottoscrizione associata al servizio. Viene inoltre fornito un collegamento al log delle operazioni. Fare clic sul collegamento per visualizzare un elenco di tutte le operazioni completate del servizio StorSimple Manager.

È possibile usare il servizio StorSimple Manager per eseguire le attività seguenti:

* Visualizzare o rigenerare la chiave di registrazione del servizio.
* Modificare la chiave DEK del servizio
* Visualizzazione dei log delle operazioni.

## <a name="view-or-regenerate-the-service-registration-key"></a>Visualizzare o rigenerare la chiave di registrazione del servizio
Il codice di registrazione del servizio viene utilizzato per registrare un dispositivo Microsoft Azure StorSimple con il servizio StorSimple Manager, di modo che il dispositivo venga visualizzato nel portale di Azure classico per altre azioni di gestione. La chiave viene creata sul primo dispositivo e condiviso con il resto dei propri dispositivi.

Facendo clic su **Chiave di registrazione** nella parte inferiore della pagina, è possibile aprire la finestra di dialogo **Chiave di registrazione del servizio**, dalla quale è possibile copiare la chiave di registrazione del servizio corrente negli Appunti o rigenerare il codice di registrazione del servizio.

La rigenerazione della chiave non influisce sui dispositivi registrati in precedenza: sarà valida solo per i dispositivi registrati con il servizio dopo che la chiave viene rigenerata.

Per ulteriori informazioni sulla visualizzazione e la generazione della chiave di registrazione del servizio, visitare [ottenere la chiave di registrazione](storsimple-manage-service.md#get-the-service-registration-key)

## <a name="change-the-service-data-encryption-key"></a>Modificare la chiave DEK del servizio
Le chiavi DEK del servizio vengono utilizzate per crittografare dati riservati dei clienti, ad esempio credenziali di account di archiviazione, che vengono inviate dal servizio StorSimple Manager al dispositivo StorSimple. Occorre modificare queste chiavi periodicamente, se l'organizzazione IT dispone di un criterio di rotazione delle chiavi nei dispositivi di archiviazione. Il processo di modifica della chiave può essere leggermente diverso a seconda che ci sia una o più dispositivi gestiti dal servizio StorSimple Manager.

La modifica della chiave DEK del servizio è un processo che prevede 3 fasi:

1. Mediante il portale di Azure classico, si autorizza un dispositivo a modificare la chiave DEK nel portale di gestione.
2. Mediante Windows PowerShell per StorSimple, si consente di avviare la modifica della chiave DEK del servizio.
3. Se si dispone di più di un dispositivo StorSimple, è necessario aggiornare la chiave DEK del servizio in altri dispositivi.

I passaggi seguenti descrivono il processo di rollover per la chiave DEK del servizio.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>Visualizzazione dei log delle operazioni
È possibile visualizzare il log delle operazioni facendo clic sul collegamento dei logo delle operazione disponibile nel riquadro **riepilogo rapido** del dashboard. Verrà visualizzata la pagina Servizi di gestione, dove è possibile filtrare e visualizzare i registri specifici per il servizio StorSimple Manager.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [risolvere i problemi relativi a un dispositivo StorSimple](storsimple-troubleshoot-operational-device.md).
* Ulteriori informazioni sull’ [utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).


