---
title: Connettersi ad applicazioni 3270 su mainframe IBM con Azure - App per la logica di Azure
description: Integra e automatizza le app basate su schermo 3270 con Azure tramite il connettore App per la logica di Azure e IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: ebf858ba86758b11ee896d745d70bdf2f0d0cde6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878381"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrare le app basate su schermo 3270 su mainframe IBM in Azure usando connettore di App per la logica di Azure e IBM 3270

> [!NOTE]
> Questo connettore è in [ *versione di anteprima pubblica*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Con App per la logica di Azure e il connettore IBM 3270, è possibile accedere ed eseguire applicazioni mainframe IBM che generalmente l'unità navigando tra le schermate dell'emulatore 3270. In questo modo, è possibile integrare le tue App mainframe IBM con Azure, Microsoft e altre App, servizi e sistemi tramite la creazione di flussi di lavoro automatizzati con App per la logica di Azure. Il connettore comunica con mainframe IBM e mediante il protocollo TN3270 ed è disponibile in tutte le aree di App per la logica di Azure ad eccezione di Azure per enti pubblici e Azure Cina 21Vianet. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

Questo articolo illustra questi aspetti per l'uso del connettore 3270: 

* Perché usare il connettore IBM 3270 in App per la logica di Azure e come il connettore esegue App basate su schermo 3270

* I prerequisiti e installazione per l'uso del connettore 3270

* La procedura per aggiungere 3270 azioni del connettore per app per la logica

## <a name="why-use-this-connector"></a>Perché usare questo connettore?

Per accedere alle App su mainframe IBM, è in genere necessario usare un emulatore terminal 3270, spesso denominato "schermata verde". Questo metodo è un modo collaudato ma ha limitazioni. Anche se potrebbero non essere possibile Host Integration Server (HIS) consente di lavorare direttamente con queste App, in alcuni casi, separare la logica di business e sullo schermo. In alternativa, forse non sono presenti le informazioni per il funzionano di applicazioni host.

Per estendere questi scenari, il connettore IBM 3270 nelle App per la logica di Azure funziona con lo strumento di progettazione 3270, utilizzabile per record, o "acquisire", le schermate di host usate per un'attività specifica, definire il flusso di navigazione per tale attività tramite l'app mainframe e definire la metodi con parametri di input e output per tale attività. Lo strumento di progettazione consente di convertire tali informazioni nei metadati che il connettore 3270 Usa quando si chiama un'azione che rappresenta tale attività dall'app per la logica.

Dopo aver generato il file di metadati dallo strumento di progettazione, si aggiunge tale file a un account di integrazione in Azure. In questo modo, l'app per la logica può accedere ai metadati dell'app quando si aggiunge un'azione connettore 3270. Il connettore legge il file di metadati dall'account di integrazione, gestisce lo spostamento tra le diverse 3270 schermate e illustra in modo dinamico i parametri per l'azione del connettore 3270. È quindi possibile fornire i dati all'applicazione host e il connettore restituisce i risultati per l'app per la logica. In questo modo, è possibile integrare le app legacy con Azure, Microsoft e altre App, servizi e sistemi che supporta le App per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Consigliato: Un [ambiente del servizio di integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  È possibile selezionare questo ambiente come percorso di creazione ed esecuzione di app per la logica. Un'istanza di ISE consente l'accesso da app per la logica alle risorse protette all'interno di reti virtuali di Azure.

* L'app per la logica da usare per l'automazione e in esecuzione l'app basata su schermo 3270

  Il connettore IBM 3270 non dispone di trigger, pertanto usare un altro trigger per avviare l'app per la logica, ad esempio la **ricorrenza** trigger. È quindi possibile aggiungere 3270 azioni del connettore. Per iniziare, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se si usa un'istanza di ISE, selezionare tale ISE come percorso dell'app per la logica.

* [Scaricare e installare lo strumento di progettazione 3270](https://aka.ms/3270-design-tool-download).
È il solo prerequisito [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  In questo modo, lo strumento si registrano le schermate, i percorsi di navigazione, metodi e parametri per le attività nell'app che aggiungono e runas 3270 azioni del connettore. Lo strumento genera un file di Host Integration Designer XML HIDX () che fornisce i metadati necessari per il connettore da utilizzare per ottimizzare l'app mainframe.
  
  Dopo aver scaricato e installato questo strumento, seguire questi passaggi per la connessione all'host:

  1. Aprire lo strumento di progettazione 3270. Dal **sessione** dal menu **Host sessioni**.
  
  1. L'host TN3270 vengono fornite informazioni server.

* Un' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), ovvero la posizione in cui è archiviato il file HIDX come una mappa in modo che l'app per la logica possa accedere le definizioni dei metadati e il metodo in tale file. 

  Assicurarsi che l'account di integrazione è collegato all'app per la logica in uso. Inoltre, se si usa un'istanza di ISE, assicurarsi che località dell'account di integrazione è la stessa ISE che usa l'app per la logica.

* Accesso al server che ospita l'app mainframe TN3270

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Creare una panoramica dei metadati

In un'app basata su schermo 3270, le schermate e i campi dati sono univoci per gli scenari, in modo che il connettore 3270 necessita di queste informazioni sull'app, che è possibile specificare come metadati. Questi metadati sono descritte informazioni che aiuta la tua app per la logica individuare e riconoscere le schermate, descrive come spostarsi tra le schermate, la posizione in cui i dati di input e dove aspettarsi risultati. Per specificare e generare i metadati, è utilizzare lo strumento di progettazione 3270, che illustra questi specifici *modalità*, o alle fasi, come descritto più avanti in modo più dettagliato:

* **Acquisire**: In questa modalità, si registrano le schermate necessari per completare un'attività specifica con l'app di mainframe, ad esempio, ottenere un saldo bancario.

* **Navigazione**: In questa modalità è specificare il piano o il percorso come spostarsi tra le schermate dell'app mainframe per l'attività specifica.

* **Metodi**: In questa modalità, ad esempio, si definisce il metodo, `GetBalance`, che descrive il percorso di navigazione della schermata. È anche possibile scegliere i campi di ogni schermata che diventano l'input del metodo e i parametri di output.

### <a name="unsupported-elements"></a>Elementi non supportati

Lo strumento di progettazione non supporta questi elementi:

* Esegue il mapping parziale IBM base Mapping supporto BMS (): Se si importa una mappa BMS, lo strumento di progettazione ignora le definizioni parziali dello schermo.
* Ingresso/uscita parametri: Non è possibile definire parametri In/Out.
* Elaborazione di menu: Non è supportato durante l'anteprima
* Elaborazione di matrice: Non è supportato durante l'anteprima

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Acquisisci schermate

In questa modalità, si contrassegna un elemento in ogni schermata 3270 che identifica in modo univoco tale schermata. Ad esempio, è possibile specificare una riga di testo o un set più complesso di condizioni, ad esempio il testo specificato e un campo non vuoto. È possibile registrare queste schermate vengono visualizzate tramite una connessione in tempo reale al server host o importare queste informazioni da una mappa di IBM base Mapping supporto BMS (). La connessione in tempo reale Usa un emulatore TN3270 per la connessione all'host. Ogni azione del connettore è necessario eseguire il mapping a una singola attività che inizia con la connessione alla sessione corrente e termina con la disconnessione dalla sessione.

1. Se hai già fatto, aprire lo strumento di progettazione 3270. Sulla barra degli strumenti, scegliere **acquisire** in modo che la modalità di acquisizione.

1. Per avviare la registrazione, premere il tasto F5, o dal **la registrazione** dal menu **Inizia registrazione**. 

1. Dal **sessione** dal menu **Connect**.

1. Nel **acquisire** riquadro, a partire dalla prima schermata nell'app, eseguire l'app per l'attività specifica che la registrazione.

1. Dopo aver completato l'attività, disconnettersi dall'app come di consueto.

1. Dal **sessione** dal menu **Disconnect**.

1. Per arrestare la registrazione, premere MAIUSC + F5 chiavi, o dal **la registrazione** dal menu **Interrompi registrazione**.

   Dopo aver acquisito le schermate per un'attività, lo strumento di progettazione Mostra le anteprime che rappresentano tali schermate. Alcune note sulle anteprime:

   * Incluso con schermate acquisite, hai una schermata denominata "Empty".

     Alla prima connessione al [CICS](https://www.ibm.com/it-infrastructure/z/cics), è necessario inviare la chiave "Cancella" prima di immettere il nome per la transazione da eseguire. La schermata in cui si invia la chiave "Cancella" non ne ha *attributi di riconoscimento*, ad esempio un titolo della schermata, che è possibile aggiungere tramite l'editor di riconoscimento dello schermo. Per rappresentare questa schermata, le anteprime sono incluse una schermata denominata "Vuoto". In un secondo momento è possibile utilizzare questa schermata per la rappresentazione di schermata in cui è stata immettere il nome della transazione.

   * Per impostazione predefinita, il nome di una schermata acquisita Usa la prima parola sullo schermo. Se tale nome esiste già, lo strumento di progettazione aggiunge il nome con un carattere di sottolineatura e un numero, ad esempio, "WBGB" e "WBGB_1".

1. Per assegnare un nome più significativo a una schermata acquisita, seguire questa procedura:

   1. Nel **schermate Host** riquadro, selezionare la schermata che si desidera rinominare.

   1. Nel riquadro stesso parte inferiore della pagina nello stesso riquadro, trovare il **nome della schermata** proprietà.

   1. Modificare il nome della schermata corrente a un nome più descrittivo.

1. Specificare ora i campi per l'identificazione di ogni schermata.

   Con il flusso di 3270 dati, schermate non sono identificatori predefiniti, pertanto è necessario selezionare il testo univoco in ogni schermata. Per scenari complessi, è possibile specificare più condizioni, ad esempio, testo univoco e un campo con una condizione specifica.

Dopo aver selezionato i campi di riconoscimento, passare alla modalità successiva.

### <a name="conditions-for-identifying-repeated-screens"></a>Condizioni per l'identificazione di schermate ripetute

Per il connettore per accedere e a distinguere tra le schermate, si trova in genere testo univoco in una schermata che è possibile utilizzare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbe essere necessario ulteriori metodi di identificazione. Ad esempio, si supponga di che avere due schermate che hanno lo stesso aspetto ma una sola schermata restituisce un valore valido, mentre l'altra schermata restituisce un messaggio di errore.

Nello strumento di progettazione, è possibile aggiungere *attributi di riconoscimento*, ad esempio, un titolo della schermata, ad esempio "Ottenere saldo dell'Account", usando l'editor di riconoscimento dello schermo. Se si dispone di un percorso con fork e di entrambi i rami restituiscono la stessa schermata, ma con risultati diversi, è necessario ad altri attributi di riconoscimento. In fase di esecuzione, il connettore Usa questi attributi per determinare il current branch e fork. Di seguito sono indicate le condizioni che è possibile usare:

* Valore specifico: Questo valore corrisponde alla stringa specificata nella posizione specificata.
* NON è un valore specifico: Questo valore non corrisponde alla stringa specificata nella posizione specificata.
* Vuota: Questo campo è vuoto.
* NON è vuoto: Questo campo non è vuoto.

Per altre informazioni, vedere la [piano di navigazione di esempio](#example-plan) più avanti in questo argomento.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definire piani di spostamento

In questa modalità è definire il flusso o i passaggi per lo spostamento tra le schermate dell'app mainframe per l'attività specifica. Ad esempio, in alcuni casi, potrebbe essere più di un percorso che l'app può richiedere in un percorso produce il risultato corretto, mentre l'altro tracciato genera un errore. Per ogni schermata, specificare le pressioni di tasti necessari per lo spostamento alla schermata successiva, ad esempio `CICSPROD <enter>`.

> [!TIP]
> Se si sta automatizzare diverse attività che usano la stessa connessione e disconnessione schermate, lo strumento di progettazione fornisce tipi di piano di connessione e disconnessione speciali. Quando si definiscono questi piani, è possibile aggiungere all'inizio e alla fine del piano di navigazione.

### <a name="guidelines-for-plan-definitions"></a>Linee guida per le definizioni di piano

* Includere tutti gli schermi, iniziando con la connessione e che termina con la disconnessione.

* È possibile creare un piano autonomo o usare i piani di connessione e disconnessione, che consentono di riutilizzare una serie di schermate comuni a tutte le transazioni.

  * L'ultima schermata del piano di connessione deve essere la stessa schermata come prima schermata nel piano di navigazione.

  * La prima schermata nel piano di disconnessione deve essere l'ultima schermata stessa schermata nel piano di navigazione.

* Schermate acquisite potrebbero contenere molte schermate ripetute, quindi, scegliere e usare solo un'istanza di qualsiasi schermate ripetute nel piano di. Di seguito sono riportati alcuni esempi di schermate ripetute:

  * Nella schermata, ad esempio, di accesso di **MSG-10** dello schermo
  * La schermata iniziale per CICS
  * "Chiaro" o **vuoto** dello schermo

<a name="create-plans"></a>

### <a name="create-plans"></a>Creazione di piani

1. Sulla barra degli strumenti dello strumento Progettazione 3270, scegliere **navigazione** in modo che la modalità di navigazione.

1. Per avviare il piano, nella **navigazione** riquadro, scegliere **nuovo piano**.

1. Sotto **scegliere Nuovo nome del piano**, immettere un nome per il piano. Dal **tipo** elencare, selezionare il tipo di piano:

   | Tipo di piano | DESCRIZIONE |
   |-----------|-------------|
   | **Processo** | Per autonoma o integrazione dei piani |
   | **Connettere** | Per i piani di Connect |
   | **Disconnetti** | Per i piani di disconnessione |
   |||

1. Dal **Host schermate** riquadro, trascinare le anteprime acquisite al piano di navigazione della superficie di attacco nel **navigazione** riquadro.

   Per rappresentare la schermata vuota in cui è stata immettere il nome della transazione, utilizzare la schermata "Vuota".

1. Disporre le schermate nell'ordine in cui vengono descritte le attività che si sta definendo.

1. Per definire il percorso del flusso tra le schermate, inclusi i fork e join, sulla barra degli strumenti dello strumento Progettazione, scegliere **Flow**.

1. Scegliere la prima schermata nel flusso. Trascinare e disegnare una connessione alla schermata successiva nel flusso.

1. Per ogni schermata, specificare i valori per il **chiave aiuti** proprietà (attenzione Identifier) e per il **testo fisso** proprietà, che sposta il flusso alla schermata successiva.

   È possibile semplicemente la chiave di aiuto, o AID sia la chiave testo fisso.

Dopo aver completato il piano di navigazione, è possibile [definire i metodi in modalità successiva](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Esempio

In questo esempio, si supponga di che esegue una transazione CICS denominata "WBGB" contenente questi passaggi: 

* Nella prima schermata, immettere un nome e un account.
* Nella seconda schermata, otterrai del saldo del conto.
* Chiudere la schermata "Vuota".
* Disconnettersi da CICS sullo schermo "MSG-10".

Si supponga inoltre che si ripetere questi passaggi, ma vengono immessi i dati non corretti, pertanto è possibile acquisire la schermata che mostra l'errore. Ecco le schermate acquisite:

* MSG-10
* Attività iniziali CICS
* Empty
* WBGB_1 (input)
* WBGB_2 (errore)
* Empty_1
* MSG-10_1

Anche se molte schermate qui ottenere nomi univoci, alcune schermate sono la stessa schermata, ad esempio, "MSG-10" e "Vuoto". Per una schermata ripetuta, usare solo un'istanza di tale schermata nel piano. Ecco alcuni esempi che illustrano come potrebbe apparire un piano autonomo, piano di Connect, Disconnect piano e un piano combinato:

* Piano autonomo

  ![Piano di navigazione autonomo](./media/connectors-create-api-3270/standalone-plan.png)

* Connettere il piano

  ![Connettere il piano](./media/connectors-create-api-3270/connect-plan.png)

* Disconnettere piano

  ![Disconnettere piano](./media/connectors-create-api-3270/disconnect-plan.png)

* Piano combinato

  ![Piano combinato](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Esempio: Identificare le schermate ripetute

Per il connettore esplorare e differenziare le schermate, si trova in genere testo univoco in una schermata che è possibile utilizzare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbe essere necessario ulteriori metodi di identificazione. Il piano di esempio ha un fork in cui è possibile ottenere schermate simili. Una sola schermata restituisce il saldo del conto, mentre l'altra schermata restituisce un messaggio di errore.

Lo strumento di progettazione consente di aggiungere gli attributi di riconoscimento, ad esempio, un titolo della schermata denominata "Ottenere saldo dell'Account", usando il riconoscimento dello schermo dell'editor. Nel caso delle schermate simili, è necessario ad altri attributi. In fase di esecuzione, il connettore Usa questi attributi per determinare il ramo e fork.

* Nel ramo che restituisce un input valido, che viene visualizzata la schermata con il saldo dell'account, è possibile aggiungere un campo contenente una condizione "non vuota".

* Nel ramo che restituisce un errore, è possibile aggiungere un campo contenente una condizione "vuota".

<a name="define-method"></a>

## <a name="define-methods"></a>Definire i metodi

In questa modalità, si definisce un metodo che ha associato il piano di navigazione. Per ogni parametro del metodo, specifica il tipo di dati, ad esempio una stringa, intero, data o ora e così via. Al termine, è possibile testare il metodo nell'host in tempo reale e verificare che il metodo funzioni come previsto. È quindi possibile generare il file di metadati o file di Host Integration Designer XML HIDX (), che ora include le definizioni del metodo da usare per la creazione ed esecuzione di un'azione per il connettore IBM 3270.

1. Sulla barra degli strumenti dello strumento Progettazione 3270, scegliere **metodi** in modo che la modalità di metodi. 

1. Nel **navigazione** riquadro, selezionare la schermata con i campi di input desiderati.

1. Per aggiungere il primo parametro di input per il metodo, seguire questa procedura:

   1. Nel **acquisire** riquadro sullo schermo dell'emulatore 3270, scegliere il campo intero, non solo con il testo all'interno del campo, che desidera come primo input.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completato, nella **View** dal menu **tutti i campi**.

   1. Sulla barra degli strumenti dello strumento Progettazione, scegliere **campo di Input**. 

   Per aggiungere più parametri di input, ripetere i passaggi precedenti per ogni parametro.

1. Per aggiungere il primo parametro di output per il metodo, seguire questa procedura:

   1. Nel **acquisire** riquadro sullo schermo dell'emulatore 3270, scegliere il campo intero, non solo con il testo all'interno del campo, che desidera come il primo output.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completato, nella **View** dal menu **tutti i campi**.

   1. Sulla barra degli strumenti dello strumento Progettazione, scegliere **campo di Output**.

   Per aggiungere ulteriori parametri di output, ripetere i passaggi precedenti per ogni parametro.

1. Dopo aver aggiunto tutti i parametri del metodo, definire queste proprietà per ogni parametro:

   | Nome proprietà | Valori possibili | 
   |---------------|-----------------|
   | **Tipo di dati** | Byte, ora, Decimal, Int, Long, Short, String |
   | **Tecnica di riempimento dei campi** | I parametri supportano questi tipi di riempimento, il riempimento con spazi vuoti se necessario: <p><p>- **Tipo**: Immettere i caratteri in modo sequenziale nel campo. <p>- **Riempimento**: Sostituire il contenuto del campo con caratteri, il riempimento con spazi vuoti se necessario. <p>- **EraseEofType**: Deselezionare il campo e quindi immettere caratteri in modo sequenziale nel campo. |
   | **Stringa di formato** | Alcuni tipi di dati di parametro usano una stringa di formato, che informa il connettore 3270 come convertire il testo dalla schermata in un tipo di dati .NET: <p><p>- **DateTime**: La stringa di formato data/ora segue il [stringhe di formato .NET Data e ora personalizzato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Ad esempio, la data `06/30/2019` Usa la stringa di formato `MM/dd/yyyy`. <p>- **Decimal**: La stringa di formato decimale Usa la [clausola immagine COBOL](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Ad esempio, il numero `100.35` Usa la stringa di formato `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Salvare e visualizzare i metadati

Dopo aver definito il metodo, ma prima di testare il metodo, salvare tutte le informazioni fino ad ora definito in un file di criteri di accesso remoto (.rap).
È possibile salvare questo file di criteri di accesso remoto in qualsiasi momento durante qualsiasi modalità. Lo strumento di progettazione include anche un file di criteri di autorizzazione risorse di esempio che è possibile aprire ed esaminare, passare alla cartella di installazione dello strumento Progettazione in questa posizione e aprire il file "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Tuttavia, se si tenta di salvare le modifiche al file di criteri di autorizzazione risorse di esempio o generando un file HIDX dal file di criteri di autorizzazione risorse di esempio mentre il file rimane nella cartella di installazione dello strumento Progettazione, è possibile ottenere un errore "accesso negato". Per impostazione predefinita, lo strumento di progettazione viene installato nella cartella del file di programma senza autorizzazioni elevate. Se si verifica un errore, provare una delle soluzioni seguenti:

* Copiare il file di esempio in un percorso diverso.
* Eseguire lo strumento di progettazione come amministratore.
* Diventare proprietario per la cartella SDK.

## <a name="test-your-method"></a>Il metodo di test

1. Per eseguire il metodo in base all'host in tempo reale, mentre si è ancora in modalità di metodi, premere il tasto F5, o dalla barra degli strumenti dello strumento Progettazione, scegliere **eseguiti**.

   > [!TIP]
   > È possibile modificare le modalità in qualsiasi momento. Nel **File** dal menu **modalità**e quindi selezionare la modalità desiderata.

1. Immettere i valori dei parametri e scegliere **OK**.

1. Per passare alla schermata successiva, scegli **successivo**.

1. Al termine, scegliere, che mostra i valori dei parametri output.****

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generare e caricare file HIDX

Quando si è pronti, generare il file HIDX in modo che è possibile caricare l'account di integrazione. Lo strumento di progettazione 3270 crea il file HIDX in una nuova sottocartella in cui è stato salvato il file RAP.

1. Sulla barra degli strumenti dello strumento Progettazione 3270, scegliere **genera codice**.

1. Passare alla cartella che contiene il file di criteri di accesso remoto e aprire la sottocartella che lo strumento creato dopo aver generato il file HIDX. Verificare che lo strumento ha creato il file HIDX.

1. Accedi per il [portale di Azure](https://portal.azure.com)e trovare l'account di integrazione.

1. Aggiungere il file HIDX come una mappa all'account di integrazione dal [seguire questa procedura simile per l'aggiunta di mappe](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ma quando si seleziona il tipo di mappa, selezionare **HIDX**.

Più avanti in questo argomento, quando si aggiunge un'azione 3270 di IBM per app per la logica per la prima volta, viene chiesto di creare una connessione tra l'app per la logica e il server host, fornendo informazioni di connessione, ad esempio i nomi per il server host e account di integrazione . Dopo aver creato la connessione, è possibile selezionare il file HIDX aggiunto in precedenza, il metodo da eseguire e i parametri da utilizzare.

Al termine tutti questi passaggi, è possibile usare l'azione che crea nell'app per la logica per la connessione a del mainframe IBM, le schermate di unità per l'app, immettere i dati, restituire i risultati e così via. È anche possibile continuare ad aggiungere altre azioni per l'app per la logica per l'integrazione con altre App, servizi e sistemi.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Eseguire le azioni di IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Sotto l'ultimo passaggio in cui si desidera aggiungere un'azione, scegliere **nuovo passaggio**e selezionare **Aggiungi un'azione**. 

1. Nella casella di ricerca, scegliere **Enterprise**. Nella casella di ricerca immettere "3270" come filtro. Nell'elenco di azioni selezionare questa azione: **Esegue un programma mainframe tramite una connessione TN3270**

   ![Selezionare l'azione 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Se nessuna connessione esiste ancora, specificare le informazioni necessarie per la connessione e scegliere **Create**.

   | Proprietà | Obbligatorio | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Connection Name** (Nome connessione) | Sì | <*nome connessione*> | Nome per la connessione |
   | **ID Account di integrazione** | Sì | <*integration-account-name*> | Nome dell'account di integrazione |
   | **URL firma di accesso condiviso dell'Account di integrazione** | Sì | <*integration-account-SAS-URL*> | URL di firma di accesso condiviso (SAS) dell'account di integrazione, che è possibile generare dalle impostazioni dell'account di integrazione nel portale di Azure. <p>1. L'integrazione di dal menu dell'account in **le impostazioni**, selezionare **URL di Callback**. <br>2. Nel riquadro di destra, copiare il **URL di Callback generato** valore. |
   | **Server** | Sì | <*TN3270-server-name*> | Il nome del server per il servizio di TN3270 |
   | **Porta** | No  | <*TN3270-server-port*> | La porta usata dal server TN3270. Se lasciato vuoto, il connettore Usa `23` come valore predefinito. |
   | **Tipo di dispositivo** | No  | <*Modello di terminale IBM*> | Il nome del modello o il numero del terminale di IBM per emulare. Se lasciato vuoto, il connettore Usa valori predefiniti. |
   | **Tabella codici** | No  | <*code-page-number*> | Il numero di pagina di codice per l'host. Se lasciato vuoto, il connettore Usa `37` come valore predefinito. |
   | **Nome dell'unità logica** | No  | <*logical-unit-name*> | Il nome di unità logica specifica per richiedere dall'host |
   | **Abilitare SSL?** | No  | Attiva o disattiva | Attivare o disattivare la crittografia SSL. |
   | **Convalida del certificato ssl host?** | No  | Attiva o disattiva | Attivare o disattivare la convalida per il certificato del server. |
   ||||

   Ad esempio: 

   ![Connection properties (Proprietà connessione)](./media/connectors-create-api-3270/connection-properties.png)

1. Fornire le informazioni necessarie per l'azione:

   | Proprietà | Obbligatorio | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Nome Hidx** | Sì | <*HIDX-file-name*> | Selezionare il file HIDX 3270 che si desidera utilizzare. |
   | **Nome del metodo** | Sì | <*nome del metodo*> | Selezionare il metodo nel file HIDX che si desidera utilizzare. Dopo aver selezionato un metodo, il **Aggiungi nuovo parametro** verrà visualizzato l'elenco è possibile scegliere i parametri da usare con tale metodo. |
   ||||

   Ad esempio: 

   **Selezionare il file HIDX**

   ![Selezionare il file HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selezionare il metodo**

   ![Seleziona metodo](./media/connectors-create-api-3270/select-method.png)

   **Selezionare i parametri**

   ![Selezionare i parametri](./media/connectors-create-api-3270/add-parameters.png)

1. Al termine, salvare ed eseguire l'app per la logica.

   Dopo la logica di completamento dell'esecuzione, i passaggi di esecuzione vengono visualizzati. 
   Questa procedura ha esito positivo Mostra segni di spunta, mentre questa procedura Visualizza la lettera "X".

1. Per esaminare gli input e output per ogni passaggio, espandere tale passaggio.

1. Per esaminare gli output, scegli **visualizzare l'output non elaborati**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche dettagliate sui trigger, azioni e limite, vengono descritti dalla definizione OpenAPI del connettore (in precedenza Swagger) descrizione, esaminare la pagina di riferimento del connettore: [https://docs.microsoft.com/connectors/<replace-with-api-topic-file-name>](/connectors/).

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
