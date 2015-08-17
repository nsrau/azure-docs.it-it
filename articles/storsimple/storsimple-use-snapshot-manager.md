<properties 
   pageTitle="Interfaccia utente di Gestione snapshot StorSimple | Microsoft Azure"
   description="Viene descritta l'interfaccia utente di Gestione snapshot StorSimple e viene illustrato come utilizzarla per gestire i processi di backup e il catalogo di backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />

<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />


# Interfaccia utente di Gestione snapshot StorSimple

## Panoramica

Gestione snapshot StorSimple dispone di un'interfaccia utente grafica (GUI) che è possibile utilizzare per gestire copie di backup dei gruppi di volumi, tra cui quelli archiviati in locale e nel cloud. In questa esercitazione viene fornita un'introduzione all'interfaccia, quindi viene illustrato come utilizzare ciascuno dei componenti. (Per una descrizione dettagliata di Gestione snapshot StorSimple, vedere [Che cos'è Gestione snapshot StorSimple?](storsimple-what-is-snapshot-manager.md))

### Descrizione della console

Per visualizzare l'interfaccia utente, fare clic sull'icona di Gestione snapshot StorSimple sul desktop. Viene visualizzata la finestra della console, come mostrato nella figura seguente.

![Riquadri di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

**Figura 1: Interfaccia utente di Gestione snapshot StorSimple**

Nella finestra della console sono presenti cinque elementi principali. Fare clic sul collegamento appropriato per una descrizione completa di ciascun elemento.

- [Barra dei menu](#menu-bar) 
- [Barra degli strumenti](#tool-bar) 
- [Riquadro Ambito](#scope-pane) 
- [Riquadro Risultati](#results-pane) 
- [Riquadro Azioni](#actions-pane) 

Inoltre, Gestione snapshot StorSimple supporta [la navigazione da tastiera e una serie di tasti di scelta rapida](#keyboard-navigation-and-shortcuts).

### Accessibilità della console

L'interfaccia utente di Gestione snapshot StorSimple supporta le funzionalità di accessibilità fornite dal sistema operativo Windows e da Microsoft Management Console (MMC), nonché alcuni tasti di scelta rapida specifici per Gestione snapshot StorSimple.

- Per una descrizione delle funzionalità di accessibilità di Windows, andare a [Tasti di scelta rapida per Windows](https://support.microsoft.com/kb/126449). 

- Per una descrizione delle funzionalità di accessibilità di MMC, andare a [Accessibilità per MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)

- Per una descrizione delle funzionalità di accessibilità di Gestione snapshot StorSimple, andare a [Navigazione da tastiera e tasti di scelta rapida](#keyboard-navigation-and-shortcuts).

## Barra dei menu

La barra dei menu nella parte superiore della finestra della console contiene i menu [**File**](#file-menu), [**Azione**](#action-menu), [**Visualizza**](#view-menu), [**Preferiti**](#favorites-menu), [**Finestra**](#window-menu) e [**Guida**](#help-menu).

Fare clic su qualsiasi elemento nella barra dei menu per visualizzare un elenco di comandi disponibili in tale menu. Nell'esempio seguente viene mostrato il menu **Visualizza** selezionato nella barra dei menu.

![Menu Visualizza selezionato](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

**Figura 2: Barra dei menu di Gestione snapshot StorSimple con il menu Visualizza selezionato**

### File menu

Il menu **File** contiene i comandi standard di Microsoft Management Console (MMC).

#### Accesso al menu

Per visualizzare il menu **File**, fare clic su **File** nella barra dei menu. Viene visualizzato il menu seguente.

![Menu File di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png)

**Figura 3: Menu File di Gestione snapshot StorSimple**

#### Descrizione del menu

Nella tabella seguente vengono descritte le voci del menu File.

| Voce di menu | Descrizione |
|:----------|:-------------|
| Nuovo | Fare clic su **Nuova** per creare una nuova console basata su Gestione snapshot StorSimple. |
| Apri | Fare clic su **Apri** per aprire una console esistente. |
| Salva | Fare clic su **Salva** per salvare la console corrente. |
| Salva con nome | Fare clic su **Salva con nome** per creare una nuova istanza rinominata della console corrente. Utilizzare l’opzione **Salva con nome** per personalizzare una visualizzazione e salvarla per un successivo recupero. Ad esempio, è possibile creare snap-in di Gestione Snapshot StorSimple che indirizzano a specifici server. |
| Aggiungi/Rimuovi snap-in | Fare clic su **Aggiungi/Rimuovi snap-in** per aggiungere o rimuovere snap-in e organizzare i nodi nel riquadro **Ambito**. Per ulteriori informazioni, andare a [Aggiunta, rimozione e organizzazione di snap-in ed estensioni in MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opzioni | Fare clic su **Opzioni** per modificare l'icona della console, specificare le modalità di accesso utente e le autorizzazioni o eliminare i file della console per aumentare lo spazio disponibile su disco. |
| Elenco dei percorsi di file | Fare clic su un percorso nell'elenco numerato per riaprire un file aperto di recente. |
| Esci | Fare clic su **Esci** per chiudere il menu **File**. |
 
### Menu Azione

Utilizzare il menu **Azione** per selezionare le azioni disponibili. Le voci disponibili dipendono dalla selezione effettuata nel riquadro **Ambito** o **Risultati**.

#### Accesso al menu

Per visualizzare il menu **Azione**, eseguire una delle operazioni seguenti:

- Fare clic con il pulsante destro del mouse su una voce nel riquadro **Ambito** o**Risultati**.

- Selezionare una voce nel riquadro **Ambito** o **Risultati**, quindi fare clic su **Azione** nella barra dei menu.

Ad esempio, se si seleziona il nodo principale nel riquadro **Ambito**, quindi si fa clic con il pulsante destro del mouse oppure si fa clic su **Azione** nella barra dei menu, viene visualizzato il menu seguente.
 
![Menu Azione di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Figura 4: Menu Azione di Gestione snapshot StorSimple**

Il riquadro **Azioni** (a destra della console) contiene lo stesso elenco di azioni del menu **Azione**. Inoltre, il riquadro **Azioni** contiene le opzioni del menu **Visualizza**, che consentono di creare una visualizzazione personalizzata del riquadro **Risultati**.

![Riquadro Azioni con menu Visualizza aperto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

**Figura 5: Riquadro Azioni di Gestione snapshot StorSimple con il menu Visualizza**

#### Descrizione del menu

Nella tabella seguente vengono elencate in ordine alfabetico le azioni di Gestione snapshot StorSimple.

- Nella colonna **Azione** vengono elencate le azioni che è possibile eseguire su nodi e risultati. 

- Nella colonna **Navigazione** viene illustrato come visualizzare il menu **Azione** appropriato, in modo da poter selezionare l’azione. Alcune azioni vengono visualizzate in più menu **Azione**. Per queste azioni, selezionare un’opzione di **Navigazione** nell'elenco puntato.

- Nella colonna **Descrizione** viene descritto come utilizzare ciascuna azione del menu **Azione** o del riquadro Azioni e viene illustrato il relativo risultato.

>[AZURE.NOTE]Il riquadro **Azioni** e il menu **Azione** contengono opzioni aggiuntive, come **Visualizza**, **Nuova finestra da qui**, **Aggiorna**, **Esporta elenco** e **Guida**. Queste opzioni sono disponibili come parte di MMC e non sono specifiche di Gestione snapshot StorSimple. Nella tabella vengono descritte tali opzioni.
 
| Azione | Navigazione | Descrizione |
|:--------|:------------|:-------------|
| Autentica | Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. | Fare clic su **Autentica** per immettere la password configurata per il dispositivo. |
| Clone | Espandere **Catalogo di backup** e **Snapshot cloud**, fare clic su un backup con data, quindi selezionare un volume nel riquadro **Risultati**. | Fare clic su **Clona** per creare una copia di uno snapshot cloud e archiviarla nella posizione designata. |
| Configurare un dispositivo | Fare clic con il pulsante destro del mouse sul nodo **Dispositivi**. | Fare clic su **Configura un dispositivo** per configurare uno o più dispositivi per la connessione all'host di Windows. |
| Crea criterio di backup | Eseguire una delle operazioni seguenti:<ul><li>Fare clic con il pulsante destro del mouse su **Criteri di backup**.</li><li>Fare clic o espandere **Gruppi di volumi**, quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li><li>Fare clic o espandere **Catalogo di backup**, quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li></ul> | Fare clic su **Crea criterio di backup** per configurare un backup pianificato per un gruppo di volumi. |
| Crea gruppo di volumi | Eseguire una delle operazioni seguenti:<ul><li>Fare clic sul nodo **Volumi**, quindi fare clic con il pulsante destro del mouse su un volume nel riquadro **Risultati**.</li><li>Fare clic con il pulsante destro del mouse sul nodo **Gruppi di volumi**.</li></ul> | Fare clic su **Crea gruppo di volumi** per assegnare i volumi a un gruppo di volumi. |
| Elimina | Fare clic su un nodo o su un risultato (questa voce viene visualizzata in diversi menu **Azione** e riquadri **Azioni**). | Fare clic su **Elimina** per eliminare il nodo o il risultato selezionato. Quando viene visualizzata la finestra di dialogo di conferma, confermare o annullare l'eliminazione. |
| Dettagli | Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. | Fare clic su **Dettagli** per visualizzare i dettagli di configurazione per un dispositivo. |
| Modificare | Fare clic su **Criteri di backup**, quindi fare clic con il pulsante destro del mouse su un criterio nel riquadro **Risultati**. | Fare clic su **Modifica** per modificare la pianificazione del backup per un gruppo di volumi. |
| Esporta elenco | Fare clic su un nodo o su un risultato (questa voce viene visualizzata in tutti i menu **Azione** e i riquadri **Azioni**). | Fare clic su **Esporta elenco** per salvare un elenco in un file con valori delimitati da virgole (CSV). È quindi possibile importare questo file in un’applicazione foglio di calcolo per l'analisi. |
| Guida | Fare clic su un nodo o su un risultato. (Questa voce viene visualizzata in tutti i menu **Azione** e i riquadri **Azioni**.) | Fare clic su **Guida** per aprire la guida in linea in una finestra separata del browser. |
| Nuova finestra da qui | Fare clic su un nodo o su un risultato (questa voce viene visualizzata in tutti i menu **Azione** e i riquadri **Azioni**). | Fare clic su **Nuova finestra da qui** per aprire una nuova finestra di Gestione snapshot StorSimple.|
| Aggiorna | Fare clic su un nodo o su un risultato (questa voce viene visualizzata in tutti i menu **Azione** e i riquadri **Azioni**). | Fare clic su **Aggiorna** per aggiornare la finestra di Gestione snapshot StorSimple attualmente visualizzata. |
| Aggiorna dispositivo | Fare clic sul nodo **Dispositivi**, quindi fare clic con il pulsante destro del mouse su un dispositivo nel riquadro **Risultati**. | Fare clic su **Aggiorna dispositivo** per sincronizzare uno specifico dispositivo connesso con Gestione snapshot StorSimple. |
| Aggiorna dispositivi | Fare clic con il pulsante destro del mouse sul nodo **Dispositivi**. | Fare clic su **Aggiorna dispositivi** per sincronizzare l'elenco dei dispositivi connessi con Gestione snapshot StorSimple. |
| Ripetere la scansione dei volumi | Fare clic con il pulsante destro del mouse sul nodo **Volumi**. | Fare clic su **Ripeti analisi dei volumi** per aggiornare l'elenco dei volumi visualizzati nel riquadro **Risultati**. |
| Ripristino | Espandere **Catalogo di backup**, espandere un gruppo di volumi, espandere **Snapshot locali** o **Snapshot cloud**, quindi fare clic con il pulsante destro del mouse su un backup. | Fare clic su **Ripristina** per sostituire i dati del gruppo di volumi corrente con i dati del backup selezionato. |
| Esegui backup | Eseguire una delle operazioni seguenti:<ul><li>Espandere **Gruppi di volumi**, quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li><li>Espandere **Catalogo di backup**, quindi fare clic con il pulsante destro del mouse su un gruppo di volumi.</li></ul> | Fare clic su **Esegui backup** per avviare immediatamente un processo di backup. |
| Visualizza/Nascondi importazioni | Fare clic con il pulsante destro del mouse sul nodo principale nel riquadro **Ambito** (il nodo **Gestione snapshot StorSimple** negli esempi). | Fare clic su **Visualizza/Nascondi importazioni** per mostrare o nascondere i gruppi di volumi e i backup associati importati dal dashboard del servizio StorSimple Manager. |

### Menu Visualizza

Utilizzare il menu **Visualizza** per creare una visualizzazione personalizzata dei contenuti del riquadro **Risultati**. Il menu **Visualizza** contiene le opzioni **Aggiungi/Rimuovi colonne** e **Personalizza**.

#### Accesso al menu

È possibile accedere al menu **Visualizza** nella barra dei menu o nel riquadro **Azioni**.

![Menu Visualizza di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

**Figura 6: Menu Visualizza di Gestione snapshot StorSimple**

#### Descrizione del menu

Nella tabella seguente vengono descritte le voci del menu **Visualizza**.

| Voce di menu | Descrizione |
|:-----------|:-------------|
| Aggiungi/Rimuovi colonne | Fare clic su **Aggiungi/Rimuovi colonne** per aggiungere o rimuovere colonne nel riquadro **Risultati**. |
| Personalizza | Fare clic su **Personalizza** per mostrare o nascondere le voci nella finestra della console di Gestione snapshot StorSimple. |

### Menu Preferiti

Utilizzare il menu **Preferiti** per aggiungere, rimuovere e organizzare le visualizzazioni della pagina e le attività utilizzate di frequente.

#### Accesso al menu

È possibile accedere al menu Preferiti nella barra dei menu.

![Menu Preferiti di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

**Figura 7: Menu Preferiti di Gestione snapshot StorSimple**

#### Descrizione del menu

Nella tabella seguente vengono descritte le voci del menu **Preferiti**.

| Voce di menu | Descrizione |
|:----------|:-------------|
| Aggiungi a Preferiti | Fare clic su **Aggiungi a Preferiti** per aggiungere la visualizzazione corrente all'elenco dei preferiti. |
| Organizza Preferiti | Fare clic su Organizza Preferiti per organizzare i contenuti della cartella Preferiti. |

### Menu Finestra

Utilizzare il menu **Finestra** per aggiungere e ridisporre le finestre della console di Gestione snapshot StorSimple.

#### Accesso al menu

È possibile accedere al menu **Finestra** nella barra dei menu.

![Menu Finestra di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

**Figura 8: Menu Finestra di Gestione snapshot StorSimple**

L'elenco numerato nella parte inferiore del menu mostra le finestre attualmente aperte. Fare clic su qualsiasi finestra dell'elenco per portarla in primo piano.

#### Descrizione del menu

Nella tabella seguente vengono descritte le voci del menu Finestra.

| Voce di menu | Descrizione |
|:-----------|:-------------|
| Nuova finestra | Fare clic su **Nuova finestra** per aprire una nuova finestra della console (in aggiunta a quella esistente). |
| Sovrapponi | Fare clic su **Sovrapponi** per visualizzare le finestre della console aperte in uno stile a cascata. |
| Affianca orizzontalmente | Fare clic su **Affianca orizzontalmente** per visualizzare le finestre della console aperte in un formato riquadro (o griglia). |
| Disponi icone | Se si dispone di più finestre della console aperte e sparse sul desktop, ridurle a icona, quindi fare clic su **Disponi icone** per disporle in una riga orizzontale nella parte inferiore dello schermo. |

### Menu Guida

Utilizzare il menu **Guida** per visualizzare la guida in linea disponibile per Gestione snapshot StorSimple e MMC. È inoltre possibile visualizzare le informazioni sulle versioni software di MMC e Gestione snapshot StorSimple attualmente installate sul sistema.

È possibile accedere al menu Guida nella barra dei menu. È inoltre possibile accedere agli argomenti della guida di Gestione snapshot StorSimple nel riquadro Azioni.

![Menu Guida di Gestione snapshot StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

**Figura 9: Menu Guida di Gestione snapshot StorSimple**

#### Descrizione del menu

Nella tabella seguente vengono descritte le voci del menu Guida.

| Voce di menu | Descrizione |
|:-----------|:-------------|
| Guida su Gestione snapshot StorSimple | Fare clic su **Guida su Gestione snapshot StorSimple** per aprire la guida di Gestione snapshot StorSimple in una finestra separata. |
| Argomenti della Guida |Fare clic su **Argomenti della Guida** per aprire la guida in linea di MMC in una finestra separata. |
| Sito Web TechCenter | Fare clic su **Sito Web TechCenter** per aprire la home page di Microsoft TechNet Tech Center in una finestra separata. |
| Informazioni su Microsoft Management Console | Fare clic su **informazioni su Microsoft Management Console** per verificare quale versione di Microsoft Management Console è installata sul sistema. |
| Informazioni su Gestione snapshot StorSimple | Fare clic su **Informazioni su Gestione snapshot StorSimple** per visualizzare quale versione dello snap-in è installata sul sistema. |

## Barra degli strumenti

La barra degli strumenti, posizionata sotto la barra dei menu, contiene le icone della navigazione e delle attività. Ciascuna icona rappresenta una scelta rapida per un'attività specifica.

### Descrizioni delle icone

Nella tabella seguente vengono descritte le icone della barra degli strumenti.

| Icona | Descrizione |
|:------|:-------------| 
| ![Freccia sinistra](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) | Fare clic sull'icona freccia sinistra per tornare alla pagina precedente. |
| ![Freccia destra](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) | Fare clic sulla freccia destra per passare alla pagina successiva (se la freccia è grigia, l'azione non è disponibile). |
| ![Icona su](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) | Fare clic sull'icona su per passare al livello successivo nell'albero della console (il riquadro **Ambito**). |
| ![Mostra/Nascondi albero della console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) | Fare clic sull'icona Mostra/Nascondi albero della console per mostrare o nascondere il riquadro **Ambito**. |
| ![Esporta elenco](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) | Fare clic sull'icona Esporta elenco per esportare un elenco in un file CSV specificato. |
| ![Icona della guida](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Fare clic sull'icona Guida per aprire un argomento della guida in linea di MMC. |
| ![Mostra/Nascondi riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) | Fare clic sull’icona Mostra/Nascondi riquadro **Azioni** per mostrare o nascondere il riquadro **Azioni**. 
 
## Riquadro Ambito

**Ambito** è il riquadro sinistro nell’interfaccia utente di Gestione snapshot StorSimple. Contiene l’albero della console (o del nodo) ed è il meccanismo di navigazione principale per Gestione snapshot StorSimple.
 
### Struttura del riquadro Ambito

Il riquadro **Ambito** contiene una serie di oggetti selezionabili (nodi) organizzati in una struttura ad albero.

![Riquadro Ambito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png)

**Figura 10: Riquadro Ambito di Gestione snapshot StorSimple**

- Per espandere o comprimere un nodo, fare clic sull'icona della freccia accanto al nome del nodo.

- Per visualizzare lo stato o i contenuti di un nodo, fare clic sul nome del nodo. Le informazioni vengono visualizzate nel riquadro **Risultati**.

Il riquadro **Ambito** contiene i nodi seguenti:

- [Nodo Dispositivi](#devices-node) 
- [Nodo Volumi](#volumes-node) 
- [Nodo Gruppi volumi](#volume-groups-node) 
- [Nodo Criteri di backup](#backup-policies-node) 
- [Nodo Catalogo di backup](#backup-catalog-node) 
- [Nodo Processi](#jobs-node) 

### Attività riquadro Ambito

Per completare un'azione su un nodo specifico, è possibile utilizzare il riquadro Ambito. Per selezionare un'attività, eseguire una delle operazioni seguenti:

- Fare clic con il pulsante destro del mouse sul nodo, quindi selezionare l'attività dal menu che viene visualizzato.

- Fare clic sul nodo, quindi fare clic su **Azione** nella barra dei menu. Selezionare l'attività dal menu che viene visualizzato.

- Fare clic sul nodo, quindi selezionare l'azione nel riquadro **Azioni**.

Quando si seleziona un nodo e si utilizza uno di questi metodi per visualizzare un elenco di attività, vengono visualizzate solo le azioni che possono essere eseguite su tale nodo.

### Nodo Dispositivi

Il nodo **Dispositivi** rappresenta i dispositivi StorSimple e i dispositivi StorSimple virtuali connessi a Gestione snapshot StorSimple. Selezionare questo nodo per connettere e configurare un dispositivo e importare i relativi volumi associati, i gruppi di volumi e le copie di backup esistenti. Più dispositivi possono essere connessi a un singolo host.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Dispositivi**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Dispositivi** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco dei dispositivi configurati, fare clic su **Dispositivi** nel riquadro **Ambito**. L'elenco dei dispositivi e le informazioni su ciascun dispositivo verranno visualizzati nel riquadro **Risultati**.

### Nodo Volumi

Il nodo Volumi rappresenta le unità che corrispondono ai volumi montati dall'host, tra cui quelli individuati tramite iSCSI e quelli individuati tramite un dispositivo. Utilizzare questo nodo per visualizzare l'elenco dei volumi disponibili e assegnare i singoli volumi a gruppi di volumi.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Volumi**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Volumi** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco dei volumi, fare clic su **Volumi** nel riquadro **Ambito**. L'elenco dei volumi e le informazioni su ciascun volume verranno visualizzati nel riquadro **Risultati**.

### Nodo Gruppi di volumi

I gruppi di volumi sono noti anche come gruppi di coerenza. Ciascun gruppo di volumi è un pool di volumi relativi all'applicazione che consente di garantire la coerenza con l'applicazione durante le operazioni di backup. Utilizzare il nodo **Gruppi di volumi** per configurare tali gruppi ed eseguire backup interattivi o creare pianificazioni di backup.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Gruppi di volumi**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Gruppi di volumi** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco dei gruppi di volumi, fare clic su **Gruppi di volumi** nel riquadro **Ambito**. L'elenco dei gruppi di volumi e le informazioni su ciascun gruppi di volumi verranno visualizzati nel riquadro **Risultati**.

### Nodo Criteri di backup

I criteri di backup sono pianificazioni dei processi per snapshot locali e cloud. Utilizzare il nodo **Criteri di backup** per specificare la frequenza di creazione di un backup e il relativo tempo di conservazione.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Criteri di backup**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Criteri di backup** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco dei criteri di backup, fare clic su **Criteri di backup** nel riquadro **Ambito**. L'elenco dei criteri di backup e le informazioni su ciascun criterio verranno visualizzati nel riquadro **Risultati**.

>[AZURE.NOTE]È possibile conservare un massimo di 64 backup.


### Nodo Catalogo di backup

Il nodo **Catalogo di backup** contiene gli elenchi dei backup locali in sede e fuori sede dei volumi StorSimple di Azure. Questo nodo è organizzato per gruppo di volumi e il contenitore di ciascun gruppo di volumi contiene strutture separate per gli snapshot locali (il nodo Snapshot locali) e gli snapshot cloud (il nodo Snapshot cloud). Se viene espanso, il contenitore di ciascun gruppo di volumi elenca tutti i backup riusciti che sono stati eseguiti in modo interattivo o da un criterio configurato.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Catalogo di backup**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Catalogo di backup** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco degli snapshot di backup, fare clic su **Catalogo di backup** nel riquadro **Ambito**. L'elenco degli snapshot e le informazioni su ciascuno snapshot verranno visualizzati nel riquadro **Risultati**.

### Nodo Snapshot locali

Il nodo **Snapshot locali** elenca gli snapshot locali per un gruppo di volumi specifico. Il nodo si trova sotto **Catalogo di backup** nel riquadro **Ambito**. Gli snapshot locali sono copie temporizzate dei dati del volume archiviati sul dispositivo StorSimple di Azure. In genere, questo tipo di backup può essere creato e ripristinato rapidamente. È possibile utilizzare uno snapshot locale come copia di backup locale.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Snapshot locali**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Snapshot locali** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco di snapshot locali, fare clic su **Snapshot locali** nel riquadro **Ambito**. L'elenco degli snapshot e le informazioni su ciascuno snapshot verranno visualizzati nel riquadro **Risultati**.

### Nodo Snapshot cloud

Il nodo **Snapshot cloud** elenca gli snapshot cloud per un gruppo di volumi specifico. Il nodo si trova sotto **Catalogo di backup** nel riquadro **Ambito**. Gli snapshot Cloud sono copie temporizzate di dati del volume archiviati nel cloud. Uno snapshot cloud equivale a uno snapshot replicato su un diverso sistema di archiviazione fuori sede. Gli snapshot cloud sono particolarmente utili negli scenari di ripristino di emergenza.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Snapshot cloud**.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Snapshot cloud** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco di snapshot cloud, fare clic su **Snapshot cloud** nel riquadro **Ambito**. L'elenco degli snapshot e le informazioni su ciascuno snapshot verranno visualizzati nel riquadro **Risultati**.

### Nodo Processi

Il nodo **Processi** contiene le informazioni sui processi di backup pianificati, in esecuzione e completati di recente.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a Processi.

- Per visualizzare un menu di azioni disponibili, fare clic con il pulsante destro del mouse sul nodo **Processi** oppure su uno dei nodi presenti nella visualizzazione espansa.

- Per visualizzare un elenco dei processi pianificati, espandere il nodo **Processi**, quindi fare clic su **Pianificati**. L’elenco dei processi configurati in precedenza e le informazioni su ciascun processo vengono visualizzati nel riquadro **Risultati**.

- Per visualizzare un elenco dei processi completati di recente, espandere il nodo **Processi**, quindi fare clic su **Ultime 24 ore**. L’elenco dei processi completati nelle ultime 24 viene visualizzato nel riquadro **Risultati**. Il riquadro **Risultati** contiene anche le informazioni su ciascun processo completato.

- Per visualizzare un elenco dei processi attualmente in esecuzione, espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. L’elenco dei processi attualmente in esecuzione e le informazioni su ciascun processo vengono visualizzati nel riquadro **Risultati**.

## Riquadro Risultati

**Risultati** è il riquadro centrale nell’interfaccia grafica di Gestione snapshot StorSimple. Contiene elenchi e informazioni dettagliate sullo stato per il nodo selezionato nel riquadro **Ambito**.

### Esempio

Per visualizzare l'esempio seguente, fare clic sul nodo **Gruppi di volumi** nel riquadro **Ambito**. Il riquadro **Risultati** contiene un elenco dei gruppi di volumi con informazioni dettagliate su ciascun gruppo.

![Riquadro Risultati](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png)

**Figura 11: Riquadro Risultati di Gestione snapshot StorSimple**

È possibile configurare i dettagli mostrati nel riquadro **Risultati**: fare clic con il pulsante destro del mouse su un nodo nel riquadro **Ambito**, fare clic su **Visualizza**, quindi fare clic su **Aggiungi/Rimuovi colonne**.

## Riquadro Azioni

**Ambito** è il riquadro destro nell’interfaccia utente di Gestione snapshot StorSimple. Contiene un menu di operazioni che è possibile eseguire sulla visualizzazione, sulla data o sul nodo selezionato nel riquadro **Ambito** o **Risultati**. Il riquadro **Azioni** contiene gli stessi comandi dei menu **Azione** disponibili per le voci nei riquadri **Ambito** e **Risultati**. Per una descrizione di ciascuna azione, vedere la tabella nella sezione relativa al menu **Azione**.

### esempi

Per vedere l’esempio seguente, nel riquadro **Ambito**, espandere il nodo **Processi**, quindi fare clic su **Pianificati**. Il riquadro **Azioni** contiene le azioni disponibili per il nodo **Pianificati**.

![Esempio processi pianificati nel riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png)

**Figura 12: Riquadro Azioni di Gestione snapshot StorSimple (processi pianificati)**

Per visualizzare ulteriori opzioni, nel riquadro **Ambito**, espandere il nodo **Processi**, fare clic su **Pianificati**, quindi fare clic su un processo pianificato nel riquadro **Risultati**. Il riquadro **Azioni** contiene le azioni disponibili per il processo pianificato, come mostrato nell'esempio seguente.

![Esempio azioni del processo nel riquadro Azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

**Figura 13: Riquadro Azioni di Gestione snapshot StorSimple (per un processo specifico)**

## Navigazione da tastiera e tasti di scelta rapida

Gestione snapshot StorSimple abilita le funzionalità di accessibilità del sistema operativo Windows e di Microsoft Management Console (MMC). Include inoltre alcune funzionalità di navigazione da tastiera e i tasti di scelta rapida specifici di Gestione snapshot StorSimple, come descritto nelle sezioni seguenti.
 
- [Tasti di navigazione da tastiera](#keyboard-navigation-keys) 
- [Tasti di scelta rapida della barra dei menu](#menu-bar-shortcut-keys) 
- [Tasti di scelta rapida del riquadro Ambito](#scope-pane-shortcut-keys) 

### Tasti di navigazione da tastiera

Nella tabella seguente vengono descritti i tasti che è possibile utilizzare per spostarsi nell'interfaccia utente di Gestione snapshot StorSimple.

| Tasto di navigazione | Azione |
|:----------------|:--------| 
| Tasto freccia giù | Utilizzare il tasto freccia giù per spostarsi in senso verticale alla voce successiva in un menu o in un riquadro. |
| Immettere | Premere il tasto INVIO per completare un'azione e procedere al passaggio successivo. Ad esempio, è possibile premere INVIO per selezionare **Avanti**, **OK** o **Crea** e procedere al passaggio successivo in una procedura guidata.|
| ESC | Premere il tasto ESC per chiudere un menu oppure per annullare e chiudere una pagina.|
| F1 | Premere il tasto F1 per visualizzare un argomento della guida per la finestra attiva corrente.|
| F5 | Premere il tasto F5 per aggiornare un nodo. |
| F6 | Premere il tasto F6 per spostarsi dal riquadro **Ambito** al riquadro **Risultati**.|
| F10 | Premere il tasto F10 per passare alla barra dei menu. |
| Tasto freccia sinistra | Utilizzare il tasto freccia sinistra per spostarsi in senso orizzontale da un'opzione della barra dei menu a quella precedente. Quando ci si sposta sulla voce precedente nella barra dei menu, viene visualizzato il menu Azione (o Contesto) per la voce precedente. |
| Tasto freccia destra | Utilizzare il tasto freccia destra per spostarsi in senso orizzontale da un’opzione della barra dei menu a quella successiva. Quando ci si sposta sulla voce successiva nella barra dei menu, viene visualizzato il menu Azione (o Contesto) per la nuova voce.
| Tasto TAB | Utilizzare il tasto TAB per spostarsi al riquadro successivo nella console oppure alla selezione o alla casella di testo successiva in una pagina. |
| Tasto freccia su | Utilizzare il tasto freccia su per spostarsi in senso verticale alla voce precedente in un menu o in un riquadro. |

### Tasti di scelta rapida della barra dei menu

Nella tabella seguente vengono descritte le combinazioni di tasti di scelta rapida per la barra dei menu. Dopo aver aperto i menu premendo i tasti di scelta rapida, è possibile utilizzare i tasti di scelta rapida del menu (i tasti sottolineati nel menu). Per ulteriori informazioni sulla barra dei menu, andare a [Barra dei menu](#menu-bar).

| Tasto di scelta rapida | Risultato | Tasti di scelta rapida del menu | Risultato |
|:---------|:--------------------------|:------------------|:----------------|
| ALT + F | Consente di aprire il menu **File**. | N | Consente di aprire l’istanza di una nuova console. |
| | | O | Consente di aprire la pagina **Strumenti di amministrazione**. |
| | | S | Consente di salvare la console di Gestione snapshot StorSimple.|
| | | A | Consente di aprire la pagina **Salva con nome**. |
| | | M | Consente di aprire la pagina **Aggiungi/Rimuovi snap-in**.|
| | | P | Consente di aprire la pagina **Opzioni**. |
| | | H | Consente di aprire la guida in linea.|
| ALT + A | Consente di aprire il menu **Azione**.| I | Consente di attivare e disattivare l'opzione di visualizzazione dell’importazione.|
| | | W | Consente di aprire una nuova console di Gestione snapshot StorSimple.|
| | | F | Consente di aggiornare la console di Gestione snapshot StorSimple.|
| | | L | Consente di aprire la pagina **Esporta elenco**. 
| | | H | Consente di aprire la guida in linea.|
| ALT + V | Consente di aprire il menu **Visualizza**. | A | Consente di aprire la pagina **Aggiungi/Rimuovi colonne**. |
| | | U | Consente di aprire la pagina **Personalizza visualizzazione**. |
| ALT + O | Consente di aprire il menu **Preferiti**. | A | Consente di aprire la pagina **Aggiungi a Preferiti**. |
| | | O | Consente di aprire la pagina **Organizza Preferiti**.|
| ALT + W | Consente di aprire il menu **Finestra**.| N | Consente di aprire un'altra finestra di Gestione snapshot StorSimple.|
| | | C | Consente di visualizzare tutte le finestre della console aperte in uno stile a cascata.|
| | | T | Consente di visualizzare tutte le finestre della console aperte in motivo di griglia. |
| | | I | Consente di disporre le icone su una riga orizzontale nella parte inferiore dello schermo.|
| ALT + H | Consente di aprire il menu **Guida**. | H | Consente di aprire la guida in linea.|
| | | T | Consente di aprire la pagina Web di Microsoft TechNet Tech Center.|
| | | A | Consente di aprire la pagina **Informazioni su Microsoft Management Console**. |
 
### Tasti di scelta rapida del riquadro Ambito

Nelle tabelle seguenti vengono illustrate le combinazioni dei tasti di scelta rapida per ciascun nodo nel riquadro **Ambito**.

- [Tasti di scelta rapida del nodo Dispositivi](#devices-node-shortcut-keys)
- [Tasti di scelta rapida del nodo Volumi](#volumes-node-shortcut-keys)
- [Tasti di scelta rapida del nodo Gruppi di volumi](#volume-groups-node-shortcut-keys)
- [Tasti di scelta rapida del nodo Criteri di backup](#backup-policies-node-shortcut-keys)
- [Tasti di scelta rapida del nodo Catalogo di backup](#backup-catalog-node-shortcut-keys)
- [Tasti di scelta rapida del nodo Processi](#jobs-node-shortcut-keys)

#### Tasti di scelta rapida del nodo Dispositivi

| Tasti di scelta rapida del menu | Risultato |
|:--------------|:-------------------------------------|
| C | Consente di aprire la pagina **Configura un dispositivo**. |
| D | Consente di aggiornare l'elenco di dispositivi e i dettagli del dispositivo.|
| V | Consente di aprire il menu **Visualizza**. |
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Dettagli**. |
| F | Consente di aggiornare la console di Gestione snapshot StorSimple. |
| L | Consente di aprire la pagina **Esporta elenco**. 
| H | Consente di aprire la guida in linea.|
 

#### Tasti di scelta rapida del nodo Volumi

| Tasti di scelta rapida del menu | Risultato |
|:----------------|:------------------------------------|
| V | Consente di aggiornare l'elenco dei volumi. |
| V (premere due volte) | Consente di aprire il menu **Visualizza**. |
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Volumi**.|
| F | Consente di aggiornare la console di Gestione snapshot StorSimple.|
| L | Consente di aprire la pagina **Esporta elenco**. 
| H | Consente di aprire la guida in linea.|
 
#### Tasti di scelta rapida del nodo Gruppi di volumi

| Tasti di scelta rapida del menu | Risultato |
|:----------------|:------------------------------------|
| G | Consente di aprire la pagina **Crea un gruppo di volumi**. |
| V | Consente di aprire il menu **Visualizza**. |
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Gruppi di volumi**.|
| F | Consente di aggiornare la console di Gestione snapshot StorSimple. |
| L | Consente di aprire la pagina **Esporta elenco**. |
| H | Consente di aprire la guida in linea.|

#### Tasti di scelta rapida del nodo Criteri di backup

| Tasti di scelta rapida del menu | Risultato |
|:----------------|:------------------------------------|
| B | Consente di aprire la pagina **Crea un criterio**. |
| V | Consente di aprire il menu **Visualizza**. |
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Gruppi di volumi**.|
| F | Consente di aggiornare la console di Gestione snapshot StorSimple.|
| L | Consente di aprire la pagina **Esporta elenco**. | H | Consente di aprire la guida in linea.|
 
#### Tasti di scelta rapida del nodo Catalogo di backup

| Tasti di scelta rapida del menu | Risultato |
|:----------------|:------------------------------------|
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Gruppi di volumi**. |
| F | Consente di aggiornare la console di Gestione snapshot StorSimple. |
| H | Consente di aprire la guida in linea.|
 
#### Tasti di scelta rapida del nodo Processi

| Tasti di scelta rapida del menu | Risultato |
|:----------------|:------------------------------------|
| V | Consente di aprire il menu **Visualizza**. |
| W | Consente di aprire una nuova console di Gestione snapshot StorSimple incentrata sul nodo **Processi**.|
| F | Consente di aggiornare la console di Gestione snapshot StorSimple.|
| L | Consente di aprire la pagina **Esporta elenco**. |
| H | Consente di aprire la guida in linea. |
 
## Passaggi successivi

Leggere le informazioni su come [utilizzare Gestione snapshot StorSimple per connettere e gestire i dispositivi](storsimple-snapshot-manager-manage-devices.md).

<!---HONumber=August15_HO6-->