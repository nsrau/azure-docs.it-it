---
title: Inviare e ricevere messaggi X12 per B2B
description: Scambiare messaggi X12 per gli scenari di integrazione aziendale B2B usando app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613095"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Scambiare messaggi X12 per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Per lavorare con i messaggi X12 in app per la logica di Azure, è possibile usare il connettore X12, che fornisce trigger e azioni per la gestione della comunicazione X12. Per informazioni sui messaggi EDIFACT, vedere la pagina relativa ai [messaggi di Exchange EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica da cui si vuole usare il connettore X12 e un trigger che avvia il flusso di lavoro dell'app per la logica. Il connettore X12 fornisce solo azioni, non trigger. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e collegato all'app per la logica in cui si prevede di usare il connettore X12. Sia l'app per la logica che l'account di integrazione devono esistere nella stessa località o nell'area di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione utilizzando il qualificatore di identità X12.

* Gli [schemi](../logic-apps/logic-apps-enterprise-integration-schemas.md) da usare per la convalida XML che è già stata aggiunta all'account di integrazione. Se si lavora con gli schemi HIPAA (Health Insurance Portability and Accountability Act), vedere gli [schemi HIPAA](#hipaa-schemas).

* Prima di poter usare il connettore X12, è necessario creare un [contratto](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 tra i partner commerciali e archiviare tale contratto nell'account di integrazione. Se si lavora con gli schemi HIPAA (Health Insurance Portability and Accountability Act), è necessario aggiungere una `schemaReferences` sezione al contratto. Per ulteriori informazioni, vedere gli [schemi HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Receive Settings (Impostazioni di ricezione)

Dopo aver impostato le proprietà dell'accordo, è possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in ingresso ricevuti dal partner tramite il presente contratto.

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

1. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Impostazioni di ricezione-identificatori

![Proprietà identificatore per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **ISA1 (Qualificatore di autorizzazione)** | Valore del qualificatore di autorizzazione che si desidera utilizzare. Il valore predefinito è **00-nessuna informazione di autorizzazione presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA2** . |
| **ISA2** | Valore delle informazioni di autorizzazione da utilizzare quando la proprietà **ISA1** non è **00-nessuna informazione di autorizzazione presente**. Il valore di questa proprietà deve contenere un minimo di un carattere alfanumerico e un massimo di 10. |
| **Qualificatore di sicurezza (ISA3)** | Valore del qualificatore di sicurezza che si desidera utilizzare. Il valore predefinito è **00-nessuna informazione di sicurezza presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA4** . |
| **ISA4** | Valore delle informazioni di sicurezza da utilizzare quando la proprietà **ISA3** non è **00-nessuna informazione di sicurezza presente**. Il valore di questa proprietà deve contenere un minimo di un carattere alfanumerico e un massimo di 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Impostazioni di ricezione-riconoscimento

![Riconoscimento per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Previsto TA1** | Restituisce un riconoscimento tecnico (TA1) al mittente dell'interscambio. |
| **Previsto FA** | Restituisce un riconoscimento funzionale (FA) al mittente dell'interscambio. <p>Per la proprietà della **versione fa** , in base alla versione dello schema, selezionare i riconoscimenti 997 o 999. <p>Per abilitare la generazione di cicli AK2 nei riconoscimenti funzionali per i set di transazioni accettati, selezionare **Includi ciclo AK2/IK2**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Impostazioni di ricezione-schemi

![Schemi per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) per ogni tipo di transazione (ST01) e applicazione mittente (GS02). La pipeline di ricezione EDI disassembla il messaggio in arrivo associando i valori e lo schema impostati in questa sezione con i valori per ST01 e GS02 nel messaggio in arrivo e con lo schema del messaggio in arrivo. Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Versione** | Versione X12 per lo schema |
| **Tipo di transazione (ST01)** | Tipo di transazione |
| **Sender Application (GS02) (Applicazione mittente (GS02))** | Applicazione mittente |
| **Schema** | Il file di schema che si desidera utilizzare |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Impostazioni di ricezione-buste

![Separatori da utilizzare nei set di transazioni per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Utilizzo ISA11** | Separatore da utilizzare in un set di transazioni: <p>- **Identificatore standard**: utilizzare un punto (.) per la notazione decimale, anziché la notazione decimale del documento in ingresso nella pipeline di ricezione EDI. <p>- **Separatore ripetizioni**: specificare il separatore per occorrenze ripetute di un elemento dati semplice o di una struttura di dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Impostazioni di ricezione-numeri di controllo

![Gestione dei duplicati dei numeri di controllo per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Proprietà | Descrizione |
|----------|-------------|
| **Non consentire duplicati di numeri di controllo interscambio** | Consente di bloccare gli interscambi duplicati. Controllare il numero di controllo interscambio (ISA13) per il numero di controllo dell'interscambio ricevuto. Se viene rilevata una corrispondenza, la pipeline di ricezione EDI non elabora l'interscambio. <p><p>Per specificare il numero di giorni per cui eseguire il controllo, immettere un valore per la proprietà **Verifica ISA13 ogni (giorni) duplicato** . |
| **Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo)** | Blocca gli interscambi con numeri di controllo di gruppo duplicati. |
| **Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni)** | Blocca gli interscambi con numeri di controllo di set di transazioni duplicati. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Impostazioni di ricezione-convalide

![Convalide per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

La riga **predefinita** Mostra le regole di convalida utilizzate per un tipo di messaggio EDI. Se si desidera definire regole diverse, selezionare ogni casella in cui si desidera impostare la regola su **true**. Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Tipo di messaggio** | Tipo di messaggio EDI |
| **EDI Validation (Convalida EDI)** | Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| **Convalida estesa** | Se il tipo di dati non è EDI, la convalida è sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min o max). |
| **Consenti zeri iniziali e finali** | Mantiene gli spazi vuoti iniziali o finali aggiuntivi. e non vengono rimossi. |
| **Rimuovi zero iniziali e finali** | Rimuovere tutti i caratteri iniziali o finali di zero e spazio. |
| **Criterio separatori finali** | Consente di generare separatori finali. <p>- **Non consentito**: impedisce delimitatori e separatori finali nell'interscambio in ingresso. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>- **Facoltativo**: accettare interscambi con o senza delimitatori e separatori finali. <p>- **Obbligatorio**: l'interscambio in ingresso deve contenere delimitatori e separatori finali. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Impostazioni di ricezione-impostazioni interne

![Impostazioni interne per i messaggi in ingresso](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Converte il formato decimale implicito nn in un valore numerico in base 10** | Converte un numero EDI specificato con il formato "nn" in un valore numerico in base 10. |
| **Crea tag XML vuoti se sono consentiti separatori finali** | Chiedere al mittente dell'interscambio di includere tag XML vuoti per i separatori finali. |
| **Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore** | Analizzare ogni set di transazioni in un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Sospendere solo le transazioni in cui la convalida ha esito negativo. |
| **Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore** | Analizzare ogni set di transazioni in un interscambio in un documento XML separato applicando la busta appropriata. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |
| **Mantieni interscambio - Sospendi set transazioni in caso di errore** | Lasciare intatto l'interscambio e creare un documento XML per l'intero interscambio in batch. Sospendere solo i set di transazioni che non superano la convalida, ma continuare a elaborare tutti gli altri set di transazioni. |
| **Mantieni interscambio - Sospendi interscambio in caso di errore** |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Send Settings (Impostazioni di invio)

Dopo aver impostato le proprietà dell'accordo, è possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in uscita inviati al partner tramite il presente contratto.

1. In **Aggiungi**, selezionare **Impostazioni di avvio**.

1. Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

   Le **impostazioni di invio** sono organizzate in queste sezioni:

   * [Identificatori](#outbound-identifiers)
   * [Acknowledgement](#outbound-acknowledgement)
   * [Schemi](#outbound-schemas)
   * [Buste](#outbound-envelopes)
   * [Numero di versione del controllo](#outbound-control-version-number)
   * [Numeri di controllo](#outbound-control-numbers)
   * [Character Sets and Separators (Set di caratteri e separatori)](#outbound-character-sets-separators)
   * [Validation](#outbound-validation)

1. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Impostazioni di invio-identificatori

![Proprietà identificatore per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **ISA1 (Qualificatore di autorizzazione)** | Valore del qualificatore di autorizzazione che si desidera utilizzare. Il valore predefinito è **00-nessuna informazione di autorizzazione presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA2** . |
| **ISA2** | Valore delle informazioni di autorizzazione da utilizzare quando la proprietà **ISA1** non è **00-nessuna informazione di autorizzazione presente**. Il valore di questa proprietà deve contenere un minimo di un carattere alfanumerico e un massimo di 10. |
| **Qualificatore di sicurezza (ISA3)** | Valore del qualificatore di sicurezza che si desidera utilizzare. Il valore predefinito è **00-nessuna informazione di sicurezza presente**. <p>**Nota**: se si selezionano altri valori, specificare un valore per la proprietà **ISA4** . |
| **ISA4** | Valore delle informazioni di sicurezza da utilizzare quando la proprietà **ISA3** non è **00-nessuna informazione di sicurezza presente**. Il valore di questa proprietà deve contenere un minimo di un carattere alfanumerico e un massimo di 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Impostazioni di invio-riconoscimento

![Proprietà di riconoscimento per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Previsto TA1** | Restituisce un riconoscimento tecnico (TA1) al mittente dell'interscambio. <p>Questa impostazione specifica che il partner host, che invia il messaggio, richiede un riconoscimento dal partner Guest nel contratto. Questi riconoscimenti sono previsti dal partner host in base alle impostazioni di ricezione del contratto. |
| **Previsto FA** | Restituisce un riconoscimento funzionale (FA) al mittente dell'interscambio. Per la proprietà della **versione fa** , in base alla versione dello schema, selezionare i riconoscimenti 997 o 999. <p>Questa impostazione specifica che il partner host, che invia il messaggio, richiede un riconoscimento dal partner Guest nel contratto. Questi riconoscimenti sono previsti dal partner host in base alle impostazioni di ricezione del contratto. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Impostazioni di invio-schemi

![Schemi per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) per ogni tipo di transazione (ST01). Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Versione** | Versione X12 per lo schema |
| **Tipo di transazione (ST01)** | Tipo di transazione per lo schema |
| **Schema** | Il file di schema che si desidera utilizzare. Se si seleziona lo schema per primo, la versione e il tipo di transazione vengono impostati automaticamente. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Impostazioni di invio-buste

![Separatori in un set di transazioni da usare per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Utilizzo ISA11** | Separatore da utilizzare in un set di transazioni: <p>- **Identificatore standard**: utilizzare un punto (.) per la notazione decimale, anziché la notazione decimale del documento in uscita nella pipeline di trasmissione EDI. <p>- **Separatore ripetizioni**: specificare il separatore per occorrenze ripetute di un elemento dati semplice o di una struttura di dati ripetuta. Ad esempio, in genere l'accento circonflesso (^) viene usato come separatore ripetizioni. Negli schemi HIPAA è possibile usare solo l'accento circonflesso. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Impostazioni di invio-numero di versione del controllo

![Numero di versione del controllo per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Per questa sezione, selezionare uno [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) dall' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) per ogni interscambio. Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Numero versione controllo (ISA12)** | Versione dello standard X12 |
| **Indicatore di utilizzo (ISA15)** | Contesto di un interscambio, che può essere costituito da dati di **test** , dati di **informazioni** o dati di **produzione** . |
| **Schema** | Schema da utilizzare per la generazione dei segmenti GS e ST per un interscambio con codifica X12 inviato alla pipeline di trasmissione EDI. |
| **GS1** | Facoltativo, selezionare il codice funzionale. |
| **GS2** | Facoltativo, specificare il mittente dell'applicazione. |
| **GS3** | Facoltativo, specificare il ricevitore dell'applicazione. |
| **GS4** | Facoltativo, selezionare **CCYYMMDD** o **AAMMGG**. |
| **GS5** | Facoltativo, selezionare **HHMM**, **HHMMSS**o **HHMMSSdd**. |
| **GS7** | Facoltativo, selezionare un valore per l'agenzia responsabile. |
| **GS8** | Facoltativo, specificare la versione del documento dello schema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Impostazioni di invio-numeri di controllo

![Numeri di controllo per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Proprietà | Descrizione |
|----------|-------------|
| **Numero di controllo interscambio (ISA13)** | Intervallo di valori per il numero di controllo interscambio, che può avere un valore minimo di 1 e un valore massimo di 999999999 |
| **Numero di controllo gruppo (GS06)** | Intervallo di valori per il numero di controllo gruppo, che può avere un valore minimo di 1 e un valore massimo di 999999999 |
| **Numero di controllo set transazioni (ST02)** | Intervallo di valori per il numero di controllo del set di transazioni, che può avere un valore minimo di 1 e un valore massimo di 999999999 <p>- **Prefix**: facoltativo, valore alfanumerico <br>- **Suffisso**: facoltativo, valore alfanumerico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Impostazioni di invio-set di caratteri e separatori

![Delimitatori per i tipi di messaggio nei messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

La riga **predefinita** Mostra il set di caratteri utilizzato come delimitatori per uno schema del messaggio. Se non si desidera utilizzare il set di caratteri **predefinito** , è possibile immettere un diverso set di delimitatori per ogni tipo di messaggio. Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

> [!TIP]
> Per fornire i valori dei caratteri speciali, modificare il contratto come JSON e specificare il valore ASCII per il carattere speciale.

| Proprietà | Descrizione |
|----------|-------------|
| **Set di caratteri da usare** | Il set di caratteri X12, che è di **base**, **esteso**o **UTF8**. |
| **Schema** | Schema che si desidera utilizzare. Dopo aver selezionato lo schema, selezionare il set di caratteri che si vuole usare, in base alle descrizioni dei separatori seguenti. |
| **Tipo di input** | Tipo di input per il set di caratteri |
| **Component Separator** | Singolo carattere che separa gli elementi dati compositi |
| **Data Element Separator** | Un singolo carattere che separa gli elementi dati semplici all'interno di dati compositi |
| **Separatore caratteri di sostituzione** | Carattere sostitutivo che sostituisce tutti i caratteri separatori nei dati del payload durante la generazione del messaggio X12 in uscita |
| **Segment Terminator** | Singolo carattere che indica la fine di un segmento EDI |
| **Suffisso** | Carattere da utilizzare con l'identificatore del segmento. Se si specifica un suffisso, l'elemento dati del carattere di terminazione segmento può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario specificare un suffisso. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Impostazioni di invio-convalida

![Proprietà di convalida per i messaggi in uscita](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

La riga **predefinita** Mostra le regole di convalida utilizzate per un tipo di messaggio EDI. Se si desidera definire regole diverse, selezionare ogni casella in cui si desidera impostare la regola su **true**. Una volta completata ogni riga, viene visualizzata automaticamente una nuova riga vuota.

| Proprietà | Descrizione |
|----------|-------------|
| **Tipo di messaggio** | Tipo di messaggio EDI |
| **EDI Validation (Convalida EDI)** | Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| **Convalida estesa** | Se il tipo di dati non è EDI, la convalida è sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min o max). |
| **Consenti zeri iniziali e finali** | Mantiene gli spazi vuoti iniziali o finali aggiuntivi. e non vengono rimossi. |
| **Rimuovi zero iniziali e finali** | Rimuovere tutti i caratteri iniziali o finali di zero e spazio. |
| **Criterio separatori finali** | Consente di generare separatori finali. <p>- **Non consentito**: impedisce delimitatori e separatori finali nell'interscambio in uscita. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>- **Facoltativo**: inviare interscambi con o senza delimitatori e separatori finali. <p>- **Obbligatoria**: è necessario che l'interscambio in uscita includa delimitatori e separatori finali. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Schemi HIPAA e tipi di messaggio

Quando si utilizzano gli schemi HIPAA e i tipi di messaggio 277 o 837, è necessario eseguire alcuni passaggi aggiuntivi. I [numeri di versione del documento (GS8)](#outbound-control-version-number) per questi tipi di messaggio contengono più di 9 caratteri, ad esempio "005010X222A1". Inoltre, alcuni numeri di versione del documento vengono mappati ai tipi di messaggio Variant. Se non si fa riferimento al tipo di messaggio corretto nello schema e nel contratto, viene ricevuto questo messaggio di errore:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

In questa tabella sono elencati i tipi di messaggio interessati, le varianti e i numeri di versione del documento mappati a tali tipi di messaggi:

| Tipo di messaggio o variante |  Descrizione | Numero di versione del documento (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Notifica sullo stato delle informazioni di assistenza sanitaria | 005010X212 |
| 837_I | Health Care Claim dentale | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Health Care Claim istituzionale | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Health Care Claim Professional | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

È anche necessario disabilitare la convalida EDI quando si usano questi numeri di versione del documento perché generano un errore che indica che la lunghezza del carattere non è valida.

Per specificare i numeri di versione e i tipi di messaggio del documento, attenersi alla seguente procedura:

1. Nello schema HIPAA sostituire il tipo di messaggio corrente con il tipo di messaggio Variant per il numero di versione del documento che si desidera utilizzare.

   Si supponga, ad esempio, di voler utilizzare il numero `005010X222A1` di versione `837` del documento con il tipo di messaggio. Nello schema sostituire ogni `"X12_00501_837"` valore con il `"X12_00501_837_P"` valore.

   Per aggiornare lo schema, attenersi alla procedura seguente:

   1. Nella portale di Azure passare all'account di integrazione. Trovare e scaricare lo schema. Sostituire il tipo di messaggio e rinominare il file di schema e caricare lo schema modificato nell'account di integrazione. Per ulteriori informazioni, vedere [Edit schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Nelle impostazioni del messaggio del contratto, selezionare lo schema modificato.

1. Nell' `schemaReferences` oggetto del contratto aggiungere un'altra voce che specifichi il tipo di messaggio VARIANT corrispondente al numero di versione del documento.

   Si supponga, ad esempio, di voler utilizzare il numero `005010X222A1` di versione `837` del documento per il tipo di messaggio. Il contratto include una `schemaReferences` sezione con le proprietà e i valori seguenti:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   In questa `schemaReferences` sezione aggiungere un'altra voce con i valori seguenti:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Al termine, la sezione ha `schemaReferences` un aspetto simile al seguente:

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

1. Nelle impostazioni del messaggio del contratto disabilitare la convalida EDI deselezionando la casella di controllo **convalida EDI** per ogni tipo di messaggio o per tutti i tipi di messaggio se si utilizzano i valori **predefiniti** .

   ![Disabilitare la convalida per tutti i tipi di messaggio o ogni tipo di messaggio](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altri dettagli tecnici su questo connettore, ad esempio le azioni e i limiti descritti dal file spavalderia del connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/x12/).

> [!NOTE]
> Per le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), questa versione con etichetta ISE del connettore usa i [limiti dei messaggi B2B per ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli altri [connettori per le app per la logica](../connectors/apis-list.md)