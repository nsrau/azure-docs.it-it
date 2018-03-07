---
title: Messaggi X12 per l'integrazione aziendale B2B - App per la logica di Azure | Microsoft Docs
description: Scambiare messaggi X12 in formato EDI per l'integrazione aziendale B2B con App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: c644dd98d468a8c99625c45bad3f06031ff22b4e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Scambiare messaggi X12 per l'integrazione aziendale con le app per la logica

Per poter scambiare messaggi X12 con App per la logica di Azure, è necessario creare un contratto X12 e archiviarlo nell'account di integrazione. Di seguito viene illustrata la procedura per la creazione di un contratto X12.

> [!NOTE]
> Questa pagina illustra le funzionalità X12 di App per la logica di Azure. Per altre informazioni, vedere [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure.
* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione e configurati con l'identificatore X12 in **Identità di business**.    
* Uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) obbligatorio da caricare nell'account di integrazione.

Dopo aver [creato un account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [aggiunto i partner](logic-apps-enterprise-integration-partners.md) e predisposto lo [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da usare, è possibile creare un contratto X12 seguendo questa procedura.

## <a name="create-an-x12-agreement"></a>Creare un contratto X12

1. Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure"). 

2. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.  

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Se **Tutti i servizi** non viene visualizzato, può essere necessario espandere il menu. Nella parte superiore del menu compresso, selezionare **Visualizza menu**.

3. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere il contratto.

   ![Selezionare l'account di integrazione in cui si vuole creare il contratto](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selezionare **Panoramica**, quindi selezionare il riquadro **Contratti**. Se non è presente il riquadro dei contratti, aggiungerlo. 

   ![Selezionare il riquadro "Contratti"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. In **Contratti** scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. In **Aggiungi**, digitare un **nome** per il contratto. Come tipo di contratto selezionare **X12**. Selezionare il **Partner host**, l'**Identità host**, il **Partner guest**, e l'**Identità guest** per il contratto. Per altre informazioni sulla proprietà, vedere la tabella seguente.

    ![Fornire i dettagli relativi al contratto](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Proprietà | DESCRIZIONE |
    | --- | --- |
    | NOME |Nome del contratto |
    | Tipo di contratto | Deve essere X12 |
    | Host Partner (Partner host) |Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
    | Host Identity (Identità host) |Un identificatore per il partner host |
    | Guest Partner (Partner guest) |Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
    | identità guest |Un identificatore per il partner guest |
    | Receive Settings (Impostazioni di ricezione) |Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto. |
    | Send Settings (Impostazioni di invio) |Queste proprietà si applicano a tutti i messaggi inviati da un contratto. |  

  > [!NOTE]
  > La risoluzione del contratto X12 dipende dall'associazione del qualificatore e dell'identificatore del mittente con il qualificatore e l'identificatore del ricevente definita nel messaggio del partner in arrivo. Se per il partner vengono modificati questi valori, è necessario aggiornare anche il contratto.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurare il modo in cui il contratto riceve i messaggi

Dopo aver impostato le proprietà del contratto, è possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in arrivo ricevuti dal partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di ricezione**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

    Il controllo **Impostazioni di ricezione** è suddiviso nelle sezioni seguenti: Identificatori, Riconoscimento, Schemi, Buste, Numeri di controllo, Convalide e Impostazioni interne.

2. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in arrivo conformi alle impostazioni selezionate.

### <a name="identifiers"></a>Identificatori

![Impostare le proprietà degli identificatori](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Proprietà | DESCRIZIONE |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |facoltativo. Immettere il valore relativo alle informazioni di autorizzazione. Se il valore immesso per ISA1 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| Qualificatore di sicurezza (ISA3) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |facoltativo. Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA3 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgment"></a>Acknowledgment (Riconoscimento)

![Impostare le proprietà di riconoscimento](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| TA1 expected (Previsto TA1) |Restituisce un riconoscimento tecnico al mittente dell'interscambio |
| FA expected (Previsto FA) |Restituisce un riconoscimento funzionale al mittente dell'interscambio. Specificare quindi se si vuole il riconoscimento 997 o 999, in base alla versione dello schema |
| Include AK2/IK2 Loop (Includi ciclo AK2/IK2) |Abilita la generazione di cicli AK2 nei riconoscimenti funzionali per i set di transazioni accettati |

### <a name="schemas"></a>Schemi

Scegliere uno schema per ogni tipo di transazione (ST1) e di applicazione mittente (GS2). Il messaggio in arrivo viene disassemblato dalla pipeline di ricezione mediante associazione dei valori ST1 e GS2 del messaggio a quelli definiti in questa pagina e dello schema del messaggio in arrivo a quello definito in questa pagina.

![Selezionare lo schema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Version |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Sender Application (GS02) (Applicazione mittente (GS02)) |Selezionare l'applicazione del mittente |
| SCHEMA |Selezionare il file di schema che si vuole usare. Gli schemi vengono aggiunti all'account di integrazione. |

> [!NOTE]
> Configurare lo [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessario che viene caricato nell'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Buste

![Specificare il separatore in un set di transazioni: scegliere un Identificatore standard o un Separatore ripetizioni](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Proprietà | DESCRIZIONE |
| --- | --- |
| Utilizzo ISA11 |Specifica il separatore da usare in un set di transazioni: <p>Selezionare l'**Identificatore standard** per usare un punto (.) per la notazione decimale invece della notazione decimale del documento in ingresso nella pipeline di ricezione EDI. <p>Selezionare il **Separatore ripetizioni** per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |

### <a name="control-numbers"></a>Numeri di controllo

![Selezionare la modalità di gestione dei numeri di controllo duplicati](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Disallow Interchange Control Number duplicates (Non consentire duplicati di numeri di controllo interscambio) |Consente di bloccare gli interscambi duplicati. Verifica il numero di controllo dell'interscambio (ISA13) del numero di controllo dell'interscambio ricevuto. Se viene rilevata una corrispondenza, la pipeline di ricezione non elabora l'interscambio. È possibile specificare il numero di giorni per l'esecuzione del controllo assegnando il valore appropriato all'opzione *Verifica ISA13 duplicati ogni (giorni)*. |
| Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo) |Consente di bloccare gli interscambi con numeri di controllo di gruppo duplicati. |
| Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni) |Consente di bloccare gli interscambi con numeri di controllo di set di transazioni duplicati. |

### <a name="validations"></a>Convalide

![Impostare le proprietà di convalida dei messaggi ricevuti](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Dopo aver completato ogni riga di convalida, ne viene aggiunta automaticamente un'altra. Se non si specifica alcuna regola, la convalida usa la riga predefinita.

| Proprietà | DESCRIZIONE |
| --- | --- |
| Tipo messaggio |Selezionare il tipo di messaggio EDI. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono mantenuti e non vengono rimossi. |
| Rimuovi zero iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono rimossi. |
| Criterio separatori finali |Consente di generare separatori finali. <p>Selezionare **Non consentiti** per non consentire delimitatori e separatori finali nell'interscambio ricevuto. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>Selezionare **Facoltativi** per accettare interscambi con o senza delimitatori e separatori finali. <p>Selezionare **Obbligatori** se l'interscambio deve contenere delimitatori e separatori finali. |

### <a name="internal-settings"></a>Impostazioni interne

![Selezionare le impostazioni interne](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Converti formato decimale implicito Nn in valore numerico in base 10 |Converte un numero EDI specificato nel formato Nn in un valore numerico in base 10 |
| Crea tag XML vuoti se sono consentiti separatori finali |Selezionare questa casella di controllo se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali. |
| Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Sospende solo le transazioni la cui convalida non riesce. |
| Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. | 
| Mantieni interscambio - Sospendi set transazioni in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende solo i set di transazioni che non superano la convalida, pur continuando a elaborare tutti gli altri set di transazioni. |
| Mantieni interscambio - Sospendi interscambio in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurare il modo in cui il contratto invia messaggi

È possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in uscita inviati al partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di avvio**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

    Il controllo **Impostazioni di invio** è suddiviso nelle sezioni seguenti: Identificatori, Riconoscimento, Schemi, Buste, Set di caratteri e separatori, Numeri di controllo e Convalida.

2. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in uscita conformi alle impostazioni selezionate.

### <a name="identifiers"></a>Identificatori

![Impostare le proprietà degli identificatori](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Proprietà | DESCRIZIONE |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |Immettere il valore relativo alle informazioni di autorizzazione. Se questo valore è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| ISA3 (Qualificatore di sicurezza) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA4 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgment"></a>Acknowledgment (Riconoscimento)

![Impostare le proprietà di riconoscimento](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Proprietà | DESCRIZIONE |
| --- | --- |
| TA1 expected (Previsto TA1) |Restituisce un riconoscimento tecnico (TA1) al mittente dell'interscambio. Questa impostazione specifica che il partner host che invia il messaggio richiede un riconoscimento dal partner guest nel contratto. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| FA expected (Previsto FA) |Restituisce un riconoscimento funzionale (FA) al mittente dell'interscambio. Specificare se si vuole il riconoscimento 997 o 999, in base alle versioni dello schema in uso. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| Versione FA |Selezionare la versione FA |

### <a name="schemas"></a>Schemi

![Selezionare lo schema da usare](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Proprietà | DESCRIZIONE |
| --- | --- |
| Version |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Schema |Selezionare lo schema da usare. Gli schemi si trovano nell'account di integrazione. Se si seleziona prima lo schema, viene automaticamente configurata la versione e il tipo di transizione  |

> [!NOTE]
> Configurare lo [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessario che viene caricato nell'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Buste

![Specificare il separatore in un set di transazioni: scegliere un Identificatore standard o un Separatore ripetizioni](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Utilizzo ISA11 |Specifica il separatore da usare in un set di transazioni: <p>Selezionare l'**Identificatore standard** per usare un punto (.) per la notazione decimale invece della notazione decimale del documento in ingresso nella pipeline di ricezione EDI. <p>Selezionare il **Separatore ripetizioni** per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |

### <a name="control-numbers"></a>Numeri di controllo

![Specificare le proprietà del numero di controllo](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Numero versione controllo (ISA12) |Selezionare la versione dello standard X12 |
| Indicatore di utilizzo (ISA15) |Selezionare il contesto di un interscambio.  I valori sono informazioni, dati di produzione o dati di test |
| SCHEMA |Genera i segmenti GS e ST per un interscambio con codifica X12 inviato alla pipeline di trasmissione |
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

Oltre al set di caratteri, è possibile immettere un set di delimitatori diverso per ogni tipo di messaggio. Se per un determinato schema del messaggio non è specificato un set di caratteri, viene usato il set di caratteri predefinito.

![Specificare i delimitatori per i tipi di messaggio](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Proprietà | DESCRIZIONE |
| --- | --- |
| Set di caratteri da usare |Per convalidare le proprietà selezionare il set di caratteri X12. Le opzioni sono: Basic, Extended e UTF8. |
| SCHEMA |Selezionare uno schema nell'elenco a discesa. Dopo aver completato ogni riga, viene aggiunta automaticamente una nuova riga. Per lo schema selezionato, selezionare il set di separatori da usare, in base alle descrizioni di separatore seguenti. |
| Tipo di input |Selezionare un tipo di input nell'elenco a discesa. |
| Component Separator |Per separare elementi di dati compositi immettere un singolo carattere. |
| Data Element Separator |Per separare elementi di dati semplici in elementi di dati compositi Immettere un singolo carattere. |
| Replacement Character |Immettere un carattere sostitutivo da usare per sostituire tutti i caratteri separatori nei dati di payload quando si genera il messaggio X12. |
| Segment Terminator |Per indicare la fine di un segmento EDI, immettere un singolo carattere. |
| Suffisso |Selezionare il carattere usato con l'identificatore di segmento. Se si designa un suffisso, l'elemento dati del terminatore di segmenti può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario designare un suffisso. |

> [!TIP]
> Per fornire i valori dei caratteri speciali, modificare il contratto come JSON e specificare il valore ASCII per il carattere speciale.

### <a name="validation"></a>Convalida

![Impostare le proprietà di convalida per l'invio di messaggi](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Dopo aver completato ogni riga di convalida, ne viene aggiunta automaticamente un'altra. Se non si specifica alcuna regola, la convalida usa la riga predefinita.

| Proprietà | DESCRIZIONE |
| --- | --- |
| Tipo messaggio |Selezionare il tipo di messaggio EDI. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono mantenuti e non vengono rimossi. |
| Rimuovi zero iniziali e finali |Tutti gli zero iniziali o finali vengono rimossi. |
| Criterio separatori finali |Consente di generare separatori finali. <p>Selezionare **Non consentiti** per non consentire delimitatori e separatori finali nell'interscambio inviato. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>Selezionare **Facoltativi** per inviare interscambi con o senza delimitatori e separatori finali. <p>Selezionare **Obbligatori** se l'interscambio inviato deve contenere delimitatori e separatori finali. |

## <a name="find-your-created-agreement"></a>Individuare il contratto creato

1.  Dopo aver impostato tutte le proprietà del contratto, scegliere **OK** nella pagina **Aggiungi** per completare la creazione del contratto e tornare all'account di integrazione.

    Il contratto appena aggiunto viene visualizzato nell'elenco **Contratti**.

2.  È anche possibile visualizzare i contratti nella panoramica dell'account di Integrazione. Nel menu dell'account di integrazione scegliere **Panoramica** e quindi selezionare il riquadro **Contratti**.

    ![Selezionare il riquadro "Contratti"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/x12/). 

## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  

