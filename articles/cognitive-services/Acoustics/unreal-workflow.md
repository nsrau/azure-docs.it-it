---
title: Esercitazione sulla progettazione con Unreal in Progetto Acustica
titlesuffix: Azure Cognitive Services
description: Questa esercitazione descrive il flusso di lavoro della progettazione per Progetto Acustica in Unreal e Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 57bde67ac2259b3847f59f95eaefba9c6fddf13e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316202"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Esercitazione sulla progettazione con Unreal/Wwise in Progetto Acustica
Questa esercitazione descrive il flusso di lavoro e la configurazione della progettazione per Progetto Acustica in Unreal e Wwise.

Prerequisiti software:
* Progetto Unreal con i plug-in Wwise e Unreal di Progetto Acustica

Per ottenere un progetto Unreal con Progetto Acustica, è possibile:
* Seguire le istruzioni per l'[integrazione di Unreal con Progetto Acustica](unreal-integration.md) per aggiungere Progetto Acustica a un progetto Unreal
* In alternativa, usare il [progetto di esempio di Progetto Acustica](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Configurare le proprietà di Wwise a livello di progetto
Wwise ha curve globali di ostruzione e occlusione che influenzano il modo in cui il plugin Progetto Acustica usa il DSP audio di Wwise.

### <a name="design-wwise-occlusion-curves"></a>Progettare le curve di occlusione di Wwise
Quando Progetto Acustica è attivo, risponde alle curve del volume di occlusione, del filtro passa-basso (LPF) e del filtro passa-alto (HPF) impostate in Wwise. È consigliabile impostare il tipo di curva di volume su lineare con un valore di -100 dB per un valore di occlusione di 100.

Con questa impostazione, se la simulazione di Progetto Acustica calcola un'occlusione di -18 dB, questa verrà immessa nella curva sottostante in X=18, e il valore Y corrispondente sarà l'attenuazione applicata. Per avere un'occlusione ridotta, impostare l'endpoint su -50 dB invece di -100 dB o su -200 dB per avere un'occlusione estrema. È possibile personalizzare e ottimizzare qualsiasi curva che meglio si adatta al gioco.
 
![Screenshot dell'editor con la curva di occlusione Wwise](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Disabilitare le curve di ostruzione Wwise
Le curve di ostruzione Wwise influenzano il livello di dry in isolamento, ma Progetto Acustica usa i controlli di progettazione e la simulazione per applicare i rapporti wet/dry. È consigliabile disabilitare la curva del volume di ostruzione. Per progettare l'intensità del riverbero, usare il controllo Wetness Adjust (Regolazione intensità riverbero) descritto più avanti.
 
Se si usano le curve LPF/HPF di ostruzione per altri scopi, assicurarsi di averle impostate su Y=0 in X=0 (ovvero nessuna curva LPF o HPF in assenza di ostruzione).

![Screenshot dell'editor con la curva di ostruzione Wwise](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Progettare i parametri del mixer di Progetto Acustica
È possibile controllare le proprietà globali del riverbero nella scheda plug-in mixer del bus di Progetto Acustica. Fare doppio clic su "Project Acoustics Mixer (Custom)" (Mixer Progetto Acustica - Personalizzato) per aprire il pannello delle impostazioni del plug-in mixer.

Si può vedere che il plug-in mixer ha anche un'opzione "Perform Spatialization" (Esegui spazializzazione). Se si preferisce usare la spazializzazione integrata di Progetto Acustica, selezionare la casella di controllo "Perform Spatialization" (Esegui spazializzazione) e scegliere tra HRTF o Panning (Panoramica). Assicurarsi di disabilitare tutti i bus Dry Aux configurati, in caso contrario si ascolterà il percorso diretto due volte. Usare "Wetness Adjust" (Regolazione intensità riverbero) e "Reverb Time Scale Factor" (Fattore di scala temporale del riverbero) per esercitare un controllo globale sul mix del riverbero. Tenere presente che è necessario riavviare Unreal, quindi rigenerare le soundbanks prima di premere play per selezionare le modifiche di configurazione del plug-in mixer, come la casella di controllo 'Perform Spatialization' (Esegui spazializzazione).

![Screenshot delle opzioni del plug-in mixer di Progetto Acustica](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Impostare i controlli di progettazione di Progetto Acustica nella gerarchia Actor-Mixer di Wwise
Per controllare i parametri di un singolo Actor-Mixer, fare doppio clic su Actor-Mixer, quindi fare clic sulla scheda Mixer Plug-in (Plug-in mixer). In questa scheda è possibile modificare i parametri a livello audio. Questi valori si combinano con quelli impostati dal lato Unreal (descritti di seguito). Ad esempio, se il plugin Progetto Acustica Unreal imposta Outdoorness Adjustment (Regolazione effetto all'aperto) per un oggetto su 0.5 e Wwise lo imposta su -0.25, la regolazione dell'effetto all'aperto risultante applicata a quel suono sarà 0.25.

![Screenshot delle impostazioni del mixer audio nella gerarchia Actor-Mixer Wwise](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Assicurarsi che il bus aux abbia l'invio dry e che il bus di uscita abbia l'invio wet
Tenere presente che la configurazione di Actor-Mixer richiesta scambia il normale routing dry e wet in Wwise. Genera il segnale di riverbero sul bus di uscita di Actor-Mixer (impostato su Project Acoustics Bus) e il segnale dry sul bus aux definito dall'utente. Questo routing è necessario a causa delle caratteristiche dell'API del plug-in mixer Wwise usata dal plugin Wwise Progetto Acustica.

![Screenshot dell'editor Wwise che mostra le linee guida per la progettazione della voce per Progetto Acustica](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Configurare le curve di attenuazione della distanza
Assicurarsi che qualsiasi curva di attenuazione usata dagli actor-mixer che usano Progetto Acustica abbia un invio aux definito dall'utente impostato su "volume del bus di uscita". Wwise esegue questa operazione per impostazione predefinita per le curve di attenuazione appena create. Se si esegue la migrazione di un progetto esistente, controllare le impostazioni della curva. 

Per impostazione predefinita, la simulazione di Progetto Acustica ha un raggio di 45 metri per la posizione del giocatore. È in genere consigliabile impostare la curva di attenuazione su -200 dB per tale distanza. Questa distanza non è un vincolo rigido. Per alcuni suoni come le armi si potrebbe volere un raggio maggiore. In questi casi, il problema è che farà parte solo una geometria entro 45 m dalla posizione del giocatore. Se il giocatore si trova in una stanza e una sorgente sonora si trova al di fuori della stanza a 100 metri di distanza, sarà correttamente bloccata. Se la sorgente sonora si trova in una stanza e il giocatore è al di fuori a 100 metri di distanza, non verrà bloccata correttamente.

![Screenshot delle curve di attenuazione Wwise](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Configurare le proprietà di Progetto Acustica a livello di scena

L'attore Acoustics Space espone molti controlli che modificano il comportamento del sistema e sono utili per il debug.

![Screenshot dei controlli Acoustics Space in Unreal](media/acoustics-space-controls.png)

* **Acoustics Data** (Dati di acustica): a questo campo deve essere assegnato un asset di acustica di cui è stato effettuato il bake dalla directory Content/Acoustics. Il plugin Progetto Acustica aggiungerà automaticamente la directory Content/Acoustics alle directory incluse nel pacchetto del progetto.
* **Tile size** (Dimensioni riquadro): estensioni dell'area intorno al listener di cui si vogliono caricare i dati di acustica nella RAM. Finché vengono caricati i probe del listener immediatamente intorno al giocatore, i risultati sono uguali a quelli del caricamento dei dati di acustica per tutti i probe. Riquadri più grandi usano più RAM, ma riducono il numero di operazioni di I/O su disco.
* **Auto Stream** (Streaming automatico): se abilitato, carica automaticamente i nuovi riquadri quando il listener raggiunge il bordo di un'area caricata. Se disabilitato, è necessario caricare manualmente i nuovi riquadri tramite il codice o blueprints.
* **Cache Scale** (Scalabilità della cache): controlla le dimensioni della cache usata per le query di acustica. Una cache più piccola usa meno RAM, ma può aumentare l'utilizzo della CPU per ogni query.
* **Acoustics Enabled** (Acustica abilitata): controllo di debug per consentire una rapida attivazione/disattivazione A/B della simulazione acustica. Questo controllo viene ignorato nelle configurazioni di spedizione. Il controllo è utile per trovare se un particolare bug audio ha origine nei calcoli acustici o qualche altro problema nel progetto Wwise.
* **Update Distances** (Aggiorna distanze): usare questa opzione se si vogliono usare le informazioni acustiche di cui è stato effettuato il pre-bake per le query di distanza. Queste query sono simili ai ray cast, ma sono state pre-calcolate, quindi richiedono molta meno CPU. Un esempio di utilizzo è per i riflessi discreti lontani dalla superficie più vicina al listener. Per sfruttare appieno questa opzione, è necessario usare il codice o Blueprints per eseguire query sulle distanze.
* **Draw Stats** (Disegna statistiche): mentre `stat Acoustics` di UE può fornire informazioni sulla CPU, questa visualizzazione di stato mostrerà la mappa attualmente caricata, l'utilizzo della RAM e altre informazioni di stato nella parte superiore sinistra della schermata.
* **Draw Voxels** (Disegna voxel): sovrappone i voxel vicino al listener mostrando la griglia di voxel usata durante l'interpolazione in runtime. Se un emettitore si trova all'interno di un voxel di runtime, non riuscirà a eseguire le query acustiche.
* **Draw Probes** (Disegna probe): mostra tutti probe per questa scena. Saranno di colori diversi a seconda del relativo stato di caricamento.
* **Draw Distances** (Disegna distanze): se è abilitata l'opzione Aggiorna distanze, questa opzione mostrerà un riquadro sulla superficie più vicina al listener in direzioni quantizzate intorno al listener.

## <a name="actor-specific-acoustics-design-controls"></a>Controlli di progettazione acustica specifici per l'attore
Questi controlli di progettazione sono limitati a un singolo componente audio in Unreal.

![Screenshot dei controlli Audio Component in Unreal](media/audio-component-controls.png)

* **Occlusion Multiplier** (Moltiplicatore di occlusione): controlla l'effetto di occlusione. I valori > 1 amplificheranno l'occlusione. I valori < 1 la ridurranno.
* **Wetness Adjustment** (Regolazione intensità riverbero): dB aggiuntivi di riverbero
* **Decay Time Multiplier** (Moltiplicatore tempo di decadimento): controlla l'RT60 in modo moltiplicativo, in base all'output della simulazione acustica
* **Outdoorness Adjustment** (Regolazione effetto all'aperto): controlla come è il riverbero all'aperto. I valori più vicini a 0 sono più per ambienti interni, i valori più vicini a 1 sono più per ambienti esterni. Questa regolazione è additiva, quindi impostando il valore su -1 si applicheranno effetti per ambienti interni, impostandolo su +1 si applicheranno effetti per ambienti esterni.
* **Transmission Db** (Db di trasmissione): esegue il rendering di un suono aggiuntivo attraverso la parete con questo volume combinato con l'attenuazione della distanza basata sulla linea di vista.
* **Wet Ratio Distance Warp** (Distorsione della distanza rapporto wet): regola le caratteristiche di riverbero sulla sorgente come se fosse più vicino o più lontano, senza influenzare il percorso diretto.
* **Show Acoustic Parameters** (Mostra parametri acustici): visualizza le informazioni di debug direttamente nella parte superiore del componente nel gioco (solo per configurazioni non di spedizione).

## <a name="blueprint-functionality"></a>Funzionalità Blueprint
L'attore Acoustics Space è accessibile tramite blueprint, fornendo funzionalità come il caricamento di una mappa o la modifica delle impostazioni tramite la creazione di script a livello. Di seguito sono riportati due esempi.

### <a name="add-finer-grained-control-over-streaming-load"></a>Aggiungere un controllo più capillare sul caricamento in streaming
Per gestire da soli lo streaming dei dati acustici invece che lo streaming automatico in base alla posizione del giocatore, è possibile usare la funzione blueprint Force Load Tile (Forza caricamento riquadro):

![Screenshot delle opzioni di streaming blueprint in Unreal](media/blueprint-streaming.png)

* **Target** (Destinazione): attore AcousticsSpace
* **Center Position** (Posizione centrale): il centro dell'area in cui è necessario caricare i dati
* **Unload Probes Outside Tile** (Scaricare i probe all'esterno del riquadro): se selezionata, tutti i probe non presenti nella nuova area verranno scaricati dalla RAM. Se deselezionata, la nuova area viene caricata in memoria lasciando in memoria anche i probe esistenti.
* **Block on Completion** (Blocca al completamento): rende il caricamento del riquadro un'operazione sincrona

Le dimensioni del riquadro devono essere già impostate prima di chiamare Force Load Tile (Forza caricamento riquadro). Ad esempio, è possibile eseguire un'operazione simile per caricare un file ACE, impostare le dimensioni del riquadro ed eseguire lo streaming in un'area:

![Screenshot delle opzioni di configurazione dello streaming in Unreal](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Facoltativamente eseguire una query per la prossimità della superficie
Se si vuole vedere quanto sono vicine le superfici in una particolare direzione intorno al listener, è possibile usare la funzione Query Distance. Questa funzione può essere utile per la guida di riflessi ritardati direzionali o per altre logiche di gioco basate sulla prossimità della superficie. La query è meno costosa di un ray-cast perché i risultati sono ricavati dalla tabella di ricerca acustica.

![Screenshot dell'esempio di query sulle distanze di blueprint](media/distance-query.png)

* **Target** (Destinazione): attore AcousticsSpace
* **Look Direction** (Cerca direzione): direzione in cui eseguire una query, con al centro il listener
* **Distance** (Distanza): se la query ha esito positivo, la distanza dalla superficie più vicina
* **Return Value** (Valore restituito): valore booleano: true se la query è riuscita; in caso contrario, false

## <a name="next-steps"></a>Passaggi successivi
* Approfondire i concetti alla base del [processo di progettazione](design-process.md)
* [Creare un account di Azure](create-azure-account.md) per effettuare il bake di una propria scena


