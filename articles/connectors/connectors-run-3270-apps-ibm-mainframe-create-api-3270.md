---
title: Connettersi a 3270 app su mainframe IBM
description: Integrare e automatizzare le app basate sullo schermo 3270 con Azure usando Le app per la logica di Azure e il connettore IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371110"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrare le app basate su schermo 3270 nei mainframe IBM con Azure usando Azure Logic Apps e il connettore IBM 3270

> [!NOTE]
> Questo connettore è in [*anteprima pubblica.*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

Con le app per la logica di Azure e il connettore IBM 3270, è possibile accedere ed eseguire le app mainframe IBM che in genere si guidano navigando attraverso le schermate dell'emulatore 3270. In questo modo, è possibile integrare le app mainframe IBM con Azure, Microsoft e altre app, servizi e sistemi creando flussi di lavoro automatizzati con App per la logica di Azure.This way, you can integrate your IBM mainframe apps with Azure, Microsoft, and other apps, services, and systems by creating automated workflows with Azure Logic Apps. Il connettore comunica con i mainframe IBM usando il protocollo TN3270 ed è disponibile in tutte le aree delle app per la logica di Azure, ad eccezione di Azure per enti pubblici e Azure China 21Vianet. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

In questo articolo vengono descritti questi aspetti per l'utilizzo del connettore 3270: 

* Perché usare il connettore IBM 3270 in Azure Logic Apps e come il connettore esegue le app basate su schermo 3270

* Prerequisiti e configurazione per l'utilizzo del connettore 3270

* Passaggi per l'aggiunta di azioni del connettore 3270 all'app per la logicaThe steps for adding 3270 connector actions to your logic app

## <a name="why-use-this-connector"></a>Perché utilizzare questo connettore?

Per accedere alle app sui mainframe IBM, in genere si utilizza un emulatore di terminale 3270, spesso chiamato "schermo verde". Questo metodo è un modo collaudato nel tempo, ma ha limitazioni. Anche se Host Integration Server (HIS) consente di lavorare direttamente con queste app, a volte, separare lo schermo e la logica di business potrebbe non essere possibile. Oppure, forse non hai più informazioni su come funzionano le applicazioni host.

Per estendere questi scenari, il connettore IBM 3270 in Azure Logic Apps funziona con lo strumento di progettazione 3270, che si usa per registrare, o "catturare", le schermate host usate per un'attività specifica, definire il flusso di navigazione per l'attività tramite l'app mainframe e definire metodi con parametri di input e output per tale attività. Lo strumento di progettazione converte tali informazioni in metadati utilizzati dal connettore 3270 quando si chiama un'azione che rappresenta tale attività dall'app per la logica.

Dopo aver generato il file di metadati dallo strumento di progettazione, è possibile aggiungerlo a un account di integrazione in Azure.After you generate the metadata file from the design tool, you add that file to an integration account in Azure. In questo modo, l'app per la logica può accedere ai metadati dell'app quando si aggiunge un'azione connettore 3270. Il connettore legge il file di metadati dall'account di integrazione, gestisce la navigazione nelle schermate 3270 e presenta dinamicamente i parametri per l'azione del connettore 3270. È quindi possibile fornire dati all'applicazione host e il connettore restituisce i risultati all'app per la logica. In questo modo, è possibile integrare le app legacy con Azure, Microsoft e altre app, servizi e sistemi supportati dalle app per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Consigliato: un ambiente del servizio di [integrazione (ISE)Recommended: An integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  È possibile selezionare questo ambiente come percorso per la creazione e l'esecuzione dell'app per la logica. Un ISE fornisce l'accesso dall'app per la logica alle risorse protette all'interno di reti virtuali di Azure.An ISE provides access from your logic app to resources that are protected within Azure virtual networks.

* App per la logica da usare per l'automazione e l'esecuzione dell'app 3270 basata su schermo

  Il connettore IBM 3270 non dispone di trigger, quindi usa un altro trigger per avviare l'app per la logica, ad esempio il trigger **Ricorrenza.** È quindi possibile aggiungere azioni del connettore 3270.You can then add 3270 connector actions. Per iniziare, [creare un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Se usi ISE, seleziona lo STESSO ISE come posizione dell'app per la logica.

* [Scaricare e installare 3270 Design Tool](https://aka.ms/3270-design-tool-download).
L'unico prerequisito è [Microsoft .NET Framework 4.6.1.](https://aka.ms/net-framework-download)

  Questo strumento consente di registrare le schermate, i percorsi di navigazione, i metodi e i parametri per le attività nell'app aggiunte ed eseguite come azioni connettore 3270. Lo strumento genera un file HIDX (Host Integration Designer XML) che fornisce i metadati necessari per il connettore da usare per guidare l'app mainframe.
  
  Dopo aver scaricato e installato questo strumento, attenersi alla seguente procedura per la connessione all'host:

  1. Aprire lo strumento di progettazione 3270. Dal menu **Sessione,** selezionare **Host Sessions**.
  
  1. Fornire le informazioni sul server host TN3270.

* Un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), ovvero la posizione in cui archiviare il file HIDX come mappa in modo che l'app per la logica possa accedere ai metadati e alle definizioni dei metodi in tale file. 

  Assicurati che l'account di integrazione sia collegato all'app per la logica in uso. Inoltre, se usi ISE, assicurati che la posizione dell'account di integrazione sia la stessa utilizzata dall'app per la logica.

* Accesso al server TN3270 che ospita l'app mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Creare una panoramica dei metadati

In un'app basata su schermate 3270, le schermate e i campi dati sono univoci per gli scenari in desiderati, pertanto il connettore 3270 necessita di queste informazioni sull'app, che è possibile fornire come metadati. Questi metadati descrivono le informazioni che consentono all'app per la logica di identificare e riconoscere le schermate, viene descritto come spostarsi tra le schermate, dove immettere i dati e dove aspettarsi risultati. Per specificare e generare questi metadati, utilizzare lo strumento di progettazione 3270, che illustra le *modalità*specifiche, o fasi, come descritto più avanti in ulteriori dettagli:

* **Cattura**: In questa modalità, si registrano le schermate necessarie per completare un'attività specifica con l'app mainframe, ad esempio per ottenere un saldo bancario.

* **Navigazione:** in questa modalità, si specifica il piano o il percorso per la navigazione nelle schermate dell'app mainframe per l'attività specifica.

* **Metodi**: in questa modalità viene definito `GetBalance`il metodo, ad esempio , che descrive il percorso di navigazione dello schermo. È inoltre possibile scegliere i campi in ogni schermata che diventano i parametri di input e output del metodo.

### <a name="unsupported-elements"></a>Elementi non supportati

Lo strumento di progettazione non supporta questi elementi:The design tool doesn't support these elements:

* Mappe BMS (Partial IBM Basic Mapping Support): se si importa una mappa BMS, lo strumento di progettazione ignora le definizioni parziali dello schermo.
* Parametri In/Out: non è possibile definire parametri In/Out.
* Elaborazione dei menu: non supportata durante l'anteprima
* Elaborazione della matrice: non supportata durante l'anteprimaArray processing: Not supported during Preview

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Schermate di acquisizione

In questa modalità, si contrassegna un elemento su ogni schermata 3270 che identifica in modo univoco tale schermata. Ad esempio, è possibile specificare una riga di testo o un insieme di condizioni più complesso, ad esempio testo specifico e un campo non vuoto. È possibile registrare queste schermate tramite una connessione attiva al server host oppure importare queste informazioni da una mappa BMS (IBM Basic Mapping Support). La connessione attiva utilizza un emulatore TN3270 per la connessione all'host. Ogni azione del connettore deve essere mappata a una singola attività che inizia con la connessione alla sessione e termina con la disconnessione dalla sessione.

1. Se non l'hai già fatto, apri lo strumento di progettazione 3270. Sulla barra degli strumenti scegliere **Acquisisci** per attivare la modalità di acquisizione.

1. Per avviare la registrazione, premere il tasto F5 oppure scegliere **Avvia registrazione**dal menu **Registrazione** . 

1. Dal menu **Sessione,** selezionare **Connetti**.

1. Nel riquadro **Acquisizione,** a partire dalla prima schermata dell'app, esegui l'app un'istruzione tramite l'attività specifica che stai registrando.

1. Dopo aver completato l'attività, disconnettersi dall'app come di consueto.

1. Dal menu **Sessione,** selezionare **Disconnetti**.

1. Per interrompere la registrazione, premere i tasti Maiusc e F5 oppure dal menu **Registrazione,** selezionare **Interrompi registrazione**.

   Dopo aver acquisito le schermate per un'attività, lo strumento di progettazione mostra le anteprime che rappresentano tali schermate. Alcune note su queste miniature:

   * Incluso con le schermate catturate, si dispone di uno schermo che è chiamato "Vuoto".

     Quando ci si connette per la prima volta a [CICS](https://www.ibm.com/it-infrastructure/z/cics), è necessario inviare la chiave "Cancella" prima di poter immettere il nome della transazione che si desidera eseguire. La schermata in cui si invia il tasto "Cancella" non ha attributi di *riconoscimento,* ad esempio il titolo di una schermata, che è possibile aggiungere utilizzando l'editor di riconoscimento dello schermo. Per rappresentare questa schermata, le miniature includono una schermata denominata "Vuoto". In seguito è possibile utilizzare questa schermata per rappresentare la schermata in cui si immette il nome della transazione.

   * Per impostazione predefinita, il nome di una schermata acquisita utilizza la prima parola sullo schermo. Se tale nome esiste già, lo strumento di progettazione aggiunge al nome un segno di sottolineatura e un numero, ad esempio "WBGB" e "WBGB_1".

1. Per assegnare un nome più significativo a una schermata acquisita, attenersi alla seguente procedura:

   1. Nel riquadro **Schermate host** selezionare la schermata che si desidera rinominare.

   1. Nello stesso riquadro, nella parte inferiore dello stesso riquadro, individuare la proprietà **Nome schermata.**

   1. Modificare il nome della schermata corrente con un nome più descrittivo.

1. Specificare ora i campi per l'identificazione di ogni schermata.

   Con il flusso di dati 3270, le schermate non hanno identificatori predefiniti, pertanto è necessario selezionare testo univoco in ogni schermata. Per scenari complessi, è possibile specificare più condizioni, ad esempio testo univoco e un campo con una condizione specifica.

Dopo aver selezionato i campi di riconoscimento, passare alla modalità successiva.

### <a name="conditions-for-identifying-repeated-screens"></a>Condizioni per l'identificazione di schermi ripetuti

Per il connettore di navigare e distinguere tra le schermate, in genere si trova testo univoco in una schermata che è possibile utilizzare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbero essere necessari più metodi di identificazione. Si supponga, ad esempio, di avere due schermate che hanno lo stesso aspetto tranne una schermata restituisce un valore valido, mentre l'altra schermata restituisce un messaggio di errore.

Nello strumento di progettazione è possibile aggiungere attributi di *riconoscimento,* ad esempio un titolo di schermata, ad esempio "Ottieni saldo account", utilizzando l'editor di riconoscimento dello schermo. Se si dispone di un percorso biforcato ed entrambi i rami restituiscono la stessa schermata ma con risultati diversi, sono necessari altri attributi di riconoscimento. In fase di esecuzione, il connettore utilizza questi attributi per determinare il ramo e il fork correnti. Ecco le condizioni che puoi utilizzare:

* Valore specifico: questo valore corrisponde alla stringa specificata nella posizione specificata.
* NON un valore specifico: questo valore non corrisponde alla stringa specificata nella posizione specificata.
* Vuoto: questo campo è vuoto.
* NOT vuoto: questo campo non è vuoto.

Per altre informazioni, vedere il piano di spostamento di [esempio](#example-plan) più avanti in questo argomento.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definire i piani di navigazione

In questa modalità si definiscono il flusso o i passaggi per spostarsi tra le schermate dell'app mainframe per l'attività specifica. Ad esempio, a volte, potresti avere più di un percorso che l'app può prendere nel punto in cui un percorso produce il risultato corretto, mentre l'altro percorso genera un errore. Per ogni schermata, specificare le sequenze di tasti `CICSPROD <enter>`necessarie per passare alla schermata successiva, ad esempio .

> [!TIP]
> Se si automatizzano diverse attività che utilizzano le stesse schermate di connessione e disconnessione, lo strumento di progettazione fornisce tipi di piano Connetti e Disconnetti speciali. Quando si definiscono questi piani, è possibile aggiungerli all'inizio e alla fine del piano di navigazione.

### <a name="guidelines-for-plan-definitions"></a>Linee guida per le definizioni di piano

* Includere tutte le schermate, iniziando con la connessione e terminando con la disconnessione.

* È possibile creare un piano autonomo o utilizzare i piani Connetti e Disconnetti, che consentono di riutilizzare una serie di schermate comuni a tutte le transazioni.

  * L'ultima schermata del piano Connect deve essere la stessa della prima schermata del piano di navigazione.

  * La prima schermata del piano Disconnetti deve essere la stessa schermata dell'ultima schermata del piano di navigazione.

* Gli schermi acquisiti potrebbero contenere molte schermate ripetute, quindi selezionare e utilizzare una sola istanza di tutte le schermate ripetute nel piano. Ecco alcuni esempi di schermate ripetute:

  * La schermata di accesso, ad esempio la schermata **MSG-10**
  * La schermata di benvenuto per il CICS
  * Schermata "Cancella" o **Vuota**

<a name="create-plans"></a>

### <a name="create-plans"></a>Creare piani

1. Nella barra degli strumenti di 3270 Design Tool, scegliere **Navigazione** per attivare la modalità di navigazione.

1. Per iniziare il piano, nel riquadro **di spostamento** scegliere **Nuovo piano**.

1. In **Scegli nuovo nome piano**immettere un nome per il piano. Dall'elenco **Tipo,** selezionare il tipo di piano:

   | Tipo di piano | Descrizione |
   |-----------|-------------|
   | **Processo** | Per piani autonomi o combinati |
   | **Connetti** | Per i piani Connect |
   | **Scollegare** | Per i piani di disconnessione |
   |||

1. Dal riquadro **Schermate host** trascinare le miniature acquisite nell'area del piano di navigazione nel riquadro **di spostamento.**

   Per rappresentare la schermata vuota in cui si immette il nome della transazione, utilizzare la schermata "Vuoto".

1. Disporre le schermate nell'ordine che descrive l'attività che si sta definendo.

1. Per definire il percorso di flusso tra le schermate, inclusi fork e giunti, sulla barra degli strumenti dello strumento di progettazione scegliere **Flusso**.

1. Scegliere la prima schermata del flusso. Trascinare e disegnare una connessione alla schermata successiva del flusso.

1. Per ogni schermata, specificare i valori per la proprietà **AID Key** (Identificatore di attenzione) e per la proprietà **Fixed Text,** che sposta il flusso alla schermata successiva.

   È possibile che siano disponibili solo il tasto AID oppure sia la chiave AID che il testo fisso.

Dopo aver completato il piano di navigazione, è possibile definire i [metodi nella modalità successiva.](#define-method)

<a name="example-plan"></a>

### <a name="example"></a>Esempio

In questo esempio, si supponga di eseguire una transazione CICS denominata "WBGB" con questi passaggi: 

* Nella prima schermata, si immettono un nome e un account.
* Nella seconda schermata, si ottiene il saldo del conto.
* Si esce alla schermata "Vuoto".
* Disconnettersi da CICS alla schermata "MSG-10".

Si supponga inoltre di ripetere questi passaggi, ma si immettono dati non corretti in modo da poter acquisire la schermata che mostra l'errore. Ecco le schermate acquisite:

* MSG-10
* CICS Benvenuti
* Empty
* WBGB_1 (input)
* WBGB_2 (errore)
* Empty_1
* File MSG-10_1

Anche se molte schermate qui ottengono nomi univoci, alcune schermate sono la stessa schermata, ad esempio, "MSG-10" e "Empty". Per una schermata ripetuta, usa una sola istanza per quella schermata nel tuo piano. Ecco alcuni esempi che illustrano l'aspetto di un piano autonomo, di un piano Connetti, di disconnetti e di un piano combinato:Here are examples that show how a standalone plan, Connect plan, Disconnect plan, and a combined plan might look:

* Piano autonomo

  ![Piano di navigazione autonomo](./media/connectors-create-api-3270/standalone-plan.png)

* Piano di connessione

  ![Piano di connessione](./media/connectors-create-api-3270/connect-plan.png)

* Disconnetti piano

  ![Disconnetti piano](./media/connectors-create-api-3270/disconnect-plan.png)

* Piano combinato

  ![Piano combinato](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Esempio: identificare schermate ripetute

Per il connettore di navigare e differenziare le schermate, in genere si trova testo univoco in una schermata che è possibile utilizzare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbero essere necessari più metodi di identificazione. Il piano di esempio ha un fork in cui è possibile ottenere schermate simili. Una schermata restituisce il saldo di un conto, mentre l'altra schermata restituisce un messaggio di errore.

Lo strumento di progettazione consente di aggiungere attributi di riconoscimento, ad esempio un titolo di schermata denominato "Ottieni saldo account", utilizzando l'editor di riconoscimento dello schermo. Nel caso con schermi simili, sono necessari altri attributi. In fase di esecuzione, il connettore utilizza questi attributi per determinare il ramo e il fork.

* Nel ramo che restituisce un input valido, ovvero la schermata con il saldo del conto, è possibile aggiungere un campo con una condizione "non vuota".

* Nel ramo che restituisce un errore, è possibile aggiungere un campo con una condizione "vuota".

<a name="define-method"></a>

## <a name="define-methods"></a>Definire i metodi

In questa modalità viene definito un metodo associato al piano di spostamento. Per ogni parametro del metodo, specificare il tipo di dati, ad esempio una stringa, un numero intero, una data o un'ora e così via. Al termine, è possibile testare il metodo nell'host attivo e verificare che il metodo funzioni come previsto. Viene quindi generato il file di metadati o il file HIDX (Host Integration Designer XML), che ora contiene le definizioni dei metodi da utilizzare per la creazione e l'esecuzione di un'azione per il connettore IBM 3270.

1. Nella barra degli strumenti di 3270 Design Tool, scegliere **Metodi** per attivare la modalità Metodi. 

1. Nel riquadro **di spostamento** selezionare la schermata contenente i campi di input desiderati.

1. Per aggiungere il primo parametro di input per il metodo, attenersi alla seguente procedura:

   1. Nella schermata dell'emulatore 3270 del riquadro **Acquisizione** scegliere l'intero campo, non solo il testo all'interno del campo, che si desidera come primo input.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completo, scegliere **Tutti i campi**dal menu **Visualizza.**

   1. Sulla barra degli strumenti dello strumento di progettazione scegliere **Campo di input**. 

   Per aggiungere altri parametri di input, ripetere i passaggi precedenti per ogni parametro.

1. Per aggiungere il primo parametro di output per il metodo, attenersi alla seguente procedura:

   1. Nella schermata dell'emulatore 3270 del riquadro **Acquisizione** scegliere l'intero campo, non solo il testo all'interno del campo, che si desidera come primo output.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completo, scegliere **Tutti i campi**dal menu **Visualizza.**

   1. Sulla barra degli strumenti dello strumento di progettazione scegliere **Campo di output**.

   Per aggiungere altri parametri di output, ripetere i passaggi precedenti per ogni parametro.

1. Dopo aver aggiunto tutti i parametri del metodo, definire queste proprietà per ogni parametro:After you add your method's parameters, define these properties for each parameter:

   | Nome proprietà | Valori possibili | 
   |---------------|-----------------|
   | **Tipo di dati** | Byte, Data ora, Decimale, Int, Long, Short, Stringa |
   | **Tecnica di riempimento campo** | I parametri supportano questi tipi di riempimento, riempiendo con spazi vuoti se necessario: <p><p>- **Tipo**: Immettere i caratteri in sequenza nel campo. <p>- **Riempi**: Sostituisce il contenuto del campo con caratteri, riempiendo con spazi vuoti se necessario. <p>- **EraseEofType**: Cancellare il campo, quindi immettere i caratteri in sequenza nel campo. |
   | **Stringa formato** | Alcuni tipi di dati di parametro utilizzano una stringa di formato, che informa il connettore 3270 come convertire il testo dallo schermo in un tipo di dati .NET:Some parameter data types use a format string, which informs the 3270 connector how to convert text from the screen into a .NET data type: <p><p>- **DateTime**: La stringa di formato DateTime segue le stringhe di [formato di data e ora personalizzate .NET.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) Ad esempio, `06/30/2019` la data `MM/dd/yyyy`utilizza la stringa di formato . <p>- **Decimale**: La stringa di formato decimale utilizza la [clausola COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Ad esempio, `100.35` il numero `999V99`utilizza la stringa di formato . |
   |||

## <a name="save-and-view-metadata"></a>Salvare e visualizzare i metadati

Dopo aver definito il metodo, ma prima di testare il metodo, salvare tutte le informazioni definite finora in un file RAP (con estensione rap).
È possibile salvare in questo file RAP in qualsiasi momento durante qualsiasi modalità. Lo strumento di progettazione include anche un file RAP di esempio che è possibile aprire ed esaminare accedendo alla cartella di installazione dello strumento di progettazione in questa posizione e aprendo il file "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Tuttavia, se si tenta di salvare le modifiche al file RAP di esempio o di generare un file HIDX dal file RAP di esempio mentre il file rimane nella cartella di installazione dello strumento di progettazione, è possibile che venga visualizzato un errore di "accesso negato". Per impostazione predefinita, lo strumento di progettazione viene installato nella cartella Programmi senza autorizzazioni elevate. Se viene visualizzato un errore, provare una di queste soluzioni:

* Copiare il file di esempio in un percorso diverso.
* Eseguire lo strumento di progettazione come amministratore.
* Impostare il proprietario della cartella SDK.

## <a name="test-your-method"></a>Testare il metodo

1. Per eseguire il metodo sull'host attivo, in modalità Metodi, premere il tasto F5 o dalla barra degli strumenti dello strumento di progettazione, scegliere **Esegui**.

   > [!TIP]
   > È possibile cambiare modalità in qualsiasi momento. Scegliere **Modalità**dal menu **File,** quindi selezionare la modalità desiderata.

1. Immettere i valori dei parametri e scegliere **OK**.

1. Per passare alla schermata successiva, scegliere **Avanti**.

1. Al termine, scegliere **Fine**, che mostra i valori dei parametri di output.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generare e caricare file HIDX

Quando sei pronto, genera il file HIDX in modo da poterlo caricare nel tuo account di integrazione. Lo strumento di progettazione 3270 crea il file HIDX in una nuova sottocartella in cui è stato salvato il file RAP.

1. Sulla barra degli strumenti di 3270 Design Tool scegliere **Genera codice**.

1. Passare alla cartella che contiene il file RAP e aprire la sottocartella creata dallo strumento dopo la generazione del file HIDX. Verificare che lo strumento ha creato il file HIDX.

1. Accedere al [portale](https://portal.azure.com)di Azure e trovare l'account di integrazione.

1. Aggiungere il file HIDX come mappa all'account di integrazione [seguendo questi passaggi simili per l'aggiunta](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)di mappe , ma quando si seleziona il tipo di mappa, selezionare **HIDX**.

Più avanti in questo argomento, quando si aggiunge un'azione IBM 3270 all'app per la logica per la prima volta, viene richiesto di creare una connessione tra l'app per la logica e il server host fornendo informazioni di connessione, ad esempio i nomi per l'account di integrazione e il server host . Dopo aver creato la connessione, è possibile selezionare il file HIDX aggiunto in precedenza, il metodo da eseguire e i parametri da utilizzare.

Al termine di tutti questi passaggi, è possibile usare l'azione creata nell'app per la logica per la connessione al mainframe IBM, le schermate di unità per l'app, immettere dati, restituire risultati e così via. Puoi anche continuare ad aggiungere altre azioni all'app per la logica per l'integrazione con altre app, servizi e sistemi.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Eseguire le azioni IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nell'ultimo passaggio in cui si vuole aggiungere un'azione scegliere **Nuovo passaggio**e selezionare **Aggiungi un'azione**. 

1. Nella casella di ricerca scegliere **Enterprise**. Nella casella di ricerca immettere "3270" come filtro. Dall'elenco delle azioni, selezionare questa azione: **esegue un programma mainframe su una connessione TN3270**

   ![Selezionare l'azione 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno**+** più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Se non esiste ancora alcuna connessione, fornire le informazioni necessarie per la connessione e scegliere **Crea**.

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome connessione** | Sì | <*nome della connessione*> | Nome per la connessione |
   | **ID account di integrazione** | Sì | <*integration-account-name*> | Nome dell'account di integrazione |
   | **URL della chiave di base dell'account di integrazione** | Sì | <*integration-account-SAS-URL*> | URL della firma di accesso condiviso (SAS) dell'account di integrazione, che è possibile generare dalle impostazioni dell'account di integrazione nel portale di Azure.Your integration account's Shared Access Signature (SAS) URL, which you can generate from your integration account's settings in the Azure portal. <p>1. Nel menu dell'account di integrazione, in **Impostazioni,** selezionare **URL di richiamata**. <br>2. Nel riquadro di destra, copiare il valore **URL di callback generato.** |
   | **Server** | Sì | <*TN3270-nome-server*> | Il nome del server per il servizio TN3270 |
   | **Porta** | No | <*Porta server TN3270*> | La porta utilizzata dal server TN3270. Se lasciato vuoto, `23` il connettore utilizza come valore predefinito. |
   | **Tipo di dispositivo** | No | <*Modello IBM-terminal*> | Il nome o il numero del modello o del numero del terminale IBM da emulare. Se lasciato vuoto, il connettore utilizza i valori predefiniti. |
   | **Tabella codici** | No | <*codice-page-numero*> | Numero della tabella codici per l'host. Se lasciato vuoto, `37` il connettore utilizza come valore predefinito. |
   | **Nome unità logica** | No | <*logical-unit-name*> | Il nome dell'unità logica specifica da richiedere dall'host |
   | **Abilitare SSL?** | No | Accese o spegnimento | Attivare o disattivare la crittografia TLS. |
   | **Convalidare il certificato ssl dell'host?** | No | Accese o spegnimento | Attivare o disattivare la convalida per il certificato del server. |
   ||||

   Ad esempio:

   ![Proprietà di connessione](./media/connectors-create-api-3270/connection-properties.png)

1. Fornire le informazioni necessarie per l'azione:

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome Hidx** | Sì | <*HIDX-nome-file*> | Selezionare il file HIDX 3270 che si desidera utilizzare. |
   | **Nome metodo** | Sì | <*nome-metodo*> | Selezionare il metodo nel file HIDX che si desidera utilizzare. Dopo aver selezionato un metodo, viene visualizzato l'elenco **Aggiungi nuovo parametro** in modo da poter selezionare i parametri da utilizzare con tale metodo. |
   ||||

   Ad esempio:

   **Selezionare il file HIDX**

   ![Selezionare il file HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selezionare il metodo**

   ![Select (metodo)](./media/connectors-create-api-3270/select-method.png)

   **Selezionare i parametri**

   ![Selezionare i parametri](./media/connectors-create-api-3270/add-parameters.png)

1. Al termine, salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, vengono visualizzati i passaggi dell'esecuzione. 
   I passaggi riusciti mostrano i segni di spunta, mentre i passaggi non riusciti mostrano la lettera "X".

1. Per esaminare gli input e gli output per ogni passaggio, espandere tale passaggio.

1. Per esaminare gli output, scegliere **Visualizza output non elaborati**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/si3270/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
