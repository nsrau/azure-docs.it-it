---
title: Connettersi a app 3270 in mainframe IBM-app per la logica di Azure
description: Integra e automatizza le app 3270 basate su schermo con Azure usando app per la logica di Azure e il connettore IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: f039d0cbfa2b34fecbcdee53ebe2b56b6e9b6d69
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787568"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrare app basate su schermo 3270 in mainframe IBM con Azure usando app per la logica di Azure e il connettore IBM 3270

> [!NOTE]
> Questo connettore è in [*anteprima pubblica*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Con le app per la logica di Azure e il connettore IBM 3270, è possibile accedere ed eseguire app IBM mainframe che in genere si spostano attraverso le schermate dell'emulatore 3270. In questo modo, è possibile integrare le app IBM mainframe con Azure, Microsoft e altre app, servizi e sistemi creando flussi di lavoro automatizzati con app per la logica di Azure. Il connettore comunica con i mainframe IBM usando il protocollo TN3270 ed è disponibile in tutte le aree di app per la logica di Azure ad eccezione di Azure per enti pubblici e Azure China 21Vianet. Se non si ha familiarità con App per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

Questo articolo descrive gli aspetti relativi all'uso del connettore 3270: 

* Perché usare il connettore IBM 3270 in app per la logica di Azure e il modo in cui il connettore esegue 3270 app basate sullo schermo

* Prerequisiti e configurazione per l'uso del connettore 3270

* Passaggi per l'aggiunta di azioni del connettore 3270 all'app per la logica

## <a name="why-use-this-connector"></a>Perché usare questo connettore?

Per accedere alle app sui mainframe IBM, in genere si usa un emulatore di terminale 3270, spesso denominato "schermo verde". Questo metodo è un modo testato a tempo, ma presenta limitazioni. Sebbene Host Integration Server (HIS) consenta di lavorare direttamente con queste app, a volte è possibile che la separazione dello schermo e della logica di business non sia possibile. Oppure, forse, non sono più disponibili informazioni sul funzionamento delle applicazioni host.

Per estendere questi scenari, il connettore IBM 3270 in app per la logica di Azure funziona con lo strumento di progettazione 3270, che è possibile usare per registrare, o "acquisire", le schermate host usate per un'attività specifica, definire il flusso di navigazione per tale attività attraverso l'app mainframe e definire il metodi con parametri di input e output per l'attività. Lo strumento di progettazione converte tali informazioni in metadati usati dal connettore 3270 quando viene chiamata un'azione che rappresenta tale attività dall'app per la logica.

Dopo aver generato il file di metadati dallo strumento di progettazione, è necessario aggiungerlo a un account di integrazione in Azure. In questo modo, l'app per la logica può accedere ai metadati dell'app quando si aggiunge un'azione 3270 Connector. Il connettore legge il file di metadati dall'account di integrazione, gestisce la navigazione attraverso le schermate 3270 e visualizza dinamicamente i parametri per l'azione del connettore 3270. È quindi possibile fornire dati all'applicazione host e il connettore restituisce i risultati all'app per la logica. In questo modo, è possibile integrare le app legacy con Azure, Microsoft e altre app, servizi e sistemi supportati da app per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Consigliato: un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  È possibile selezionare questo ambiente come percorso per la creazione e l'esecuzione dell'app per la logica. Un ISE fornisce l'accesso dall'app per la logica alle risorse protette nelle reti virtuali di Azure.

* App per la logica da usare per l'automazione e l'esecuzione di un'app 3270 basata sullo schermo

  Il connettore IBM 3270 non contiene trigger, quindi usare un altro trigger per avviare l'app per la logica, ad esempio il trigger di **ricorrenza** . È quindi possibile aggiungere azioni del connettore 3270. Per iniziare, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se si usa ISE, selezionare ISE come posizione dell'app per la logica.

* [Scaricare e installare lo strumento di progettazione 3270](https://aka.ms/3270-design-tool-download).
L'unico prerequisito è [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Questo strumento consente di registrare le schermate, i percorsi di navigazione, i metodi e i parametri per le attività nell'app che vengono aggiunte ed eseguite come azioni del connettore 3270. Lo strumento genera un file XML HIDX (Host Integration Designer) che fornisce i metadati necessari per il connettore da usare per la guida dell'app mainframe.
  
  Dopo aver scaricato e installato questo strumento, attenersi alla seguente procedura per la connessione all'host:

  1. Aprire lo strumento di progettazione 3270. Scegliere **sessioni host**dal menu **sessione** .
  
  1. Fornire le informazioni sul server host TN3270.

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), ovvero la posizione in cui si archivia il file hidx come mappa, in modo che l'app per la logica possa accedere ai metadati e alle definizioni dei metodi in tale file. 

  Verificare che l'account di integrazione sia collegato all'app per la logica in uso. Inoltre, se si usa ISE, verificare che il percorso dell'account di integrazione sia lo stesso ISE usato dall'app per la logica.

* Accesso al server TN3270 che ospita l'app mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Cenni preliminari sulla creazione dei metadati

In un'app basata su schermo 3270, le schermate e i campi dati sono univoci per gli scenari, quindi il connettore 3270 richiede queste informazioni sull'app, che è possibile fornire come metadati. Questi metadati descrivono le informazioni che consentono all'app per la logica di identificare e riconoscere le schermate, descrive come spostarsi tra le schermate, dove inserire i dati e dove prevedere i risultati. Per specificare e generare questi metadati, è possibile usare lo strumento di progettazione 3270, che illustra in modo dettagliato le *modalità*specifiche, o le fasi, come descritto più avanti in dettaglio:

* **Acquisisci**: in questa modalità vengono registrate le schermate necessarie per il completamento di un'attività specifica con l'app mainframe, ad esempio per ottenere un saldo bancario.

* **Navigazione**: in questa modalità è possibile specificare il piano o il percorso per spostarsi tra le schermate dell'app mainframe per l'attività specifica.

* **Metodi**: in questa modalità viene definito il metodo, ad esempio `GetBalance`, che descrive il percorso di navigazione della schermata. Si scelgono anche i campi in ogni schermata che diventano i parametri di input e output del metodo.

### <a name="unsupported-elements"></a>Elementi non supportati

Lo strumento di progettazione non supporta questi elementi:

* Mappe del supporto per il mapping di base di IBM (BMS) parziale: se si importa una mappa di BMS, lo strumento di progettazione ignorerà le definizioni delle schermate parziali.
* Parametri in/out: non è possibile definire parametri in/out.
* Elaborazione menu: non supportata durante l'anteprima
* Elaborazione di matrici: non supportata durante l'anteprima

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Acquisisci schermate

In questa modalità si contrassegna un elemento in ogni schermata 3270 che identifica in modo univoco la schermata. Ad esempio, è possibile specificare una riga di testo o un set più complesso di condizioni, ad esempio testo specifico e un campo non vuoto. È possibile registrare queste schermate su una connessione in tempo reale al server host oppure importare queste informazioni da una mappa di supporto per il mapping di IBM Basic (BMS). La connessione dinamica usa un emulatore di TN3270 per la connessione all'host. Ogni azione del connettore deve essere mappata a una singola attività che inizia con la connessione alla sessione e termina con la disconnessione dalla sessione.

1. Se non è già stato fatto, aprire lo strumento di progettazione 3270. Sulla barra degli strumenti scegliere **Acquisisci** in modo da attivare la modalità di acquisizione.

1. Per avviare la registrazione, premere il tasto F5 oppure scegliere **Avvia registrazione**dal menu **registrazione** . 

1. Scegliere **Connetti**dal menu **sessione** .

1. Nel riquadro **acquisizione** , a partire dalla prima schermata dell'app, eseguire un'istruzione alla volta nell'app per l'attività specifica che si sta registrando.

1. Al termine dell'attività, disconnettersi dall'app come in genere.

1. Scegliere **Disconnetti**dal menu **sessione** .

1. Per arrestare la registrazione, premere i tasti MAIUSC + F5 oppure scegliere **Interrompi registrazione**dal menu **registrazione** .

   Dopo aver acquisito le schermate per un'attività, lo strumento di progettazione Mostra le anteprime che rappresentano tali schermate. Di seguito sono riportate alcune note sulle anteprime:

   * Con le schermate acquisite, è presente una schermata denominata "Empty".

     Quando si esegue la prima connessione a [CICS](https://www.ibm.com/it-infrastructure/z/cics), è necessario inviare la chiave "Clear" prima di poter immettere il nome della transazione che si desidera eseguire. La schermata in cui si invia la chiave "Clear" non contiene *attributi di riconoscimento*, ad esempio un titolo della schermata, che è possibile aggiungere mediante l'editor di riconoscimento dello schermo. Per rappresentare questa schermata, le anteprime includono una schermata denominata "Empty". In seguito sarà possibile utilizzare questa schermata per rappresentare la schermata in cui si immette il nome della transazione.

   * Per impostazione predefinita, il nome di una schermata acquisita usa la prima parola sullo schermo. Se tale nome esiste già, lo strumento di progettazione aggiunge il nome con un carattere di sottolineatura e un numero, ad esempio, "WBGB" e "WBGB_1".

1. Per assegnare un nome più significativo a una schermata acquisita, attenersi alla seguente procedura:

   1. Nel riquadro **schermate host** selezionare la schermata che si desidera rinominare.

   1. Nello stesso riquadro, in prossimità della parte inferiore dello stesso riquadro, trovare la proprietà **Nome schermata** .

   1. Modificare il nome della schermata corrente con un nome più descrittivo.

1. Specificare ora i campi per l'identificazione di ogni schermata.

   Con il flusso di dati 3270, le schermate non hanno identificatori predefiniti, quindi è necessario selezionare testo univoco in ogni schermata. Per gli scenari complessi è possibile specificare più condizioni, ad esempio testo univoco e un campo con una condizione specifica.

Al termine della selezione dei campi di riconoscimento, passare alla modalità successiva.

### <a name="conditions-for-identifying-repeated-screens"></a>Condizioni per l'identificazione di schermate ripetute

Per spostarsi tra le schermate e distinguere tra le schermate, in genere si trova un testo univoco in una schermata che è possibile usare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbero essere necessari altri metodi di identificazione. Si supponga, ad esempio, di avere due schermate che hanno un aspetto identico, ad eccezione del fatto che una schermata restituisce un valore valido, mentre l'altra schermata restituisce un messaggio di errore.

Nello strumento di progettazione è possibile aggiungere *attributi di riconoscimento*, ad esempio un titolo di schermata, ad esempio "Get account Balance", mediante l'editor di riconoscimento dello schermo. Se si dispone di un percorso con fork ed entrambi i rami restituiscono la stessa schermata ma con risultati diversi, sono necessari altri attributi di riconoscimento. In fase di esecuzione, il connettore usa questi attributi per determinare il ramo corrente e il fork. Di seguito sono riportate le condizioni che è possibile usare:

* Valore specifico: questo valore corrisponde alla stringa specificata in corrispondenza della posizione specificata.
* NON è un valore specifico: questo valore non corrisponde alla stringa specificata nella posizione specificata.
* Empty: questo campo è vuoto.
* NON vuoto: questo campo non è vuoto.

Per ulteriori informazioni, vedere il [piano di navigazione di esempio](#example-plan) più avanti in questo argomento.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definire i piani di navigazione

In questa modalità è possibile definire il flusso o i passaggi per spostarsi tra le schermate dell'app mainframe per l'attività specifica. In alcuni casi, ad esempio, si potrebbe avere più di un percorso che l'app può eseguire dove un percorso produce il risultato corretto, mentre l'altro percorso genera un errore. Per ogni schermata, specificare le sequenze di tasti necessarie per il passaggio alla schermata successiva, ad esempio `CICSPROD <enter>`.

> [!TIP]
> Se si automatizzano diverse attività che usano le stesse schermate di connessione e disconnessione, lo strumento di progettazione fornisce tipi speciali di piani di connessione e disconnessione. Quando si definiscono questi piani, è possibile aggiungerli all'inizio e alla fine del piano di navigazione.

### <a name="guidelines-for-plan-definitions"></a>Linee guida per le definizioni dei piani

* Includere tutte le schermate, a partire dalla connessione e terminando con la disconnessione.

* È possibile creare un piano autonomo o usare i piani di connessione e disconnessione, che consentono di riutilizzare una serie di schermate comuni a tutte le transazioni.

  * L'ultima schermata del piano Connect deve essere identica a quella della prima schermata nel piano di navigazione.

  * La prima schermata del piano di disconnessione deve essere identica a quella dell'ultima schermata del piano di navigazione.

* Le schermate acquisite potrebbero contenere molte schermate ripetute, quindi selezionare e usare una sola istanza di tutte le schermate ripetute del piano. Di seguito sono riportati alcuni esempi di schermate ripetute:

  * Schermata di accesso, ad esempio, la schermata **MSG-10**
  * Schermata iniziale per CICS
  * Schermata "Cancella" o **vuota**

<a name="create-plans"></a>

### <a name="create-plans"></a>Creare piani

1. Sulla barra degli strumenti dello strumento di progettazione 3270 scegliere **navigazione** per attivare la modalità di navigazione.

1. Per avviare il piano, scegliere **nuovo piano**nel riquadro di **spostamento** .

1. In **Scegli nuovo piano nome**immettere un nome per il piano. Nell'elenco **tipo** selezionare il tipo di piano:

   | Tipo di piano | Description |
   |-----------|-------------|
   | **Processo** | Per piani autonomi o combinati |
   | **Connettere** | Per i piani di connessione |
   | **Disconnettere** | Per i piani di disconnessione |
   |||

1. Dal riquadro **schermate host** trascinare le anteprime acquisite nella superficie del piano di spostamento nel riquadro di **spostamento** .

   Per rappresentare la schermata vuota in cui si immette il nome della transazione, utilizzare la schermata "Empty".

1. Disporre le schermate nell'ordine in cui viene descritta l'attività che si sta definendo.

1. Per definire il percorso del flusso tra le schermate, inclusi i fork e i join, sulla barra degli strumenti dello strumento di progettazione scegliere **flusso**.

1. Scegliere la prima schermata nel flusso. Trascinare e creare una connessione alla schermata successiva nel flusso.

1. Per ogni schermata, fornire i valori per la proprietà **chiave di supporto** (ID attenzione) e per la proprietà **testo fisso** , che sposta il flusso alla schermata successiva.

   È possibile che si disponga solo della chiave di supporto o della chiave del supporto e del testo fisso.

Al termine del piano di navigazione, è possibile [definire i metodi nella modalità successiva](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Esempio

In questo esempio si supponga di eseguire una transazione CICS denominata "WBGB" con i passaggi seguenti: 

* Nella prima schermata immettere un nome e un account.
* Nella seconda schermata si ottiene il saldo del conto.
* Uscire dalla schermata "vuota".
* È possibile disconnettersi da CICS alla schermata "MSG-10".

Si supponga inoltre di ripetere questi passaggi, ma si immettono dati non corretti per poter acquisire la schermata che mostra l'errore. Ecco le schermate acquisite:

* MESSAGGIO-10
* Pagina iniziale di CICS
* Empty
* WBGB_1 (input)
* WBGB_2 (errore)
* Empty_1
* MESSAGGIO-10_1

Sebbene molte schermate ottengano nomi univoci, alcune schermate sono la stessa schermata, ad esempio "MSG-10" e "Empty". Per una schermata ripetuta, utilizzare solo un'istanza per la schermata del piano. Di seguito sono riportati alcuni esempi che illustrano come un piano autonomo, un piano di connessione, un piano di disconnessione e un piano combinato potrebbero essere:

* Piano autonomo

  ![Piano di navigazione autonomo](./media/connectors-create-api-3270/standalone-plan.png)

* Piano di connessione

  ![Piano di connessione](./media/connectors-create-api-3270/connect-plan.png)

* Disconnetti piano

  ![Disconnetti piano](./media/connectors-create-api-3270/disconnect-plan.png)

* Piano combinato

  ![Piano combinato](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Esempio: identificare le schermate ripetute

Poiché il connettore consente di spostarsi e distinguere le schermate, in genere si trova un testo univoco in una schermata che è possibile usare come identificatore tra le schermate acquisite. Per le schermate ripetute, potrebbero essere necessari altri metodi di identificazione. Il piano di esempio include una forchetta in cui è possibile ottenere schermate simili. Una schermata restituisce un saldo del conto, mentre l'altra schermata restituisce un messaggio di errore.

Lo strumento di progettazione consente di aggiungere attributi di riconoscimento, ad esempio un titolo di schermata denominato "Get account Balance", mediante l'editor di riconoscimento dello schermo. Nel caso di schermate simili, sono necessari altri attributi. In fase di esecuzione, il connettore usa questi attributi per determinare il ramo e il fork.

* Nel ramo che restituisce un input valido, ovvero la schermata con il saldo del conto, è possibile aggiungere un campo con una condizione "non vuota".

* Nel ramo restituito con un errore è possibile aggiungere un campo con una condizione "Empty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definire i metodi

In questa modalità si definisce un metodo associato al piano di navigazione. Per ogni parametro di metodo è possibile specificare il tipo di dati, ad esempio una stringa, un numero intero, una data o un'ora e così via. Al termine, è possibile testare il metodo nell'host Live e verificare che il metodo funzioni come previsto. Viene quindi generato il file di metadati o il file XML HIDX (Host Integration Designer), che ora include le definizioni dei metodi da usare per la creazione e l'esecuzione di un'azione per il connettore IBM 3270.

1. Sulla barra degli strumenti dello strumento di progettazione 3270 scegliere **Metodi** per immettere la modalità dei metodi. 

1. Nel riquadro di **spostamento** selezionare la schermata con i campi di input desiderati.

1. Per aggiungere il primo parametro di input per il metodo, attenersi alla procedura seguente:

   1. Nella schermata dell'emulatore 3270 del riquadro **acquisizione** scegliere il campo intero, non solo il testo all'interno del campo, che si vuole come primo input.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completa, scegliere **tutti i campi**dal menu **Visualizza** .

   1. Sulla barra degli strumenti dello strumento di progettazione scegliere **campo di input**. 

   Per aggiungere altri parametri di input, ripetere i passaggi precedenti per ogni parametro.

1. Per aggiungere il primo parametro di output per il metodo, attenersi alla procedura seguente:

   1. Nella schermata dell'emulatore 3270 del riquadro **acquisizione** scegliere il campo intero, non solo il testo all'interno del campo, che si vuole come primo output.

      > [!TIP]
      > Per visualizzare tutti i campi e assicurarsi di selezionare il campo completa, scegliere **tutti i campi**dal menu **Visualizza** .

   1. Sulla barra degli strumenti dello strumento di progettazione scegliere **campo di output**.

   Per aggiungere altri parametri di output, ripetere i passaggi precedenti per ogni parametro.

1. Dopo aver aggiunto tutti i parametri del metodo, definire queste proprietà per ogni parametro:

   | Nome proprietà | Valori possibili | 
   |---------------|-----------------|
   | **Tipo di dati** | Byte, data e ora, Decimal, int, Long, short, String |
   | **Tecnica di riempimento campo** | I parametri supportano questi tipi di riempimento, inserendo spazi vuoti se necessario: <p><p>**tipo**di - : immettere i caratteri in sequenza nel campo. <p>**riempimento**- : sostituire il contenuto del campo con i caratteri, inserendo spazi vuoti se necessario. <p>- **EraseEofType**: deselezionare il campo, quindi immettere i caratteri in sequenza nel campo. |
   | **Stringa di formato** | Alcuni tipi di dati dei parametri usano una stringa di formato, che informa il connettore 3270 come convertire il testo dalla schermata in un tipo di dati .NET: <p><p>- **DateTime**: la stringa di formato DateTime segue le [stringhe di formato di data e ora personalizzate .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Ad esempio, la data `06/30/2019` usa la stringa di formato `MM/dd/yyyy`. <p>- **Decimal**: la stringa di formato decimale usa la [clausola COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Il numero `100.35`, ad esempio, utilizza la stringa di formato `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Salvare e visualizzare i metadati

Dopo aver definito il metodo, ma prima di testare il metodo, salvare tutte le informazioni definite finora in un file RAP (rap).
È possibile salvare in questo file RAP in qualsiasi momento durante qualsiasi modalità. Lo strumento di progettazione include anche un file RAP di esempio che è possibile aprire ed esaminare esplorando la cartella di installazione dello strumento di progettazione in questo percorso e aprendo il file "WoodgroveBank. rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Tuttavia, se si tenta di salvare le modifiche apportate al file RAP di esempio o di generare un file HIDX dal file RAP di esempio mentre il file rimane nella cartella di installazione dello strumento di progettazione, è possibile che venga visualizzato un errore di accesso negato. Per impostazione predefinita, lo strumento di progettazione viene installato nella cartella programmi senza autorizzazioni elevate. Se viene ricevuto un errore, provare una delle soluzioni seguenti:

* Copiare il file di esempio in un percorso diverso.
* Eseguire lo strumento di progettazione come amministratore.
* Creare il proprietario della cartella SDK.

## <a name="test-your-method"></a>Testare il metodo

1. Per eseguire il metodo sull'host Live, mentre ancora in modalità metodi premere il tasto F5 oppure scegliere **Esegui**dalla barra degli strumenti dello strumento di progettazione.

   > [!TIP]
   > È possibile modificare le modalità in qualsiasi momento. Scegliere **modalità**dal menu **file** , quindi selezionare la modalità desiderata.

1. Immettere i valori dei parametri e scegliere **OK**.

1. Per passare alla schermata successiva, fare clic su **Avanti**.

1. Al termine, scegliere **fine**per visualizzare i valori dei parametri di output.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generare e caricare il file HIDX

Quando si è pronti, generare il file HIDX in modo che sia possibile caricarlo nell'account di integrazione. Lo strumento di progettazione 3270 crea il file HIDX in una nuova sottocartella in cui è stato salvato il file con estensione RAP.

1. Sulla barra degli strumenti dello strumento di progettazione 3270 scegliere **genera codice**.

1. Passare alla cartella che contiene il file RAP e aprire la sottocartella creata dallo strumento dopo la generazione del file HIDX. Verificare che lo strumento abbia creato il file HIDX.

1. Accedere al [portale di Azure](https://portal.azure.com)e individuare l'account di integrazione.

1. Aggiungere il file HIDX come mappa al proprio account di integrazione [attenendosi alla procedura seguente per l'aggiunta di mappe](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ma quando si seleziona il tipo di mappa selezionare **hidx**.

Più avanti in questo argomento, quando si aggiunge un'azione IBM 3270 all'app per la logica per la prima volta, viene richiesto di creare una connessione tra l'app per la logica e il server host fornendo le informazioni di connessione, ad esempio i nomi dell'account di integrazione e del server host . Dopo aver creato la connessione, è possibile selezionare il file HIDX aggiunto in precedenza, il metodo da eseguire e i parametri da usare.

Al termine di tutti questi passaggi, è possibile usare l'azione creata nell'app per la logica per la connessione al mainframe IBM, le schermate di unità per l'app, immettere i dati, restituire i risultati e così via. È anche possibile continuare ad aggiungere altre azioni all'app per la logica per l'integrazione con altre app, servizi e sistemi.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Eseguire azioni IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nell'ultimo passaggio in cui si desidera aggiungere un'azione, scegliere **nuovo passaggio**e selezionare **Aggiungi un'azione**. 

1. Nella casella di ricerca scegliere **Enterprise**. Nella casella di ricerca immettere "3270" come filtro. Nell'elenco azioni selezionare questa azione: **esegue un programma mainframe su una connessione TN3270**

   ![Selezionare l'azione 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Se non esiste ancora alcuna connessione, fornire le informazioni necessarie per la connessione e scegliere **Crea**.

   | Proprietà | Obbligatoria | Value | Description |
   |----------|----------|-------|-------------|
   | **Connection Name** (Nome connessione) | SÌ | <*nome connessione*> | Nome per la connessione |
   | **ID account di integrazione** | SÌ | <*integration-account-name*> | Nome dell'account di integrazione |
   | **URL SAS dell'account di integrazione** | SÌ | <*Integration-account-SAS-URL*> | L'URL di firma di accesso condiviso (SAS) dell'account di integrazione, che è possibile generare dalle impostazioni dell'account di integrazione nella portale di Azure. <p>1. nel menu dell'account di integrazione in **Impostazioni**selezionare **URL callback**. <br>2. nel riquadro di destra, copiare il valore di **URL callback generato** . |
   | **Server** | SÌ | <*TN3270-server-name*> | Nome del server per il servizio TN3270 |
   | **Porta** | No | <*TN3270-server-port*> | La porta usata dal server TN3270. Se lasciato vuoto, il connettore USA `23` come valore predefinito. |
   | **Tipo di dispositivo** | No | <> *IBM-Terminal-Model* | Nome o numero del modello per il terminale IBM da emulare. Se viene lasciato vuoto, il connettore usa i valori predefiniti. |
   | **Tabella codici** | No | <*codice-numero di pagina*> | Numero della tabella codici per l'host. Se lasciato vuoto, il connettore USA `37` come valore predefinito. |
   | **Nome unità logica** | No | <*nome-unità-logico*> | Nome dell'unità logica specifica da richiedere dall'host |
   | **Abilitare SSL?** | No | On o off | Attivare o disattivare la crittografia SSL. |
   | **Convalidare il certificato SSL host?** | No | On o off | Attivare o disattivare la convalida per il certificato del server. |
   ||||

   ad esempio:

   ![Connection properties (Proprietà connessione)](./media/connectors-create-api-3270/connection-properties.png)

1. Fornire le informazioni necessarie per l'azione:

   | Proprietà | Obbligatoria | Value | Description |
   |----------|----------|-------|-------------|
   | **Nome hidx** | SÌ | <*hidx-nome file*> | Selezionare il file HIDX 3270 che si vuole usare. |
   | **Nome metodo** | SÌ | *nome-metodo* <> | Selezionare il metodo nel file HIDX che si desidera utilizzare. Dopo aver selezionato un metodo, viene visualizzato l'elenco **Aggiungi nuovo parametro** in modo che sia possibile selezionare i parametri da usare con il metodo. |
   ||||

   ad esempio:

   **Selezionare il file HIDX**

   ![Seleziona file HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selezionare il metodo**

   ![Select (metodo)](./media/connectors-create-api-3270/select-method.png)

   **Selezionare i parametri**

   ![Selezionare i parametri](./media/connectors-create-api-3270/add-parameters.png)

1. Al termine, salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, verranno visualizzati i passaggi dell'esecuzione. 
   I passaggi riusciti mostrano segni di spunta, mentre i passaggi non riusciti mostrano la lettera "X".

1. Per esaminare gli input e gli output per ogni passaggio, espandere il passaggio.

1. Per esaminare gli output, scegliere **Visualizza output non elaborati**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento del connettore](/connectors/si3270/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
