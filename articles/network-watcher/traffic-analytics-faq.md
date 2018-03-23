---
title: Domande frequenti su Analisi del traffico di Azure | Microsoft Docs
description: Ottenere le risposte ad alcune delle domande più frequenti su Analisi del traffico.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: fd97e0ca7615691c537dcb1dc18643627046742d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Domande frequenti su Analisi del traffico

1.  Quali sono i prerequisiti per usare Analisi del traffico?

    Analisi del traffico prevede i prerequisiti seguenti:

    - Una sottoscrizione abilitata per Network Watcher
    - Log dei flussi dei gruppi di sicurezza di rete abilitati per i gruppi di sicurezza di rete da monitorare
    - Un account di archiviazione di Azure per archiviare i log dei flussi non elaborati
    - Un'area di lavoro Log Analytics (OMS) con accesso in lettura e scrittura

2.  In quali aree è disponibile Analisi del traffico?

    Nella versione di anteprima è possibile usare Analisi del traffico per i gruppi di sicurezza di rete in tutte le **aree supportate** seguenti: Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali,Stati Uniti centrali, Stati Uniti occidentali, Stati Uniti occidentali 2, Europa occidentale, Europa settentrionale, Regno Unito occidentale, Regno Unito meridionale, Australia orientale e Australia sud-orientale. L'area di lavoro Log Analytics deve esistere nell'area Stati Uniti centro-occidentali, Stati Uniti orientali, Europa occidentale, Australia sud-orientale o Regno Unito meridionale.

3.  I gruppi di sicurezza di rete per cui vengono abilitati i log dei flussi possono trovarsi in aree differenti dalla propria area di lavoro OMS?

    Sì

4.  È possibile configurare più gruppi di sicurezza di rete in una singola area di lavoro?

    Sì

5.  È possibile usare un'area di lavoro OMS esistente?

    Sì. Se si seleziona un'area di lavoro esistente, assicurarsi che sia stata eseguita la migrazione al nuovo linguaggio di query. Se non si vuole aggiornare l'area di lavoro, è necessario crearne una nuova. Per altre informazioni sul nuovo linguaggio di query, vedere [Aggiornamento di Azure Log Analytics alla nuova ricerca log](../log-analytics/log-analytics-log-search-upgrade.md).

6.  È possibile usare sottoscrizioni diverse per l'account di archiviazione di Azure e l'area di lavoro OMS?

    Sì

7.  È possibile archiviare i log non elaborati in un altro account di archiviazione di una sottoscrizione differente?

    No. È possibile archiviare i log non elaborati in qualsiasi account di archiviazione in cui un gruppo di sicurezza di rete è abilitato per i log dei flussi, tuttavia sia l'account di archiviazione che i log non elaborati devono essere nella stessa sottoscrizione e area.

8.  Se si riceve un errore "Non trovato" durante la configurazione di un gruppo di sicurezza di rete per Analisi del traffico, come risolvere il problema?

    Selezionare un'area supportata tra quelle elencate alla domanda 2. Se si seleziona un'area non supportata, viene visualizzato un errore "Non trovato".

9.  Nei log dei flussi dei gruppi di sicurezza di rete viene visualizzato lo stato "Impossibile caricare". Cosa fare?

    Per il corretto funzionamento della registrazione dei flussi, è necessario registrare il provider Microsoft.Insights. Se non si è sicuri che il provider Microsoft.Insights sia registrato o meno per la sottoscrizione, sostituire *xxxxx-xxxxx-xxxxxx-xxxx* nel comando seguente ed eseguire questi comandi di PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. Ho configurato la soluzione. Perché non viene visualizzato nessun elemento nel dashboard?

    La prima visualizzazione del dashboard può richiedere fino a 30 minuti. Prima di generare i report, la soluzione deve aggregare dati sufficienti per poter ottenere informazioni significative. 

11.  È stato visualizzato il messaggio seguente: "Non sono stati trovati dati in questa area di lavoro per l'intervallo di tempo selezionato. Provare a modificare l'intervallo di tempo o selezionare un'altra area di lavoro". Come risolvere il problema?

        Provare le opzioni seguenti:
        - Provare a modificare l'intervallo di tempo nella barra superiore.
        - Selezionare una diversa area di lavoro di Log Analytics nella barra superiore
        - Provare ad accedere ad Analisi del traffico dopo 30 minuti, se è stata abilitata di recente
    
        Se i problemi persistono, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Vengono visualizzati i messaggi seguenti: "1) È in corso l'analisi dei log del flusso del gruppo di sicurezza di rete per la prima volta. L'operazione potrebbe richiedere circa 20-30 minuti. Verificare più tardi". 2) "Controllare qui l'utilizzo dell'area di lavoro per convalidare la quota in eccedenza (se l'area di lavoro si trova nello SKU gratuito)". Come risolvere il problema?

        Questo errore potrebbe verificarsi per i motivi seguenti:
        - Analisi del traffico potrebbe essere stato abilitato di recente ed è possibile che stia aggregando dati sufficienti per ricavare informazioni dettagliate significative prima di generare un report. In questo caso, riprovare dopo 30 minuti
        - L'area di lavoro di OMS è associata allo SKU gratuito e ha violato i limiti di quota. In questo caso, potrebbe essere necessario usare un'area di lavoro in uno SKU con capacità più elevata.
    
        Se i problemi persistono, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  È stato visualizzato il messaggio seguente: "Sono disponibili solo i dati delle risorse (topologia) e nessuna informazione sui flussi. Nel frattempo, fare clic per visualizzare solo i dati delle risorse". Come risolvere il problema?

        Le informazioni sulle risorse sono visualizzate nel dashboard, tuttavia non sono presenti le statistiche relative ai flussi. I dati potrebbero non essere presenti a causa della mancanza di flussi di comunicazione tra le risorse. Attendere 60 minuti e ricontrollare lo stato. Se si è certi dell'esistenza dei flussi di comunicazione tra le risorse, chiedere assistenza nel [forum di suggerimenti per gli utenti](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14.  Come viene determinato il prezzo di Analisi del traffico?

        Finché Analisi del traffico sarà in anteprima pubblica, non verrà effettuato alcun addebito. La generazione dei log dei flussi dei gruppi di sicurezza di rete e la conservazione dei dati in un'area di lavoro OMS sono addebitate in base alle tariffe pubblicate.

