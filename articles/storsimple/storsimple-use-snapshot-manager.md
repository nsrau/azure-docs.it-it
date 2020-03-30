---
title: Interfaccia utente di StorSimple Snapshot Manager | Documentazione Microsoft
description: Viene descritta l'interfaccia utente di StorSimple Snapshot Manager e viene illustrato come utilizzarla per gestire i processi di backup e il catalogo di backup.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933965"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Usare l'interfaccia utente di StorSimple Snapshot Manager per gestire i processi di backup e il catalogo di backup

## <a name="overview"></a>Panoramica
StorSimple Snapshot Manager ha un'interfaccia utente intuitiva che è possibile usare per acquisire e gestire i backup. Questa esercitazione fornisce un'introduzione all'interfaccia utente e illustra come usare ogni componente. Per una descrizione dettagliata di StorSimple Snapshot Manager, vedere [Che cos'è StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrizione della console
Per visualizzare l'interfaccia utente, fare clic sull'icona di StorSimple Snapshot Manager sul desktop. Viene visualizzata la finestra della console, come mostrato nella figura seguente.

![Riquadri di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Nella finestra della console sono presenti cinque elementi principali. Fare clic sul collegamento appropriato per una descrizione completa di ciascun elemento.

* [Barra dei menu](#menu-bar) 
* [Barra degli strumenti](#tool-bar) 
* [Riquadro Ambito](#scope-pane) 
* [Riquadro Risultati](#results-pane) 
* [Riquadro Azioni](#actions-pane) 

Inoltre, StorSimple Snapshot Manager supporta [la navigazione da tastiera e una serie di tasti di scelta rapida](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Accessibilità della console
L'interfaccia utente di StorSimple Snapshot Manager supporta le funzionalità di accessibilità fornite dal sistema operativo Windows e da Microsoft Management Console (MMC), nonché alcuni tasti di scelta rapida specifici per StorSimple Snapshot Manager. 

* Per una descrizione delle funzionalità di accessibilità di Windows, andare a [Tasti di scelta rapida per Windows](https://support.microsoft.com/kb/126449). 
* Per una descrizione delle funzionalità di accessibilità di MMC, andare a [Accessibilità per MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Per una descrizione delle funzionalità di accessibilità di StorSimple Snapshot Manager, andare a [Navigazione da tastiera e tasti di scelta rapida](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra dei menu
La barra dei menu nella parte superiore della finestra della console contiene i menu [File](#file-menu), [Azione](#action-menu), [Visualizza](#view-menu), [Preferiti](#favorites-menu), [Finestra](#window-menu) e [?](#help-menu).

Fare clic su qualsiasi elemento nella barra dei menu per visualizzare un elenco di comandi disponibili in tale menu. Nell'esempio seguente viene mostrato il menu **Visualizza** selezionato nella barra dei menu.

![Menu Visualizza selezionato](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu File
Il menu **File** contiene i comandi MMC (Microsoft Management Console) standard.

#### <a name="menu-access"></a>Accesso al menu
Per visualizzare il menu **File** fare clic su **File** nella barra dei menu. Viene visualizzato il menu seguente.

![Menu File di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrizione del menu
La tabella seguente descrive le voci presenti nel menu **File**.

| Voce di menu | Descrizione |
|:--- |:--- |
| Nuovo |Fare clic su **Nuova** per creare una nuova console basata su StorSimple Snapshot Manager. |
| Apri |Fare clic su **Apri** per aprire una console esistente. |
| Salvare |Fare clic su **Salva** per salvare la console corrente. |
| Salva con nome |Fare clic su **Salva con nome** per creare una nuova istanza rinominata della console corrente. Utilizzare l’opzione **Salva con nome** per personalizzare una visualizzazione e salvarla per un successivo recupero. Ad esempio, è possibile creare snap-in di StorSimple Snapshot Manager che indirizzano a specifici server. |
| Aggiungi/Rimuovi snap-in |Fare clic su **Aggiungi/Rimuovi snap-in** per aggiungere o rimuovere gli snap-in e organizzare i nodi nel riquadro **Ambito**. Per ulteriori informazioni, andare a [Aggiunta, rimozione e organizzazione di snap-in ed estensioni in MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opzioni |Fare clic su **Opzioni** per modificare l'icona della console, specificare modalità e autorizzazioni di accesso utente o eliminare file della console per aumentare lo spazio su disco disponibile. |
| Elenco dei percorsi di file |Fare clic su un percorso nell'elenco numerato per riaprire un file aperto di recente. |
| Esci |Fare clic su **Esci** per chiudere il menu **File**. |

### <a name="action-menu"></a>Menu Azione
Usare il menu **Azione** per selezionare una delle azioni disponibili. Le voci disponibili dipendono dalle opzioni selezionate nel riquadro **Ambito** o nel riquadro **Risultati**.

#### <a name="menu-access"></a>Accesso al menu
Per visualizzare il menu **Azione**, eseguire una delle operazioni seguenti:

* Fare clic con il pulsante destro del mouse su un elemento nel riquadro **Ambito** o nel riquadro **Risultati**.
* Selezionare un elemento nel riquadro **Ambito** o nel riquadro **Risultati** e quindi fare clic su **Azione** nella barra dei menu. 

Se, ad esempio, si seleziona il nodo principale nel riquadro **Ambito** e quindi si fa clic con il pulsante destro del mouse o si fa clic su **Azione** nella barra dei menu, viene visualizzato il menu seguente.

![Menu Azione di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Il riquadro **Azioni** (sulla destra della console) contiene lo stesso elenco di azioni del menu **Azione**. Il riquadro **Azioni** contiene anche le opzioni del menu **Visualizza**, che consentono di creare una visualizzazione personalizzata del riquadro **Risultati**.

![Riquadro Azioni con menu Visualizza aperto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrizione del menu
Nella tabella seguente vengono elencate in ordine alfabetico le azioni di StorSimple Snapshot Manager. 

* Nella colonna **Azione** sono elencate le azioni che è possibile eseguire sui nodi e i risultati. 
* Nella colonna **Navigazione** viene spiegato come visualizzare il menu **Azione** appropriato per poter selezionare l'azione. Alcune azioni vengono visualizzate in più menu **Azione**. Per queste azioni, selezionare un'opzione di **Navigazione** nell'elenco puntato. 
* Nella colonna **Descrizione** viene descritto come usare ogni azione del menu **Azione** o del riquadro Azioni e viene illustrato il relativo risultato.

> [!NOTE]
> Il riquadro **Azioni** e i menu **Azione** contengono altre opzioni, ad esempio **Visualizza**, **Nuova finestra in questa posizione**, **Aggiorna**, **Esporta elenco** e **Guida**. Queste opzioni sono disponibili come parte di MMC e non sono specifiche di StorSimple Snapshot Manager. Nella tabella vengono descritte tali opzioni.
> 
> 

| Azione | Navigazione | Descrizione |
|:--- |:--- |:--- |
| Authenticate |Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. |Fare clic su **Autentica** per immettere la password configurata per il dispositivo. |
| Clone |Espandere **Catalogo backup**, espandere **Snapshot cloud**, fare clic su un backup con data e quindi selezionare un volume nel riquadro **Risultati**. |Fare clic su **Clona** per creare una copia di una snapshot cloud e archiviarla in una posizione designata. |
| Configurare un dispositivo |Fare clic con il pulsante destro del mouse sul nodo **Dispositivi**. |Fare clic su **Configura un dispositivo** per configurare uno o più dispositivi per la connessione all'host di Windows. |
| Crea criterio di backup |Eseguire una delle operazioni seguenti:<ul><li>Fare clic con il pulsante destro del mouse su **Criteri di backup**.</li><li>Fare clic o espandere **Gruppi di volumi** e quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li><li>Fare clic o espandere **Catalogo backup** e quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li></ul> |Fare clic su **Crea criteri di backup** per configurare un backup pianificato per un gruppo di volumi. |
| Crea gruppo di volumi |Eseguire una delle operazioni seguenti:<ul><li>Fare clic sul nodo **Volumi** e quindi fare clic con il pulsante destro del mouse su un volume nel riquadro **Risultati**.</li><li>Fare clic con il pulsante destro del mouse sul nodo **Gruppi di volumi**.</li></ul> |Fare clic su **Crea gruppo di volumi** per assegnare i volumi a un gruppo di volumi. |
| Delete |Fare clic su un nodo o su un risultato; questa voce viene visualizzata in diversi menu **Azione** e riquadri **Azioni**. |Fare clic su **Elimina** per eliminare il nodo o il risultato selezionato. Quando viene visualizzata la finestra di dialogo di conferma, confermare o annullare l'eliminazione. |
| Dettagli |Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. |Fare clic su **Dettagli** per visualizzare i dettagli di configurazione per un dispositivo. |
| Modifica |Fare clic su **Criteri di backup** e quindi fare clic con il pulsante destro del mouse su un criterio nel riquadro **Risultati**. |Fare clic su **Modifica** per cambiare la pianificazione del backup per un gruppo di volumi. |
| Esporta elenco |Fare clic su un nodo o un risultato. Questo elemento viene visualizzato in tutti i menu **Azione** e nei riquadri **Azioni**. |Fare clic su **Esporta elenco** per salvare un elenco in file con valori delimitati da virgole (CSV), È quindi possibile importare questo file in un’applicazione foglio di calcolo per l'analisi. |
| Guida |Fare clic su un nodo o su un risultato. Questo elemento viene visualizzato in tutti i menu **Azione** e nei riquadri **Azioni**. |Fare clic su **Guida** per aprire la Guida in una finestra del browser separata. |
| Nuova finestra da qui |Fare clic su un nodo o un risultato. Questo elemento viene visualizzato in tutti i menu **Azione** e nei riquadri **Azioni**. |Fare clic su **Nuova finestra da qui** per aprire una nuova finestra di StorSimple Snapshot Manager. |
| Aggiorna |Fare clic su un nodo o un risultato. Questo elemento viene visualizzato in tutti i menu **Azione** e nei riquadri **Azioni**. |Fare clic su **Aggiorna** per aggiornare la finestra di StorSimple Snapshot Manager attualmente visualizzata. |
| Aggiorna dispositivo |Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. |Fare clic su **Aggiorna dispositivo** per sincronizzare uno specifico dispositivo connesso con StorSimple Snapshot Manager. |
| Aggiorna dispositivi |Fare clic con il pulsante destro del mouse sul nodo **Dispositivi**. |Fare clic su **Aggiorna dispositivi** per sincronizzare l'elenco dei dispositivi connessi con StorSimple Snapshot Manager. |
| Ripetere la scansione dei volumi |Fare clic con il pulsante destro del mouse sul nodo **Volumi**. |Fare clic su **Ripeti analisi dei volumi** per aggiornare l'elenco di volumi visualizzati nel riquadro **Risultati**. |
| Restore |Espandere **Catalogo di backup**, espandere un gruppo di volumi, espandere **Snapshot locali** o **Snapshot nel cloud** e quindi fare clic con il pulsante destro del mouse su un backup. |Fare clic su **Ripristina** per sostituire i dati del gruppo di volumi corrente con i dati del backup selezionato. |
| Esegui backup |Eseguire una delle operazioni seguenti:<ul><li>Espandere **Gruppi di volumi** e quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li><li>Espandere **Catalogo backup** e quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li></ul> |Fare clic su **Esegui backup** per avviare immediatamente un processo di backup. |
| Visualizza/Nascondi importazioni |Fare clic con il pulsante destro del mouse sul nodo principale nel riquadro **Ambito**, ovvero il nodo **StorSimple Snapshot Manager** negli esempi. |Fare clic su **Visualizza/Nascondi importazioni** per mostrare o nascondere i gruppi di volumi e i backup associati importati dal dashboard del servizio Gestione dispositivi StorSimple. |

### <a name="view-menu"></a>Menu Visualizza
Il menu **Visualizza** consente di creare una visualizzazione personalizzata del contenuto del riquadro **Risultati**. Nel menu **Visualizza** sono disponibili le opzioni **Aggiungi/rimuovi colonne** e **Personalizza**.

#### <a name="menu-access"></a>Accesso al menu
È possibile accedere al menu **Visualizza** dalla barra dei menu o dal menu **Azioni**.

![Menu Visualizza di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrizione del menu
La tabella seguente descrive le voci presenti nel menu **Visualizza**.

| Voce di menu | Descrizione |
|:--- |:--- |
| Aggiungi/Rimuovi colonne |Fare clic su **Aggiungi/rimuovi colonne** per aggiungere o rimuovere colonne nel riquadro **Risultati**. |
| Personalizza |Fare clic su **Personalizza** per mostrare o nascondere le voci nella finestra della console di StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Preferiti
Usare il menu **Preferiti** per aggiungere, rimuovere e organizzare le visualizzazioni pagine e le attività usate di frequente. 

#### <a name="menu-access"></a>Accesso al menu
È possibile accedere al menu **Preferiti** nella barra dei menu.

![Menu Preferiti di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrizione del menu
La tabella seguente descrive le voci presenti nel menu **Preferiti**.

| Voce di menu | Descrizione |
|:--- |:--- |
| Aggiungi a Preferiti |Fare clic su **Aggiungi a Preferiti** per aggiungere la visualizzazione corrente all'elenco di preferiti. |
| Organizza Preferiti |Fare clic su **Organizza Preferiti** per organizzare i contenuti della cartella Preferiti. |

### <a name="window-menu"></a>Menu Finestra
Usare il menu **Finestra** per aggiungere e ridisporre le finestre della console di StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Accesso al menu
È possibile accedere al menu **Finestra** nella barra dei menu.

![Menu Finestra di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

L'elenco numerato nella parte inferiore del menu mostra le finestre attualmente aperte. Fare clic su qualsiasi finestra dell'elenco per portarla in primo piano. 

#### <a name="menu-description"></a>Descrizione del menu
Nella tabella seguente vengono descritte le voci del menu Finestra.

| Voce di menu | Descrizione |
|:--- |:--- |
| Nuova finestra |Fare clic su **Nuova finestra** per aprire una nuova finestra della console (oltre a quella esistente). |
| Cascade |Fare clic su **Sovrapponi** per visualizzare le finestre aperte della console sovrapponendole. |
| Affianca orizzontalmente |Fare clic su **Affianca orizzontalmente** per visualizzare le finestre aperte della console affiancandole (formato griglia). |
| Disponi icone |Se più finestre della console sono aperte e sparpagliate sul desktop, ridurle a icona e quindi fare clic su **Disponi icone** per disporle in una riga orizzontale nella parte inferiore della schermata. |

### <a name="help-menu"></a>Menu Guida
Usare il menu **Guida** per visualizzare la guida in linea disponibile per StorSimple Snapshot Manager e MMC. È inoltre possibile visualizzare le informazioni sulle versioni software di MMC e StorSimple Snapshot Manager attualmente installate sul sistema. 

È possibile accedere al menu **?** nella barra dei menu. È inoltre possibile accedere agli argomenti della guida di StorSimple Snapshot Manager nel riquadro **Azioni** .

![Menu Guida di StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrizione del menu
Nella tabella seguente vengono descritte le voci del menu Guida.

| Voce di menu | Descrizione |
|:--- |:--- |
| Guida di StorSimple Snapshot Manager |Fare clic su **Guida di StorSimple Snapshot Manager** per aprire la guida di StorSimple Snapshot Manager in una finestra separata. |
| Argomenti della Guida |Fare clic su **Guida** per aprire la Guida di MMC in una finestra separata. |
| Sito Web TechCenter |Fare clic su **Sito Web TechCenter** per aprire la home page di Microsoft TechNet Tech Center in una finestra separata. |
| Informazioni su Microsoft Management Console |Fare clic su **Informazioni su Microsoft Management Console** per conoscere la versione di Microsoft Management Console installata nel sistema. |
| Informazioni su StorSimple Snapshot Manager |Fare clic su **Informazioni su StorSimple Snapshot Manager** per visualizzare quale versione dello snap-in è installata sul sistema. |

## <a name="tool-bar"></a>Barra degli strumenti
La barra degli strumenti, posizionata sotto la barra dei menu, contiene le icone della navigazione e delle attività. Ciascuna icona rappresenta una scelta rapida per un'attività specifica.

### <a name="icon-descriptions"></a>Descrizioni delle icone
Nella tabella seguente vengono descritte le icone della barra degli strumenti. 

| Icona | Descrizione |
|:--- |:--- |
| ![Freccia sinistra](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Fare clic sull'icona freccia sinistra per tornare alla pagina precedente. |
| ![Freccia destra](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Fare clic sulla freccia destra per passare alla pagina successiva (se la freccia è grigia, l'azione non è disponibile). |
| ![Icona su](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Fare clic sull'icona su per passare al livello successivo nell'albero della console (il riquadro **Ambito** ). |
| ![Mostra/Nascondi albero della console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Fare clic sull'icona Mostra/Nascondi albero della console per mostrare o nascondere il riquadro **Ambito** . |
| ![Esporta elenco](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Fare clic sull'icona Esporta elenco per esportare un elenco in un file CSV specificato. |
| ![Icona della guida](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Fare clic sull'icona Guida per aprire un argomento della guida in linea di MMC. |
| ![Mostra/Nascondi riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Fare clic sull'icona Mostra/Nascondi riquadro **Azioni** per mostrare o nascondere il riquadro **Azioni**. |

## <a name="scope-pane"></a>Riquadro Ambito
**Ambito** è il riquadro più a sinistra nell'interfaccia utente di StorSimple Snapshot Manager. Contiene l’albero della console (o del nodo) ed è il meccanismo di navigazione principale per StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struttura del riquadro Ambito
Il riquadro **Ambito** contiene una serie di oggetti (nodi) selezionabili organizzati in una struttura ad albero. 

![Riquadro Ambito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Per espandere o comprimere un nodo, fare clic sull'icona della freccia accanto al nome del nodo.
* Per visualizzare lo stato o i contenuti di un nodo, fare clic sul nome del nodo. Le informazioni vengono visualizzate nel riquadro **Risultati**. 

Il riquadro **Ambito** contiene i nodi seguenti: 

* [Nodo Dispositivi](#devices-node) 
* [Nodo Volumi](#volumes-node) 
* [Nodo Gruppi di volumi](#volume-groups-node) 
* [Nodo Criteri di backup](#backup-policies-node) 
* [Nodo Catalogo di backup](#backup-catalog-node) 
* [Nodo Processi](#jobs-node) 

### <a name="scope-pane-tasks"></a>Attività riquadro Ambito
Usare il riquadro **Ambito** per completare un'azione in un nodo specifico. Per selezionare un'attività, eseguire una delle operazioni seguenti:

* Fare clic con il pulsante destro del mouse sul nodo, quindi selezionare l'attività dal menu che viene visualizzato.
* Fare clic sul nodo, quindi fare clic su **Azione** nella barra dei menu. Selezionare l'attività dal menu che viene visualizzato.
* Fare clic sul nodo, quindi selezionare l'azione nel riquadro **Azioni**.

Quando si seleziona un nodo e si utilizza uno di questi metodi per visualizzare un elenco di attività, vengono visualizzate solo le azioni che possono essere eseguite su tale nodo.

### <a name="devices-node"></a>Nodo Dispositivi
Il nodo **Dispositivi** rappresenta i dispositivi StorSimple e i dispositivi StorSimple virtuali connessi a StorSimple Snapshot Manager. Selezionare questo nodo per connettere e configurare un dispositivo e importare i relativi volumi associati, i gruppi di volumi e le copie di backup esistenti. Più dispositivi possono essere connessi a un singolo host.

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Dispositivi**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Dispositivi** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco dei dispositivi configurati, fare clic su **Dispositivi** nel riquadro **Ambito**. L'elenco dei dispositivi e le informazioni su ciascun dispositivo verranno visualizzati nel riquadro **Risultati** .

### <a name="volumes-node"></a>Nodo Volumi
Il nodo **Volumi** rappresenta le unità che corrispondono ai volumi montati dall'host, ad esempio quelli individuati tramite iSCSI e quelli individuati tramite un dispositivo. Utilizzare questo nodo per visualizzare l'elenco dei volumi disponibili e assegnare i singoli volumi a gruppi di volumi.

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Volumi**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Volumi** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di volumi, fare clic su **Volumi** nel riquadro **Ambito**. L'elenco di volumi e le informazioni su ogni volume vengono visualizzati nel riquadro **Risultati**.

### <a name="volume-groups-node"></a>Nodo Gruppi volumi
I gruppi di volumi sono noti anche come gruppi di coerenza. Ciascun gruppo di volumi è un pool di volumi relativi all'applicazione che consente di garantire la coerenza con l'applicazione durante le operazioni di backup. Usare il nodo **Gruppi di volumi** per configurare questi gruppi e per eseguire backup interattivi o creare pianificazioni di backup. 

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Gruppi di volumi**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Gruppi di volumi** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di gruppi di volumi, fare clic su **Gruppi di volumi** nel riquadro **Ambito**. L'elenco di gruppi di volumi e le informazioni su ogni gruppo di volumi vengono visualizzati nel riquadro **Risultati**.

### <a name="backup-policies-node"></a>Nodo Criteri di backup
I criteri di backup sono pianificazioni dei processi per snapshot locali e cloud. Usare il nodo **Criteri di backup** per specificare la frequenza di creazione di un backup e per quanto tempo deve essere conservato. 

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Criteri di backup**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Criteri di backup** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di criteri di backup, fare clic su **Criteri di backup** nel riquadro **Ambito**. L'elenco di criteri di backup e le informazioni sui criteri vengono visualizzati nel riquadro **Risultati**.

> [!NOTE]
> È possibile conservare un massimo di 64 backup.


### <a name="backup-catalog-node"></a>Nodo Catalogo di backup
Il nodo **Catalogo di backup** contiene gli elenchi dei backup locali in sede e fuori sede dei volumi StorSimple di Azure. Questo nodo è organizzato per gruppo di volumi e il contenitore di ogni gruppo di volumi contiene strutture separate per gli snapshot locali (il nodo **Snapshot locali**) e gli snapshot cloud (il nodo **Snapshot cloud**). Se viene espanso, il contenitore di ciascun gruppo di volumi elenca tutti i backup riusciti che sono stati eseguiti in modo interattivo o da un criterio configurato.

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Catalogo di backup**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Catalogo di backup** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di snapshot di backup, fare clic su **Catalogo di backup** nel riquadro **Ambito**. L'elenco di snapshot e le informazioni su ogni volume vengono visualizzati nel riquadro **Risultati**.

### <a name="local-snapshots-node"></a>Nodo Snapshot locali
Nel nodo **Snapshot locali** vengono elencati gli snapshot locali per un gruppo di volumi specifico. Il nodo si trova sotto il nodo **Catalogo di backup** nel riquadro **Ambito**. Gli snapshot locali sono copie temporizzate dei dati del volume archiviati sul dispositivo StorSimple di Azure. In genere, questo tipo di backup può essere creato e ripristinato rapidamente. È possibile utilizzare uno snapshot locale come copia di backup locale.

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Snapshot locali**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Snapshot locali** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di snapshot locali, fare clic su **Snapshot locali** nel riquadro **Ambito**. L'elenco di snapshot e le informazioni su ogni volume vengono visualizzati nel riquadro **Risultati**.

### <a name="cloud-snapshots-node"></a>Nodo Snapshot cloud
Nel nodo **Snapshot cloud** vengono elencati gli snapshot cloud per un gruppo di volumi specifico. Il nodo si trova sotto il nodo **Catalogo di backup** nel riquadro **Ambito**. Gli snapshot Cloud sono copie temporizzate di dati del volume archiviati nel cloud. Uno snapshot cloud equivale a uno snapshot replicato su un diverso sistema di archiviazione fuori sede. Gli snapshot cloud sono particolarmente utili negli scenari di ripristino di emergenza.

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Snapshot cloud**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Snapshot cloud** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco di snapshot cloud, fare clic su **Snapshot cloud** nel riquadro **Ambito**. L'elenco di snapshot e le informazioni su ogni volume vengono visualizzati nel riquadro **Risultati**.

### <a name="jobs-node"></a>Nodo Processi
Il nodo **Processi** contiene le informazioni sui processi di backup pianificati, in esecuzione e completati di recente. 

* Per espandere il nodo, fare clic sull'icona a forma di freccia accanto a **Processi**.
* Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Processi** oppure su uno dei nodi presenti nella vista espansa.
* Per visualizzare un elenco dei processi pianificati, espandere il nodo **Processi**, quindi fare clic su **Pianificati**. L'elenco dei processi configurati in precedenza e le informazioni su ogni processo vengono visualizzati nel riquadro **Risultati**. 
* Per visualizzare un elenco dei processi completati di recente, espandere il nodo **Processi**, quindi fare clic su **Ultime 24 ore**. Un elenco dei processi completati nelle ultime 24 ore viene visualizzato nel riquadro **Risultati**. Il riquadro **Risultati** contiene anche le informazioni su ciascun processo completato.
* Per visualizzare un elenco dei processi attualmente in esecuzione, espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. L'elenco dei processi attualmente in esecuzione e le informazioni su ogni processo vengono visualizzati nel riquadro **Risultati**.

## <a name="results-pane"></a>Riquadro Risultati
**Risultati** è il riquadro centrale nell’interfaccia grafica di StorSimple Snapshot Manager. Contiene elenchi e informazioni di stato dettagliate per il nodo selezionato nel riquadro **Ambito**.

### <a name="example"></a>Esempio
Per visualizzare il seguente esempio, fare clic sul nodo **Gruppo di volumi** nel riquadro **Ambito**. Nel riquadro **Risultati** viene visualizzato un elenco di gruppi di volumi con i dettagli su ciascun gruppo.

![Riquadro Risultati](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

È possibile configurare i dettagli mostrati nel riquadro **Risultati**: fare clic con il pulsante destro del mouse su un nodo nel riquadro **Ambito**, fare clic su **Visualizza**, quindi fare clic su **Aggiungi/Rimuovi colonne**.

## <a name="actions-pane"></a>Riquadro Azioni
**Ambito** è il riquadro destro nell’interfaccia utente di StorSimple Snapshot Manager. Il riquadro contiene un menu di operazioni che è possibile eseguire sul nodo, sulla visualizzazione o sui dati selezionati nel riquadro **Ambito** o **Risultati**. Il riquadro **Azioni** contiene gli stessi comandi dei menu **Azione** disponibile per gli elementi nel riquadro **Ambito** e **Risultati**. Per una descrizione di ciascuna azione, vedere la tabella nella sezione relativa al menu **Azione** .

### <a name="examples"></a>Esempi
Per visualizzare il seguente esempio, nel riquadro **Ambito** espandere il nodo **Processi** e fare clic su **Pianificati**. Nel riquadro **Azioni** vengono visualizzate le azioni disponibili per il nodo **Pianificati**.

![Esempio processi pianificati nel riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Per visualizzare più opzioni, nel riquadro **Ambito** espandere il nodo **Processi**, fare clic su **Pianificati**, quindi fare clic su un processo pianificato nel riquadro **Risultati**. Nel riquadro **Azioni** vengono visualizzate le azioni disponibili per il processo pianificato, come illustrato nel seguente esempio.

![Esempio azioni del processo nel riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navigazione da tastiera e tasti di scelta rapida
StorSimple Snapshot Manager abilita le funzionalità di accessibilità del sistema operativo Windows e di Microsoft Management Console (MMC). Include inoltre alcune funzionalità di navigazione da tastiera e i tasti di scelta rapida specifici di StorSimple Snapshot Manager, come descritto nelle sezioni seguenti.

* [Tasti di navigazione da tastiera](#keyboard-navigation-keys) 
* [Tasti di scelta rapida della barra dei menu](#menu-bar-shortcut-keys) 
* [Tasti di scelta rapida del riquadro Ambito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tasti di navigazione da tastiera
Nella tabella seguente vengono descritti i tasti che è possibile utilizzare per spostarsi nell'interfaccia utente di StorSimple Snapshot Manager. 

| Tasto di navigazione | Azione |
|:--- |:--- |
| Tasto freccia giù |Utilizzare il tasto freccia giù per spostarsi in senso verticale alla voce successiva in un menu o in un riquadro. |
| Immettere |Premere il tasto INVIO per completare un'azione e procedere al passaggio successivo. È possibile ad esempio premere INVIO per selezionare **Avanti**, **OK** o **Crea** e quindi passare al passaggio successivo in una procedura guidata. |
| ESC |Premere il tasto ESC per chiudere un menu oppure per annullare e chiudere una pagina. |
| F1 |Premere il tasto F1 per visualizzare un argomento della guida per la finestra attiva corrente. |
| F5 |Premere il tasto F5 per aggiornare un nodo. |
| F6 |Premere F6 per spostarsi dal riquadro **Ambito** al riquadro **Risultati**. |
| F10 |Premere il tasto F10 per passare alla barra dei menu. |
| Tasto freccia sinistra |Utilizzare il tasto freccia sinistra per spostarsi in senso orizzontale da un'opzione della barra dei menu a quella precedente. Quando ci si sposta sulla voce precedente nella barra dei menu, viene visualizzato il menu Azione (o Contesto) per la voce precedente. |
| Tasto freccia destra |Utilizzare il tasto freccia destra per spostarsi in senso orizzontale da un’opzione della barra dei menu a quella successiva. Quando ci si sposta sulla voce successiva nella barra dei menu, viene visualizzato il menu Azione (o Contesto) per la nuova voce. |
| Tasto TAB |Utilizzare il tasto TAB per spostarsi al riquadro successivo nella console oppure alla selezione o alla casella di testo successiva in una pagina. |
| Tasto freccia su |Utilizzare il tasto freccia su per spostarsi in senso verticale alla voce precedente in un menu o in un riquadro. |

### <a name="menu-bar-shortcut-keys"></a>Tasti di scelta rapida della barra dei menu
Nella tabella seguente vengono descritte le combinazioni di tasti di scelta rapida per la barra dei menu. Dopo aver aperto i menu premendo i tasti di scelta rapida, è possibile utilizzare i tasti di scelta rapida del menu (i tasti sottolineati nel menu). Per ulteriori informazioni sulla barra dei menu, andare a [Barra dei menu](#menu-bar).

| Tasto di scelta rapida | Risultato | Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |:--- |:--- |
| ALT + F |Apre il menu **File**. |N |Consente di aprire l’istanza di una nuova console. |
|  |O |Apre la pagina **Strumenti di amministrazione**. | |
|  |S |Consente di salvare la console di StorSimple Snapshot Manager. | |
|  |Una  |Apre la pagina **Salva con nome**. | |
|  |M |Apre la pagina **Aggiungi/rimuovi snap-in**. | |
|  |P |Apre la pagina **Opzioni**. | |
|  |H |Consente di aprire la guida in linea. | |
| ALT + A |Apre il menu **Azione**. |I |Consente di attivare e disattivare l'opzione di visualizzazione dell’importazione. |
|  |W |Consente di aprire una nuova console di StorSimple Snapshot Manager. | |
|  |F |Consente di aggiornare la console di StorSimple Snapshot Manager. | |
|  |L |Apre la pagina **Esporta elenco**. | |
|  |H |Consente di aprire la guida in linea. | |
| ALT + V |Apre il menu **Visualizza**. |Una  |Apre la pagina **Aggiungi/rimuovi colonne**. |
|  |U |Apre la pagina **Personalizza visualizzazione**. | |
| ALT + O |Apre il menu **Preferiti**. |Una  |Apre la pagina **Aggiungi a Preferiti**. |
|  |O |Apre la pagina **Organizza preferiti**. | |
| ALT + W |Apre il menu **Finestra**. |N |Consente di aprire un'altra finestra di StorSimple Snapshot Manager. |
|  |C |Consente di visualizzare tutte le finestre della console aperte in uno stile a cascata. | |
|  |T |Consente di visualizzare tutte le finestre della console aperte in motivo di griglia. | |
|  |I |Consente di disporre le icone su una riga orizzontale nella parte inferiore dello schermo. | |
| ALT + H |Apre il menu **?**. |H |Consente di aprire la guida in linea. |
|  |T |Consente di aprire la pagina Web di Microsoft TechNet Tech Center. | |
|  |Una  |Apre la pagina **Informazioni su Microsoft Management Console**. | |

### <a name="scope-pane-shortcut-keys"></a>Tasti di scelta rapida del riquadro Ambito
Nelle seguenti tabelle vengono visualizzate le combinazioni di tasti per ogni nodo del riquadro **Ambito**. 

* [Tasti di scelta rapida del nodo Dispositivi](#devices-node-shortcut-keys)
* [Tasti di scelta rapida del nodo Volumi](#volumes-node-shortcut-keys)
* [Tasti di scelta rapida del nodo Gruppi di volumi](#volume-groups-node-shortcut-keys)
* [Tasti di scelta rapida del nodo Criteri di backup](#backup-policies-node-shortcut-keys)
* [Tasti di scelta rapida del nodo Catalogo di backup](#backup-catalog-node-shortcut-keys)
* [Tasti di scelta rapida del nodo Processi](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Dispositivi
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| C |Apre la pagina **Configura un dispositivo**. |
| D |Consente di aggiornare l'elenco di dispositivi e i dettagli del dispositivo. |
| V |Apre il menu **Visualizza**. |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Dettagli** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| L |Apre la pagina **Esporta elenco**. |
| H |Consente di aprire la guida in linea. |

#### <a name="volumes-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Volumi
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| V |Consente di aggiornare l'elenco dei volumi. |
| V (premere due volte) |Apre il menu **Visualizza**. |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Volumi** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| L |Apre la pagina **Esporta elenco**. |
| H |Consente di aprire la guida in linea. |

#### <a name="volume-groups-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Gruppi di volumi
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| G |Apre la pagina **Crea un gruppo di volumi**. |
| V |Apre il menu **Visualizza**. |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Gruppi di volumi** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| L |Apre la pagina **Esporta elenco**. |
| H |Consente di aprire la guida in linea. |

#### <a name="backup-policies-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Criteri di backup
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| b |Apre la pagina **Crea criteri**. |
| V |Apre il menu **Visualizza**. |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Gruppi di volumi** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| L |Consente di aprire la pagina **Esporta elenco**. |
| H |Consente di aprire la guida in linea. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Catalogo di backup
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Gruppi di volumi** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| H |Consente di aprire la guida in linea. |

#### <a name="jobs-node-shortcut-keys"></a>Tasti di scelta rapida del nodo Processi
| Tasti di scelta rapida del menu | Risultato |
|:--- |:--- |
| V |Apre il menu **Visualizza**. |
| W |Consente di aprire una nuova console di StorSimple Snapshot Manager incentrata sul nodo **Processi** . |
| F |Consente di aggiornare la console di StorSimple Snapshot Manager. |
| L |Apre la pagina **Esporta elenco**. |
| H |Consente di aprire la guida in linea. |

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Leggere le informazioni su come [usare StorSimple Snapshot Manager per connettere e gestire i dispositivi](storsimple-snapshot-manager-manage-devices.md).

