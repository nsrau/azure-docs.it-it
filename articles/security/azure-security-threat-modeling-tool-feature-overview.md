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
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>Panoramica della funzione Threat Modeling Tool

Siamo lieti della scelta dell'utente di impiegare Threat Modeling Tool per le esigenze di modellazione. Se non lo si è ancora fatto, visitare **[Guida introduttiva a Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md)** per apprendere le nozioni di base.

> Lo strumento viene aggiornato spesso, perciò si consiglia di controllare questa guida di frequente per vedere le ultime funzionalità e miglioramenti.

Facendo clic sul pulsante "Crea un nuovo modello" viene visualizzata una pagina iniziale vuota, simile all'immagine riportata di seguito:

![Pagina iniziale vuota](./media/azure-security-threat-modeling-tool/tmtstart.png)

Utilizzando il modello delle minacce creato dal nostro team nell'esempio **[Introduzione](./azure-security-threat-modeling-tool-getting-started.md)** esempio, controlliamo tutte le funzionalità disponibili nello strumento oggi.

![Modello di minaccia di base](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>Navigazione

Prima di illustrare le caratteristiche, è opportuno discutere dei componenti principali presenti nello strumento

### <a name="menu-items"></a>Voci di menu

L'esperienza dovrebbe essere simile ad altri prodotti Microsoft. Iniziamo esaminando le voci di menu di primo livello:

![Voci di menu](./media/azure-security-threat-modeling-tool/menuitems.png)

| Etichetta                               | Dettagli      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Aprire, salvare e chiudere i file</li><li>Accedere e disconnettersi dagli account di OneDrive</li><li>Condividere collegamenti (visualizzazione + modifica)</li><li>Visualizzare le informazioni di file</li><li>Applicare un nuovo modello a modellazioni esistenti</li></ul> |
| **Modifica** | Annullare/ripristinare azioni, nonché copia, incolla ed elimina |
| **Visualizza** | <ul><li>Passare fra le visualizzazioni **Analisi** e **Progettazione**</li><li>Aprire finestre chiuse (ad es. stencil, proprietà degli elementi e messaggi)</li><li>Ripristinare le impostazioni predefinite del layout</li></ul> |
| **Diagramma** | Aggiungere/eliminare diagrammi e spostarsi tra le "schede" dei diagrammi |
| **Report** | Creare report HTML da condividere con altri utenti |
| **Guida** | Informazioni di guida sull'uso dello strumento |

Le icone sono collegamenti ai menu di primo livello:

| Icona                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Apri** | Apre un nuovo file |
| **Salva** | Salva il file corrente |
| **Progettazione** | Passa alla visualizzazione progettazione dove è possibile creare modelli |
| **Analizzare** | Mostra le minacce generate e le relative proprietà |
| **Aggiungi diagramma** | Aggiunge un nuovo diagramma (simile a nuove schede di Excel) |
| **Elimina diagramma** | Elimina il diagramma corrente |
| **Copia/Taglia/Incolla** | Copia/taglia /incolla elementi |
| **Annulla/Ripeti** | Annulla/ripete azioni |
| **Zoom avanti/Zoom indietro** | Esegue lo zoom avanti e indietro del diagramma per una migliore visualizzazione |
| **Commenti e suggerimenti** | Apre il Forum MSDN |

### <a name="canvas"></a>Canvas

Lo spazio in cui si trascinano gli elementi. Il trascinamento è il modo più rapido ed efficiente per creare i modelli. È anche possibile fare clic e selezionare dal menu, per aggiungere versioni generiche degli elementi che si stanno utilizzando, come illustrato di seguito.

#### <a name="dropping-the-stencil-on-the-canvas"></a>Rilascio dello stencil sul canvas

![Rilascio sul canvas](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>Clic sullo stencil

![Proprietà dell'elemento](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>Stencil

Dove è possibile trovare tutti gli stencil disponibili per l'uso sul modello selezionato. Se non è possibile trovare gli elementi giusti, provare a usare un altro modello o a modificarne uno in base alle proprie esigenze. In genere si dovrebbe riuscire a trovare una combinazione di categorie come quelli indicati di seguito:

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
| **Messaggi** | Logica dello strumento interno che avvisa gli utenti ogni volta che si verifica un errore, ad esempio l'assenza di flusso di dati tra gli elementi |
| **Note** | Note manuali aggiunte al file dai team di tecnici nel corso del processo di progettazione e revisione |

### <a name="element-properties"></a>Proprietà dell'elemento

Queste variano in base agli elementi selezionati. Oltre ai limiti di trust, tutti gli altri elementi contengono 3 selezioni generali:

| Proprietà dell'elemento                               | Dettagli      |
| --------------------------------------- | ------------ |
| **Nome** | Utile per assegnare nomi a processi, archivi, interazioni e flussi in modo da facilitarne il riconoscimento |
| **Fuori ambito** | Se selezionato, l'elemento viene sottratto dalla matrice di generazione delle minacce (scelta non consigliata) |
| **Ragioni per il fuori ambito** | Campo di giustificazione per informare gli utenti perché è stato selezionato il fuori ambito |

Vengono modificate le proprietà sotto ogni categoria di elemento. Fare clic su ciascun elemento per controllare le opzioni disponibili o aprire il modello per altre informazioni. Esaminiamo le funzionalità.

## <a name="welcome-screen"></a>Schermata iniziale

La schermata di benvenuto è la prima cosa che viene visualizzato quando si apre l'app.

### <a name="open-a-model"></a>Aprire un modello

Passando con il mouse sul pulsante "Apri un modello" vengono visualizzate 2 opzioni nascoste: "Apri da questo computer" e "Apri da OneDrive". La prima apre la schermata Apri file, mentre la seconda conduce l'utenre attraverso la procedura di accesso a OneDrive, consentendo di scegliere cartelle e file dopo l'autenticazione.

![Aprire un modello](./media/azure-security-threat-modeling-tool/openmodel.png)

![Aprire dal computer o da OneDrive](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Commenti, suggerimenti e problemi

Selezionando questa opzione si passa al forum MSDN per gli strumenti SDL. È un ottimo modo per vedere opinioni di altri utenti sullo strumento nonché nuove idee e soluzioni alternative.

![Commenti e suggerimenti](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>Visualizzazione progettazione

Ogni volta che si apre o si crea un nuovo modello, viene mostrata la visualizzazione progettazione.

### <a name="adding-elements"></a>Aggiunta di elementi

Esistono 2 modi per aggiungere elementi sulla griglia:

- **Trascinare e rilasciare**: trascinare l'elemento desiderato nella griglia, quindi usare le proprietà dell'elemento per fornire informazioni aggiuntive.
- **Clic con il pulsante destro del mouse**: fare clic con il pulsante destro del mouse in punto qualsiasi della griglia e scegliere dal menu a discesa. Una rappresentazione generica dell'elemento verrà visualizzata sullo schermo.

### <a name="connecting-elements"></a>Collegamento di elementi

Sono disponibili 2 modi per connettere gli elementi nello strumento:

- **Trascinare e rilasciare**: trascinare il flusso di dati desiderato sulla griglia e connettere entrambe le estremità agli elementi appropriati.
- **Eseguire clic + MAIUSC**: fare clic sul primo elemento (invio di dati), premere e tenere premuto il tasto MAIUSC, quindi selezionare il secondo elemento (ricezione di dati). Fare clic con il pulsante destro del mouse e selezionare "Connetti". Se si utilizza un flusso di dati bidirezionale, l'ordine non è importante.

### <a name="properties"></a>Proprietà

Mostra tutte le proprietà che possono essere modificate negli stencil inseriti nel diagramma. Per visualizzare le proprietà, fare clic sullo stencil e le informazioni verranno popolate di conseguenza. L'esempio seguente mostra la situazione prima e dopo il trascinamento di uno stencil "Database" nel diagramma:

#### <a name="before"></a>Prima

![Prima](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>Dopo

![Dopo](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>Messaggi

Se si crea un modello di minacce e si dimentica di connettere i flussi di dati agli elementi, la finestra messaggio notifica l'azione da eseguire. È possibile scegliere di ignorarla o seguire le istruzioni per risolvere il problema. 

![Messaggi](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>Note

Alternando le schede dai messaggi alle note è possibile aggiungere note al diagramma per catturare tutti i pensieri

## <a name="analysis-view"></a>Visualizzazione analisi

Dopo aver compilato il diagramma, passare alla visualizzazione analisi passando alle selezioni di menu in alto e scegliendo la lente di ingrandimento accanto alla tavolozza.

![Visualizzazione analisi](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>Selezione di minacce generata

Quando si fa clic su una minaccia, è possibile sfruttare tre funzioni uniche:

| Funzionalità                               | Info      |
| --------------------------------------- | ------------ |
| **Indicatore letto** | <p>La minaccia è ora contrassegnato come letta, il che può facilitare il monitoraggio degli elementi già analizzati</p><p>![Indicatore letto/non letto](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **Centro di interazione** | <p>Viene evidenziata l'interazione nel diagramma appartenente a tale minaccia</p><p>![Centro di interazione](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **Proprietà della minaccia** | <p>Altre informazioni sulla minaccia sono aggiunte nella finestra delle proprietà della minaccia</p><p>![Proprietà della minaccia](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>Modifica della priorità

La modifica del livello di priorità di ciascuna minaccia generata modifica anche i colori per facilitare l'identificazione delle minacce a priorità alta, media e bassa.

![Modifica della priorità](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campi modificabili delle proprietà della minaccia

Come illustrato nell'immagine precedente, gli utenti possono modificare le informazioni generate dallo strumento a anche aggiungere informazioni a determinati campi, ad esempio una giustificazione. Questi campi vengono generati dal modello, pertanto se sono necessarie altre informazioni per ogni minaccia, si consiglia di apportare modifiche.

![Proprietà della minaccia](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>Report

Dopo aver completato la modifica delle priorità e aggiornato lo stato di ciascuna minaccia generata, è possibile salvare il file e/o stampare un report passando a "Report" e poi a "Crea report completo". Verrà chiesto di assegnare un nome al report e, al termine dell'operazione, si dovrebbe vedere qualcosa di simile all'immagine riportata di seguito:

![Report](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>Passaggi successivi

Per rilasciare un modello alla community, visitare la nostra pagina **[GitHub](https://github.com/Microsoft/threat-modeling-templates)**. **[Scaricare](https://aka.ms/tmtpreview)** lo strumento per iniziare oggi stesso.

