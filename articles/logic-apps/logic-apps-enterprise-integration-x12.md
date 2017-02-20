---
title: Creare un contratto X12 nelle app per la logica di Azure | Microsoft Docs
description: Informazioni su come creare un contratto X12 per Enterprise Integration Pack | App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 1a982309fe771c0c244c691ae648bd5f4844a825
ms.openlocfilehash: f7b61b85cadfabde6637e46f0e65108a29901198


---
# <a name="enterprise-integration-with-x12"></a>Enterprise integration con X12

## <a name="prereqs"></a>Prerequisiti
Prima di poter scambiare messaggi X12, è necessario creare un contratto X12 e archiviarlo nell'account di integrazione. Attenersi alla procedura seguente per creare un contratto X12.

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure  
* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) configurati con il qualificatore EDI X12 nelle identità di business.   
* [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessario da caricare sull'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="create-an-x12-agreement"></a>Creare un contratto X12
Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "portale di Azure"):  

1. Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure")
2. Selezionare **More services** (Altri servizi) e immettere **integrazione** nella casella di ricerca del filtro. Selezionare **Account di integrazione** nell'elenco dei risultati:    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Selezionare l'account di integrazione a cui aggiungere il certificato:    
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)   
4. Selezionare il riquadro **Accordi** . Se il riquadro dei contratti non viene visualizzato, aggiungerlo:     
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Selezionare il pulsante **Aggiungi** nel pannello Accordi visualizzato.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     
6. Nel pannello Accordi visualizzato immettere un **nome** per il contratto e quindi selezionare **Tipo di contratto**, **Partner host**, **Identità host**, **Partner guest** e **Identità guest**.    
![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

| Proprietà | Descrizione |
| --- | --- |
| Nome |Nome del contratto |
| Tipo di contratto | Deve essere X12 |
| Host Partner (Partner host) |Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che sta configurando il contratto |
| Host Identity (Identità host) |Un identificatore per il partner host |
| Guest Partner (Partner guest) |Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host |
| identità guest |Un identificatore per il partner guest |
| Receive Settings (Impostazioni di ricezione) |Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto |
| Send Settings (Impostazioni di invio) |Queste proprietà si applicano a tutti i messaggi inviati da un contratto |  

> [!NOTE]
> La risoluzione del contratto X12 dipende dall'associazione del qualificatore e dell'identificatore del mittente con il qualificatore e l'identificatore del ricevente definita nel messaggio del partner in arrivo.  Se questi valori sono stati modificati per il partner, modificare anche il contratto.
> 
> 

## <a name="receive-settings"></a>Receive Settings (Impostazioni di ricezione)

1. Selezionare **Receive Settings** (Impostazioni di ricezione) per configurare la gestione dei messaggi ricevuti tramite questo contratto.  
2. Il controllo delle impostazioni di ricezione è suddiviso nelle sezioni seguenti, inclusi Identificatori, Riconoscimenti, Schemi, Buste, Numeri di controllo, Convalide e Impostazioni interne. Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Ecco una visualizzazione di questi controlli. Configurarli in base a come si vuole che il contratto identifichi e gestisca i messaggi in ingresso  

### <a name="identifiers"></a>Identificatori

![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Proprietà | Descrizione |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |Facoltativo. Immettere il valore relativo alle informazioni di autorizzazione. Se il valore immesso per ISA1 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| Qualificatore di sicurezza (ISA3) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |Facoltativo. Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA3 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgments"></a>Ringraziamenti

![](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Proprietà | Descrizione |
| --- | --- |
| TA1 expected (Previsto TA1) |Restituisce un riconoscimento tecnico al mittente dell'interscambio |
| FA expected (Previsto FA) |Restituisce un riconoscimento funzionale al mittente dell'interscambio. Specificare quindi se si vuole il riconoscimento 997 o 999, in base alla versione dello schema |
| Include AK2/IK2 Loop (Includi ciclo AK2/IK2) |Abilita la generazione di cicli AK2 nei riconoscimenti funzionali per i set di transazioni accettati |


### <a name="schemas"></a>Schemi
Scegliere uno schema per ogni tipo di transazione (ST1) e di applicazione mittente (GS2). Il messaggio in arrivo viene disassemblato dalla pipeline di ricezione mediante associazione dei valori ST1 e GS2 del messaggio a quelli definiti in questa pagina e dello schema del messaggio in arrivo a quello definito in questa pagina.

![](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Proprietà | Descrizione |
| --- | --- |
| Versione |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Sender Application (GS02) (Applicazione mittente (GS02)) |Selezionare l'applicazione del mittente |
| Schema |Selezionare il file di schema che si vuole usare. Gli schemi sono stati aggiunti nell'account di integrazione |

> [!NOTE]
> Configurare lo [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessario che viene caricato nell'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Buste

![](./media/logic-apps-enterprise-integration-x12/x12-34.png) 

| Proprietà | Descrizione |
| --- | --- |
| Utilizzo ISA11 |Usare questo campo per specificare il separatore in un set di transazioni:</br></br>Selezionare l'identificatore Standard per usare la notazione decimale "." al posto della notazione decimale del documento in ingresso nella pipeline di ricezione EDI.</br></br>Selezionare il separatore ripetizioni per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Il carattere (^) è ad esempio usato in genere come separatore di ripetizioni. Per gli schemi HIPAA, è possibile usare solo (^). |

### <a name="control-numbers"></a>Numeri di controllo

![](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Proprietà | Descrizione |
| --- | --- |
| Disallow Interchange Control Number duplicates (Non consentire duplicati di numeri di controllo interscambio) |Interscambi duplicati di blocchi. Consente di verificare il numero di controllo dell'interscambio (ISA13) per il numero di controllo dell'interscambio ricevuto. Se viene rilevata una corrispondenza, la pipeline di ricezione non elabora l'interscambio. È possibile specificare l'intervallo di giorni con cui verrà eseguito il controllo assegnando il valore appropriato all'opzione *Check for duplicate ISA13 every x days* (Verifica ISA13 duplicati ogni x giorni) |
| Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo) |Consente di bloccare gli interscambi con numeri di controllo di gruppo duplicati |
| Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni) |Consente di bloccare gli interscambi con numeri di controllo di set di transazioni duplicati |

### <a name="validations"></a>Convalide

![](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Tipi di messaggio EDI, ad esempio 850-Ordine d'acquisto o 999-Riconoscimento implementazione. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Eventuali spazi e zeri aggiuntivi iniziali o finali vengono mantenuti e non vengono rimossi. |
| Criterio separatori finali |Genera separatori finali nell'interscambio ricevuto. Le opzioni includono Non consentiti, Facoltativi e Obbligatori. |

### <a name="internal-settings"></a>Impostazioni interne

![](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Proprietà | Descrizione |
| --- | --- |
| Converti formato decimale implicito Nn in valore numerico in base 10 |Converte un numero EDI specificato nel formato Nn in un valore numerico in base&10; |
| Crea tag XML vuoti se sono consentiti separatori finali |Selezionare questa casella di controllo se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali. |
| Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Sospende solo le transazioni in cui la convalida non riesce |
| Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Sospendi tutto l'interscambio se la convalida di uno o più set di transazioni dell'interscambio non riesce | 
| Mantieni interscambio - Sospendi set transazioni in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende solo i set di transazioni che non superano la convalida, pur continuando a elaborare tutti gli altri set di transazioni |
| Mantieni interscambio - Sospendi interscambio in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch.  Sospendi tutto l'interscambio se la convalida di uno o più set di transazioni dell'interscambio non riesce |

Dopo aver impostato le proprietà delle impostazioni di ricezione, selezionare il pulsante **OK**  
   
Il contratto è pronto per gestire i messaggi in ingresso conformi allo schema selezionato.

## <a name="send-settings"></a>Send Settings (Impostazioni di invio)

1. Selezionare **Impostazioni di invio** per configurare la gestione dei messaggi inviati tramite questo contratto.  

2. Il controllo Impostazioni di invio è suddiviso nelle sezioni seguenti, inclusi Identificatori, Riconoscimento Schemi, Buste, Numeri di controllo, Character Sets and Separators (Set di caratteri e separatori) e Convalida.  Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Ecco una visualizzazione di questi controlli. Configurarli in base a come si vuole che il contratto identifichi e gestisca i messaggi in ingresso.


### <a name="identifiers"></a>Identificatori

![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Proprietà | Descrizione |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |Immettere il valore relativo alle informazioni di autorizzazione. Se questo valore è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| ISA3 (Qualificatore di sicurezza) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA4 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgment"></a>Acknowledgment (Riconoscimento)

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Proprietà | Descrizione |
| --- | --- |
| TA1 expected (Previsto TA1) |Selezionare questa casella di controllo per restituire un riconoscimento tecnico (TA1) al mittente dell'interscambio. Questa impostazione specifica che il partner host che invia il messaggio richiede un riconoscimento dal partner guest nel contratto. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| FA expected (Previsto FA) |Selezionare questa casella di controllo per restituire un riconoscimento funzionale (FA) al mittente dell'interscambio, quindi specificare se si vuole il riconoscimento 997 o 999, in base alle versioni dello schema in uso. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| Versione FA |Selezionare la versione FA |

### <a name="schemas"></a>Schemi

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Proprietà | Descrizione |
| --- | --- |
| Versione |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Schema |Selezionare lo schema da usare. Gli schemi si trovano nell'account di integrazione. Se si seleziona prima lo schema, viene automaticamente configurata la versione e il tipo di transizione  |

> [!NOTE]
> Configurare lo [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessario che viene caricato nell'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Buste

![](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Proprietà | Descrizione |
| --- | --- |
| Utilizzo ISA11 |Usare questo campo per specificare il separatore in una transazione |
| Identificatore standard |Selezionare l'identificatore Standard per usare la notazione decimale "." |
| Repetition Separator |Selezionare il separatore ripetizioni per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Il carattere (^) è ad esempio usato in genere come separatore di ripetizioni. Per gli schemi HIPAA, è possibile usare solo (^) |

### <a name="control-numbers"></a>Numeri di controllo

![](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Proprietà | Descrizione |
| --- | --- |
| Numero versione controllo (ISA12) |Selezionare la versione dello standard X12 |
| Indicatore di utilizzo (ISA15) |Selezionare il contesto di un interscambio.  I valori sono informazioni, dati di produzione o dati di test |
| Schema |Genera i segmenti GS e ST per un interscambio con codifica X12 inviato alla pipeline di trasmissione |
| GS1 |Facoltativo, selezionare un valore per il codice funzionale nell'elenco a discesa |
| GS2 |Facoltativo, mittente dell'applicazione |
| GS3 |Facoltativo, ricevente dell'applicazione |
| GS4 |Facoltativo, selezionare CCYYMMDD o YYMMDD |
| GS5 |Facoltativo, selezionare HHMM, HHMMSS o HHMMSSdd |
| GS7 |Facoltativo, selezionare un valore per l'agenzia responsabile nell'elenco a discesa |
| GS8 |Facoltativo, versione del documento |
| Numero di controllo interscambio (ISA13) |Obbligatorio,inserire un intervallo di valori per il numero di controllo dell'interscambio. Immettere un valore numerico compreso tra 1 e 999999999 |
| Numero di controllo gruppo (GS06) |Obbligatorio, immettere un intervallo di numeri per il numero di controllo del gruppo. Immettere un valore numerico compreso tra 1 e 999999999 |
| Numero di controllo set transazioni (ST02) |Obbligatorio, immettere un intervallo di numeri per il numero di controllo del set di transazioni. Immettere un intervallo di valori numerici compreso tra 1 e 999999999 |
| Prefisso |Facoltativo, pensato per l'intervallo di numeri di controllo del set di transazioni usati nel riconoscimento. Immettere un valore numerico per i due campi centrali e, se si vuole, un valore alfanumerico per i campi relativi al prefisso e al suffisso. I campi centrali sono obbligatori e contengono i valori minimo e massimo per il numero di controllo |
| Suffisso |Facoltativo, pensato per l'intervallo di numeri di controllo del set di transazioni usati nel riconoscimento. Immettere un valore numerico per i due campi centrali e, se si vuole, un valore alfanumerico per i campi relativi al prefisso e al suffisso. I campi centrali sono obbligatori e contengono i valori minimo e massimo per il numero di controllo |

### <a name="character-sets-and-separators"></a>Character Sets and Separators (Set di caratteri e separatori)

Oltre al set di caratteri, è possibile immettere un diverso set di delimitatori da usare per ogni tipo di messaggio. Se per un determinato schema del messaggio non è specificato un set di caratteri, viene usato il set di caratteri predefinito.

![](./media/logic-apps-enterprise-integration-x12/x12-9.png) 


| Proprietà | Descrizione |
| --- | --- |
| Set di caratteri da usare |Selezionare il set di caratteri X12 per convalidare le proprietà.  Le opzioni sono: Basic, Extended e UTF8 |
| Schema |Selezionare uno schema nell'elenco a discesa. Selezionare i separatori da usare per lo schema selezionato |
| Tipo di input |Selezionare un tipo di input nell'elenco a discesa |
| Separatore elementi componenti |Immettere un singolo carattere per separare elementi di dati compositi |
| Data Element Separator |Immettere un singolo carattere per separare elementi di dati semplici in elementi di dati compositi |
| Replacement Character |Inserire un carattere di sostituzione. Quando si genera il messaggio X12 in uscita, tutte le istanze dei caratteri separatori nei dati di payload vengono sostituite con il carattere specificato |
| Segment Terminator |Immettere un singolo carattere per indicare la fine di un segmento EDI |
| Suffisso |Selezionare il carattere usato con l'identificatore di segmento. Se si designa un suffisso, l'elemento dati del terminatore di segmenti può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario designare un suffisso. |

### <a name="validation"></a>Convalida

![](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Selezionare il tipo di messaggio dall'elenco |
| EDI Validation (Convalida EDI) |Selezionando questa opzione si consente la convalida nel ricevitore dell'interscambio. Questa operazione esegue la convalida EDI degli elementi dati del set di transazioni, convalidando i tipi di dati, le restrizioni relative alla lunghezza, gli elementi dati vuoti e i separatori finali |
| Convalida estesa |Se si seleziona questa opzione, viene abilitata la convalida estesa degli interscambi ricevuti dal mittente dell'interscambio, inclusi la convalida della lunghezza dei campi, la facoltatività e il conteggio ripetizioni, oltre alla convalida dei tipi di dati XSD. È possibile abilitare la convalida dell'estensione senza abilitare la convalida EDI o viceversa. |
| Consenti zeri iniziali/finali |Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo quando l'elemento dati in un interscambio EDI non rispetta il requisito di lunghezza a causa della presenza di spazi iniziali o finali, ma lo rispetta quando questi vengono rimossi. |
| Separatore finale |Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo se un elemento dati in un interscambio EDI non rispetta il requisito di lunghezza solo a causa della presenza di zero iniziali (o finali) o di spazi finali e lo rispetta quando questi vengono rimossi.</br></br>Selezionare Non consentiti per non consentire delimitatori e separatori finali in un interscambio ricevuto dal mittente dell'interscambio. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido.</br></br>Selezionare Facoltativi per accettare interscambi con o senza delimitatori e separatori finali.</br></br>Selezionare Obbligatori se l'interscambio ricevuto deve contenere delimitatori e separatori finali. |

Dopo aver impostato le proprietà delle impostazioni di invio, selezionare il pulsante **OK**.  Il contratto è pronto per gestire i messaggi in uscita conformi allo schema selezionato.

Selezionare OK per creare il contratto.

Selezionare il riquadro **Agreements** (Contratti) nel pannello Integration Account (Account di integrazione) e verrà visualizzato il nuovo contratto aggiunto.  
![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  




<!--HONumber=Feb17_HO1-->