15.  Come è possibile spostarsi con la tastiera nella visualizzazione mappa geografica?

        La pagina mappa geografica contiene due sezioni principali:
    
        - **Banner**: il banner posizionato nella parte superiore della mappa geografica permette di selezionare i filtri di distribuzione del traffico con pulsanti come Distribuzione, Nessuna distribuzione, Attivo, Inattivo, Analisi del traffico abilitata, Analisi del traffico non abilitata, Traffico dai paesi, Di tipo non dannoso, Di tipo dannoso, Traffico dannoso consentito e le informazioni sulla legenda. Quando si selezionano i pulsanti definiti, viene applicato il rispettivo filtro alla mappa; ad esempio, se un utente seleziona il pulsante del filtro "Attivo" nel banner, nella mappa verranno evidenziati i data center "attivi" nella propria distribuzione.
        - **Mappa**: la sezione Mappa posizionata sotto il banner mostra la distribuzione del traffico tra i data center di Azure e i paesi.
    
        **Navigazione da tastiera sul banner**
    
        - Per impostazione predefinita, la selezione nella pagina mappa geografica per il banner è il pulsante di filtro "Data center di Azure".
        - Per passare a un altro pulsante di filtro, è possibile usare il tasto `Tab` o `Right arrow` per spostarsi in avanti. Per spostarsi all'indietro, usare il tasto `Shift+Tab` o `Left arrow`. La precedenza data alla direzione di navigazione in avanti è da sinistra a destra, seguita dalla direzione dall'alto verso il basso.
        - Premere il tasto di direzione `Enter` o `Down` per applicare il filtro selezionato. In base alla selezione e alla distribuzione del filtro, vengono evidenziati uno o più nodi nella sezione Mappa.
            - Per alternare tra **Banner** e **Mappa**, premere `Ctrl+F6`.
        
        **Navigazione da tastiera sulla mappa**
    
        - Dopo aver selezionato un filtro nel banner e premuto `Ctrl+F6`, lo stato attivo si sposta su uno dei nodi evidenziati (**Data Center di Azure** o **Paese/area geografica**) nella visualizzazione della mappa.
        - Per passare agli altri nodi evidenziati nella mappa è possibile usare i tasti `Tab` o `Right arrow` per spostarsi in avanti e i tasti `Shift+Tab` o `Left arrow` per spostarsi all'indietro.
        - Per selezionare qualsiasi nodo evidenziato nella mappa, usare il tasto `Enter` o `Down arrow`.
        - Selezionando uno di questi nodi, lo stato attivo si sposta sulla **casella degli strumenti Informazioni** relativa al nodo. Per impostazione predefinita, lo stato attivo passerà al pulsante di chiusura nella **casella degli strumenti Informazioni**. Per spostarsi ulteriormente all'interno della visualizzazione della **casella**, usare i tasti `Right` e `Left arrow` per andare in avanti o indietro. Premere `Enter` equivale a selezionare il pulsante con stato attivo nella **casella degli strumenti Informazioni**.
        - Premendo `Tab` mentre è attiva la **casella degli strumenti Informazioni**, lo stato attivo passa agli endpoint dello stesso continente del nodo selezionato. È possibile usare i tasti `Right` e `Left arrow` per spostarsi tra questi endpoint.
        - Per passare ad altri endpoint di flusso/cluster del continente, usare `Tab` per spostarsi in avanti e `Shift+Tab` per spostarsi all'indietro.
        - Quando lo stato attivo si trova su `Continent clusters`, usare i tasti di direzione `Enter` o `Down` per evidenziare gli endpoint all'interno del cluster del continente. Per spostarsi tra gli endpoint e il pulsante di chiusura nella casella Informazioni del cluster del continente, usare il tasto `Right` o `Left arrow` rispettivamente per il movimento in avanti e all'indietro. In qualsiasi endpoint è possibile usare `Shift+L` per passare alla linea di connessione dal nodo selezionato all'endpoint. Premendo `Shift+L` si passa all'endpoint selezionato.
        
        In qualsiasi fase:
    
        - `ESC` comprime la selezione espansa.
        - Il tasto `UP Arrow` esegue la stessa azione di `ESC`. Il tasto `Down arrow` esegue la stessa azione di `Enter`.
        - Usare `Shift+Plus` per fare zoom avanti e `Shift+Minus` per fare zoom indietro.