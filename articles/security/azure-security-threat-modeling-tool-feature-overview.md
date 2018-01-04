---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: "Informazioni su tutte le funzionalità disponibili in Threat Modeling Tool"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="threat-modeling-tool-feature-overview"></a>Panoramica della funzione Threat Modeling Tool

Threat Modeling Tool offre supporto in ambito di modellazione delle minacce. Per un'introduzione allo strumento, vedere [Guida introduttiva a Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Threat Modeling Tool viene aggiornato spesso, perciò si consiglia di controllare questa guida di frequente per vedere le ultime funzionalità e miglioramenti.

Per aprire una pagina vuota, selezionare **Creare un modello**.

![Pagina vuota](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Per vedere tutte le funzionalità attualmente disponibili nello strumento, usare il modello di minaccia creato dal nostro team nell'esempio di [introduzione](./azure-security-threat-modeling-tool-getting-started.md).

![Modello di minaccia di base](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigazione

Prima di illustrare le funzionalità integrate, è opportuno discutere dei componenti principali presenti nello strumento.

### <a name="menu-items"></a>Voci di menu

L'esperienza è simile ad altri prodotti Microsoft. Esaminare le voci di menu di primo livello.

![Voci di menu](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Etichetta                               | Dettagli      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Aprire, salvare e chiudere i file</li><li>Accedere e disconnettersi dagli account di OneDrive.</li><li>Condividere collegamenti (visualizzazione e modifica).</li><li>Visualizzare le informazioni di file.</li><li>Applicare un nuovo modello a modelli esistenti.</li></ul> |
| **Modifica** | Annullare e ripristinare azioni, copiare, incollare ed eliminare. |
| **Visualizza** | <ul><li>Passare fra le visualizzazioni **Analisi** e **Progettazione**.</li><li>Aprire finestre chiuse (ad esempio stencil, proprietà degli elementi e messaggi).</li><li>Ripristinare le impostazioni predefinite del layout.</li></ul> |
| **Diagramma** | Aggiungere ed eliminare diagrammi e spostarsi tra le schede dei diagrammi. |
| **Report** | Creare report HTML da condividere con altri utenti. |
| **Guida** | Individuare guide sull'uso dello strumento. |

I simboli sono collegamenti ai menu di primo livello:

| Simbolo                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Apri** | Apre un nuovo file. |
| **Salva** | Salva il file attuale. |
| **Progettazione** | Passa alla visualizzazione **Progettazione** dove è possibile creare modelli. |
| **Analizzare** | Mostra le minacce generate e le relative proprietà. |
| **Aggiungi diagramma** | Aggiunge un nuovo diagramma (simile a nuove schede di Excel). |
| **Elimina diagramma** | Elimina il diagramma attuale. |
| **Copia/Taglia/Incolla** | Copia, taglia e incolla gli elementi. |
| **Annulla/Ripeti** | Annulla e ripete azioni. |
| **Zoom avanti/Zoom indietro** | Esegue lo zoom avanti e indietro nel diagramma per una migliore visualizzazione. |
| **Commenti e suggerimenti** | Apre il forum MSDN. |

### <a name="canvas"></a>Canvas

Spazio in cui si trascinano gli elementi. Il trascinamento è il modo più rapido ed efficiente per creare i modelli. È anche possibile fare clic con il pulsante destro del mouse e scegliere voci dal menu per aggiungere versioni generiche di elementi, come illustrato:

#### <a name="drop-the-stencil-on-the-canvas"></a>Rilascio dello stencil sul canvas

![Rilascio sul canvas](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selezione dello stencil

![Proprietà dell'elemento](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stencil

In base al modello selezionato è possibile trovare tutti gli stencil disponibili per l'uso. Se non si riesce a trovare gli elementi adatti, usare un altro modello. È anche possibile modificare un modello secondo le proprie esigenze. In genere è possibile trovare una combinazione di categorie come quelle indicati di seguito:

| Nome dello stencil                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Processo** | Applicazioni, plugin del browser, minacce, macchine virtuali |
| **Interazione esterna** | Provider di autenticazione, browser, utenti, applicazioni Web |
| **Archivio dati** | Cache, archiviazione, file di configurazione, database, registro |
| **Flusso di dati** | File binario, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, named pipe, RPC/DCOM, SMB, UDP |
| **Linea di trust/limite perimetrale** | Reti aziendali, Internet, computer, Sandbox, modalità utente/kernel |

### <a name="notesmessages"></a>Note/messaggi

| Componente                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Messaggi** | Logica dello strumento interno che avvisa gli utenti ogni volta che si verifica un errore, ad esempio l'assenza di flussi di dati tra gli elementi. |
| **Note** | Vengono aggiunte note manuali al file dai team di tecnici nel corso del processo di progettazione e revisione. |

### <a name="element-properties"></a>Proprietà dell'elemento

Le proprietà dell'elemento variano in base gli elementi selezionati. Oltre ai limiti di trust, tutti gli altri elementi contengono tre selezioni generali:

| Proprietà dell'elemento                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Nome** | Utile per assegnare nomi a processi, risorse di archiviazione, interazioni e flussi in modo da facilitarne il riconoscimento. |
| **Fuori ambito** | Se selezionato, l'elemento viene sottratto dalla matrice di generazione delle minacce (scelta non consigliata). |
| **Ragioni per il fuori ambito** | Campo di giustificazione per informare gli utenti perché è stato selezionato il fuori ambito. |

Vengono modificate le proprietà sotto ogni categoria di elemento. Selezionare ogni elemento per controllare le opzioni disponibili. È anche possibile aprire il modello per ottenere altre informazioni. Esaminiamo le funzionalità.

## <a name="welcome-screen"></a>Schermata iniziale

All'apertura dell'app viene visualizzata la schermata di **benvenuto**.

### <a name="open-a-model"></a>Aprire un modello

Passare il mouse su **Apri un modello** per visualizzare due opzioni: **Apri da questo computer** e **Apri da OneDrive**. La prima opzione apre la schermata **Apri file**. La seconda opzione illustra il processo di accesso per OneDrive. Dopo l'autenticazione sarà possibile selezionare file e cartelle.

![Aprire un modello](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Aprire dal computer o da OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Commenti, suggerimenti e problemi

Quando si seleziona **Commenti, suggerimenti e problemi**, visitare il forum di MSDN per gli strumenti di SDL. Consente di vedere opinioni di altri utenti sullo strumento nonché nuove idee e soluzioni alternative.

![Commenti e suggerimenti](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Visualizzazione progettazione

Quando si apre o crea un nuovo modello, viene aperta la visualizzazione **Progettazione**.

### <a name="add-elements"></a>Aggiungere elementi

È possibile aggiungere elementi nella griglia in due modi:

- **Trascinamento**: trascinare l'elemento desiderato sulla griglia. Usare le proprietà dell'elemento per fornire informazioni aggiuntive.
- **Clic con il pulsante destro del mouse**: fare clic con il pulsante destro del mouse in un punto qualsiasi della griglia e scegliere voci dal menu a discesa. Verrà visualizzata una rappresentazione generica dell'elemento selezionato.

### <a name="connect-elements"></a>Connettere elementi

È possibile connettere gli elementi in due modi:

- **Trascinamento**: trascinare il flusso di dati desiderato sulla griglia e connettere entrambe le estremità agli elementi appropriati.
- **Clic + MAIUSC**: fare clic sul primo elemento (invio di dati), tenere premuto il tasto MAIUSC e quindi selezionare il secondo elemento (ricezione di dati). Fare clic con il pulsante destro del mouse e scegliere **Connetti**. Se si usa un flusso di dati bidirezionale, l'ordine non è importante.

### <a name="properties"></a>Properties

 Per visualizzare le proprietà che possono essere modificate negli stencil, selezionare lo stencil e le informazioni verranno popolate di conseguenza. L'esempio seguente mostra la situazione prima e dopo il trascinamento di uno stencil **Database** nel diagramma:

#### <a name="before"></a>Prima

![Prima](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Dopo

![Dopo](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Messaggi

Se si crea un modello di minaccia e si dimentica di connettere i flussi di dati agli elementi, viene visualizzata una notifica. È possibile ignorare il messaggio o seguire le istruzioni per risolvere il problema. 

![Messaggi](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Note

Per aggiungere note al diagramma, passare dalla scheda **Messaggi** alla scheda **Note**.

## <a name="analysis-view"></a>Visualizzazione analisi

Dopo aver compilato il diagramma, selezionare il simbolo **Analisi** (lente di ingrandimento) sulla barra degli strumenti dei collegamenti per passare alla visualizzazione **Analisi**.

![Visualizzazione analisi](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Selezione di minacce generata

Quando si seleziona una minaccia, è possibile usare tre funzioni distinte:

| Funzionalità                               | Informazioni      |
| --------------------------------------- | ------------ |
| **Indicatore letto** | <p>La minaccia viene contrassegnata come letta, permettendo di tenere traccia degli elementi rivisti.</p><p>![Indicatore letto/non letto](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Centro di interazione** | <p>Viene evidenziata l'interazione nel diagramma appartenente a una minaccia.</p><p>![Centro di interazione](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Proprietà della minaccia** | <p>Altre informazioni sulla minaccia sono visualizzate nella finestra **Proprietà della minaccia**.</p><p>![Proprietà della minaccia](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Modifica della priorità

È possibile modificare il livello di priorità di ogni minaccia generata. Colori diversi rendono più semplice identificare le minacce con priorità alta, media e bassa.

![Modifica della priorità](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campi modificabili delle proprietà della minaccia

Come illustrato nella figura precedente, è possibile modificare le informazioni generate dallo strumento. È anche possibile aggiungere informazioni a determinati campi, ad esempio la giustificazione. Questi campi vengono generati dal modello. Se sono necessarie altre informazioni per ogni minaccia, è possibile apportare modifiche.

![Proprietà della minaccia](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Report

Dopo aver completato la modifica delle priorità e aggiornato lo stato di ogni minaccia generata, sarà possibile salvare il file e/o stampare un report. Passare a **Report** > **Crea report completo**. Denominare il report. Verrà visualizzata una schermata simile all'immagine seguente:

![Report](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Passaggi successivi

* Per rilasciare un modello alla community, visitare la nostra pagina [GitHub](https://github.com/Microsoft/threat-modeling-templates). 
* Per iniziare a usare lo strumento, passare alla pagina [Download](https://aka.ms/tmtpreview).
