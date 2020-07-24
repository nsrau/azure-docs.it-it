---
title: Inviare e ricevere messaggi X12 per B2B
description: Scambiare messaggi X12 per scenari di integrazione aziendale B2B usando App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87066129"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Scambiare messaggi X12 per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Per gestire i messaggi X12 in App per la logica di Azure è possibile usare il connettore X12, che fornisce trigger e azioni per la gestione della comunicazione X12. Per informazioni sui messaggi EDIFACT, vedere [Scambiare messaggi EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica da cui si vuole usare il connettore X12 e un trigger che avvia il flusso di lavoro dell'app per la logica. Il connettore X12 fornisce solo azioni, non trigger. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e collegato all'app per la logica in cui si prevede di usare il connettore X12. Sia l'app per la logica che l'account di integrazione devono essere nella stessa località o nella stessa area di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione tramite il qualificatore di identità X12.

* Gli [schemi](../logic-apps/logic-apps-enterprise-integration-schemas.md) da usare per la convalida XML già aggiunti all'account di integrazione. Se si usano gli schemi HIPAA (Health Insurance Portability and Accountability Act), vedere [Schemi HIPAA](#hipaa-schemas).

* Per usare il connettore X12, occorre prima creare un [contratto](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 tra i partner commerciali e archiviarlo nell'account di integrazione. Se si usano gli schemi HIPAA, è necessario aggiungere una sezione `schemaReferences` al contratto. Per altre informazioni, vedere [Schemi HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Receive Settings (Impostazioni di ricezione)

Dopo aver impostato le proprietà del contratto, è possibile configurare la modalità di identificazione e gestione dei messaggi in ingresso ricevuti dal partner tramite il contratto.

1. In **Aggiungi**, selezionare **Impostazioni di ricezione**.

1. Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Le **impostazioni di ricezione** sono organizzate in queste sezioni:

   * [Identificatori](#inbound-identifiers)
   * [Acknowledgement](#inbound-acknowledgement)
   * [Schemi](#inbound-schemas)
   * [Buste](#inbound-envelopes)
   * [Numeri di controllo](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [Impostazioni interne](#inbound-internal-settings)

   Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

1. Al termine, assicurarsi di salvare le impostazioni scegliendo **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Impostazioni di ricezione - identificatori

![Proprietà degli identificatori per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **ISA1 (qualificatore di autorizzazione)** | Valore del qualificatore di autorizzazione da usare. Il valore predefinito è **00 - Nessuna informazione di autorizzazione presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA2**. |
| **ISA2** | Valore delle informazioni di autorizzazione da usare quando la proprietà **ISA1** non è impostata su **00 - Nessuna informazione di autorizzazione presente**. Il valore di questa proprietà deve essere costituito da un minimo di uno e un massimo di 10 caratteri alfanumerici. |
| **ISA3 (qualificatore di sicurezza)** | Valore del qualificatore di sicurezza da usare. Il valore predefinito è **00 - Nessuna informazione di sicurezza presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA4**. |
| **ISA4** | Valore delle informazioni di sicurezza da usare quando la proprietà **ISA3** non è impostata su **00 - Nessuna informazione di sicurezza presente**. Il valore di questa proprietà deve essere costituito da un minimo di uno e un massimo di 10 caratteri alfanumerici. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Impostazioni di ricezione - Acknowledgement

![Acknowledgement per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Previsto TA1** | Restituisce un riconoscimento tecnico (TA1) al mittente dell'interscambio. |
| **Previsto FA** | Restituisce un riconoscimento funzionale (FA) al mittente dell'interscambio. <p>Per la proprietà **Versione FA**, in base alla versione dello schema selezionare l'acknowledgment 997 o 999. <p>Per abilitare la generazione di cicli AK2 negli acknowledgment funzionali per i set di transazioni accettati, selezionare **Includi ciclo AK2/IK2**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Impostazioni di ricezione - Schemi

![Schemi per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall'[account di integrazione](./logic-apps-enterprise-integration-create-integration-account.md) per ogni tipo di transazione (ST01) e applicazione mittente (GS02). La pipeline di ricezione EDI disassembla il messaggio in arrivo associando i valori e lo schema impostati in questa sezione ai valori di ST01 e GS02 nel messaggio in arrivo e allo schema del messaggio in arrivo. Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Versione** | Versione X12 per lo schema |
| **Tipo di transazione (ST01)** | Tipo di transazione |
| **Applicazione mittente (GS02)** | Applicazione mittente |
| **Schema** | File di schema da usare |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Impostazioni di ricezione - Buste

![Separatori da usare nei set di transazioni per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Utilizzo ISA11** | Separatore da usare in un set di transazioni: <p>- **Identificatore standard**: viene usato un punto (.) per la notazione decimale invece della notazione decimale del documento in ingresso nella pipeline di ricezione EDI. <p>- **Separatore ripetizioni**: specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Impostazioni di ricezione - Numeri di controllo

![Gestione dei numeri di controllo duplicati per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Proprietà | Descrizione |
|----------|-------------|
| **Non consentire numeri di controllo interscambio duplicati** | Consente di bloccare gli interscambi duplicati. Verifica il numero di controllo interscambio (ISA13) del numero di controllo interscambio ricevuto. Se viene rilevata una corrispondenza, la pipeline di ricezione EDI non elabora l'interscambio. <p><p>Per specificare il numero di giorni per l'esecuzione del controllo, immettere un valore per la proprietà **Verifica ISA13 duplicati ogni (giorni)** . |
| **Non consentire numeri di controllo del gruppo duplicati** | Blocca gli interscambi con numeri di controllo del gruppo duplicati. |
| **Non consentire numeri di controllo set transazioni duplicati** | Blocca gli interscambi con numeri di controllo di set di transazioni duplicati. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Impostazioni di ricezione - Convalide

![Convalide per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

La riga **Predefinita** mostra le regole di convalida usate per un tipo di messaggio EDI. Se si vogliono definire regole diverse, selezionare ogni casella in cui si vuole impostare la regola su **true**. Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Tipo di messaggio** | Tipo di messaggio EDI |
| **Convalida EDI** | Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| **Convalida estesa** | Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla lunghezza dell'elemento dati (minima o massima). |
| **Consenti zero iniziali e finali** | Tutti gli zero iniziali e finali e gli spazi vengono mantenuti. e non vengono rimossi. |
| **Rimuovi zero iniziali e finali** | Tutti gli zero iniziali e finali e i gli spazi vengono rimossi. |
| **Criteri separatori finali** | Consente di generare separatori finali. <p>- **Non consentiti**: non consente delimitatori e separatori finali nell'interscambio in ingresso. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>- **Facoltativi**: accetta interscambi con o senza delimitatori e separatori finali. <p>- **Obbligatori**: l'interscambio in ingresso deve contenere delimitatori e separatori finali. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Impostazioni di ricezione - Impostazioni interne

![Impostazioni interne per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Converti formato decimale implicito Nn in valore numerico in base 10** | Converte un numero EDI specificato nel formato "Nn" in un valore numerico in base 10. |
| **Crea tag XML vuoti se sono consentiti separatori finali** | Selezionare questa opzione se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali. |
| **Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore** | Analizza ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Sospende solo le transazioni la cui convalida non riesce. |
| **Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore** | Analizza ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |
| **Mantieni interscambio - Sospendi set transazioni in caso di errore** | Mantiene l'interscambio intatto e crea un documento XML per l'intero interscambio in batch. Sospende solo i set di transazioni che non superano la convalida, ma continua a elaborare tutti gli altri set di transazioni. |
| **Mantieni interscambio - Sospendi interscambio in caso di errore** |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Send Settings (Impostazioni di invio)

Dopo aver impostato le proprietà del contratto, è possibile configurare la modalità di identificazione e gestione dei messaggi in uscita inviati dal partner tramite il contratto.

1. In **Aggiungi**, selezionare **Impostazioni di avvio**.

1. Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

   Le **impostazioni di invio** sono organizzate in queste sezioni:

   * [Identificatori](#outbound-identifiers)
   * [Acknowledgement](#outbound-acknowledgement)
   * [Schemi](#outbound-schemas)
   * [Buste](#outbound-envelopes)
   * [Numero versione controllo](#outbound-control-version-number)
   * [Numeri di controllo](#outbound-control-numbers)
   * [Set di caratteri e separatori](#outbound-character-sets-separators)
   * [Convalida](#outbound-validation)

1. Al termine, assicurarsi di salvare le impostazioni scegliendo **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Impostazioni di invio - Identificatori

![Proprietà degli identificatori per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **ISA1 (qualificatore di autorizzazione)** | Valore del qualificatore di autorizzazione da usare. Il valore predefinito è **00 - Nessuna informazione di autorizzazione presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA2**. |
| **ISA2** | Valore delle informazioni di autorizzazione da usare quando la proprietà **ISA1** non è impostata su **00 - Nessuna informazione di autorizzazione presente**. Il valore di questa proprietà deve essere costituito da un minimo di uno e un massimo di 10 caratteri alfanumerici. |
| **ISA3 (qualificatore di sicurezza)** | Valore del qualificatore di sicurezza da usare. Il valore predefinito è **00 - Nessuna informazione di sicurezza presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA4**. |
| **ISA4** | Valore delle informazioni di sicurezza da usare quando la proprietà **ISA3** non è impostata su **00 - Nessuna informazione di sicurezza presente**. Il valore di questa proprietà deve essere costituito da un minimo di uno e un massimo di 10 caratteri alfanumerici. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Impostazioni di invio - Acknowledgement

![Proprietà di acknowledgement per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Previsto TA1** | Restituisce un riconoscimento tecnico (TA1) al mittente dell'interscambio. <p>Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest nel contratto. Questi acknowledgment sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| **Previsto FA** | Restituisce un riconoscimento funzionale (FA) al mittente dell'interscambio. Per la proprietà **Versione FA**, in base alla versione dello schema selezionare l'acknowledgment 997 o 999. <p>Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest nel contratto. Questi acknowledgment sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Impostazioni di invio - Schemi

![Schemi per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall'[account di integrazione](./logic-apps-enterprise-integration-create-integration-account.md) per ogni tipo di transazione (ST01). Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Versione** | Versione X12 per lo schema |
| **Tipo di transazione (ST01)** | Tipo di transazione dello schema |
| **Schema** | File di schema da usare. Se si seleziona prima lo schema, la versione e il tipo di transazione vengono impostati automaticamente. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Impostazioni di invio - Buste

![Separatori in un set di transazioni da usare per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Utilizzo ISA11** | Separatore da usare in un set di transazioni: <p>- **Identificatore standard**: viene usato un punto (.) per la notazione decimale invece della notazione decimale del documento in uscita nella pipeline di trasmissione EDI. <p>- **Separatore ripetizioni**: specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Impostazioni di invio - Numero versione controllo

![Numero di versione del controllo per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall'[account di integrazione](./logic-apps-enterprise-integration-create-integration-account.md) per ogni interscambio. Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Numero versione controllo (ISA12)** | Versione dello standard X12 |
| **Indicatore di utilizzo (ISA15)** | Contesto di un interscambio, che può essere costituito da dati di tipo **Test**, **Informazioni** o **Produzione** |
| **Schema** | Schema da usare per la generazione dei segmenti GS e ST per un interscambio con codifica X12 inviato alla pipeline di trasmissione EDI. |
| **GS1** | Facoltativo, selezionare il codice funzionale. |
| **GS2** | Facoltativo, specificare il mittente dell'applicazione. |
| **GS3** | Facoltativo, specificare il ricevitore dell'applicazione. |
| **GS4** | Facoltativo, selezionare **CCYYMMDD** o **YYMMDD**. |
| **GS5** | Facoltativo, selezionare **HHMM**, **HHMMSS** o **HHMMSSdd**. |
| **GS7** | Facoltativo, selezionare un valore per l'agenzia responsabile. |
| **GS8** | Facoltativo, specificare la versione del documento dello schema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Impostazioni di invio - Numeri di controllo

![Numeri di controllo per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Numero di controllo interscambio (ISA13)** | Intervallo di valori per il numero di controllo interscambio, che può avere un valore compreso tra 1 e 999999999 |
| **Numero di controllo gruppo (GS06)** | Intervallo di valori per il numero di controllo gruppo, che può avere un valore compreso tra 1 e 999999999 |
| **Numero di controllo set transazioni (ST02)** | Intervallo di valori per il numero di controllo del set di transazioni, che può avere un valore compreso tra 1 e 999999999 <p>- **Prefisso**: facoltativo, valore alfanumerico <br>- **Suffisso**: facoltativo, valore alfanumerico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Impostazioni di invio - Set di caratteri e separatori

![Delimitatori per i tipi di messaggio nei messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

La riga **Predefinito** mostra il set di caratteri usato come delimitatori per uno schema del messaggio. Se non si vuole usare il set di caratteri **Predefinito**, è possibile immettere un set di delimitatori diverso per ogni tipo di messaggio. Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

> [!TIP]
> Per fornire i valori dei caratteri speciali, modificare il contratto come JSON e specificare il valore ASCII per il carattere speciale.

| Proprietà | Descrizione |
|----------|-------------|
| **Set di caratteri da usare** | Set di caratteri X12, che può essere **Base**, **Esteso** o **UTF8**. |
| **Schema** | Schema da usare. Dopo aver selezionato lo schema, selezionare il set di caratteri da usare, in base alle descrizioni di separatori seguenti. |
| **Tipo di input** | Tipo di input del set di caratteri |
| **Separatore componenti** | Singolo carattere che separa elementi dati compositi |
| **Separatore elementi dati** | Singolo carattere che separa elementi dati semplici all'interno di dati compositi |
| **Separatore caratteri di sostituzione** | Carattere sostitutivo che sostituisce tutti i caratteri separatori nei dati di payload quando si genera il messaggio X12 in uscita |
| **Carattere di terminazione segmento** | Singolo carattere che indica la fine di un segmento EDI |
| **Suffisso** | Carattere da usare con l'identificatore di segmento. Se si specifica un suffisso, l'elemento dati del carattere di terminazione segmento può essere vuoto. Se il carattere di terminazione del segmento viene lasciato vuoto, è necessario specificare un suffisso. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Impostazioni di invio - Convalida

![Proprietà di convalida per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

La riga **Predefinita** mostra le regole di convalida usate per un tipo di messaggio EDI. Se si vogliono definire regole diverse, selezionare ogni casella in cui si vuole impostare la regola su **true**. Dopo aver completato ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Tipo di messaggio** | Tipo di messaggio EDI |
| **Convalida EDI** | Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| **Convalida estesa** | Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla lunghezza dell'elemento dati (minima o massima). |
| **Consenti zero iniziali e finali** | Tutti gli zero iniziali e finali e gli spazi vengono mantenuti. e non vengono rimossi. |
| **Rimuovi zero iniziali e finali** | Tutti gli zero iniziali e finali e i gli spazi vengono rimossi. |
| **Criteri separatori finali** | Consente di generare separatori finali. <p>- **Non consentiti**: non consente delimitatori e separatori finali nell'interscambio in uscita. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>- **Facoltativi**: invia interscambi con o senza delimitatori e separatori finali. <p>- **Obbligatori**: l'interscambio in uscita deve contenere delimitatori e separatori finali. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Schemi HIPAA e tipi di messaggi

Quando si usano gli schemi HIPAA e i tipi di messaggio 277 o 837, è necessario eseguire alcuni passaggi aggiuntivi. I [numeri di versione del documento (GS8)](#outbound-control-version-number) per questi tipi di messaggio sono costituiti da più di 9 caratteri, ad esempio "005010X222A1". Inoltre, alcuni numeri di versione del documento sono mappati a tipi di messaggio o varianti. Se non si fa riferimento al tipo di messaggio corretto nello schema e nel contratto, si riceve questo messaggio di errore:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Questa tabella elenca i tipi di messaggi interessati, le varianti e i numeri di versione del documento mappati a tali tipi di messaggi:

| Tipo di messaggio o variante |  Descrizione | Numero di versione del documento (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Health Care Information Status Notification | 005010X212 |
| 837_I | Health Care Claim Institutional | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Health Care Claim Dental | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Health Care Claim Professional | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Occorre anche disabilitare la convalida EDI quando si usano questi numeri di versione del documento perché generano un errore che indica che la lunghezza dei caratteri non è valida.

Per specificare i numeri di versione del documento e i tipi di messaggio, seguire questa procedura:

1. Nello schema HIPAA sostituire il tipo di messaggio corrente con il tipo di messaggio variante relativo al numero di versione del documento che si vuole usare.

   Si supponga ad esempio di voler usare il numero di versione del documento `005010X222A1` con il tipo di messaggio `837`. Nello schema sostituire ogni valore `"X12_00501_837"` con il valore `"X12_00501_837_P"`.

   Per aggiornare lo schema, seguire questa procedura:

   1. Nel portale di Azure passare all'account di integrazione. Trovare e scaricare lo schema. Sostituire il tipo di messaggio e rinominare il file di schema, quindi caricare lo schema modificato nell'account di integrazione. Per altre informazioni, vedere [Modificare gli schemi](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Nelle impostazioni dei messaggi del contratto selezionare lo schema modificato.

1. Nell'oggetto `schemaReferences` del contratto aggiungere un'altra voce che specifichi il tipo di messaggio variante corrispondente al numero di versione del documento.

   Si supponga ad esempio di voler usare il numero di versione del documento `005010X222A1` per il tipo di messaggio `837`. Il contratto include una sezione `schemaReferences` con le proprietà e i valori seguenti:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   In questa sezione `schemaReferences` aggiungere un'altra voce con i valori seguenti:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Al termine, la sezione `schemaReferences` avrà un aspetto simile al seguente:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Nelle impostazioni dei messaggi del contratto disabilitare la convalida EDI deselezionando la casella di controllo **Convalida EDI** per ogni tipo di messaggio o per tutti i tipi di messaggio se si usano i valori **predefiniti**.

   ![Disabilitare la convalida per tutti i tipi di messaggio o per ogni tipo di messaggio](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altre informazioni tecniche su questo connettore, ad esempio su azioni e limiti descritti nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/x12/).

> [!NOTE]
> Per le app per la logica in un [ambiente del servizio di integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la versione con etichetta ISE del connettore usa i [limiti dei messaggi B2B per ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
