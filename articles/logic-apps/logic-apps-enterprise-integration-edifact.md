---
title: Messaggi EDIFACT per l'integrazione aziendale B2B - App per la logica di Azure | Microsoft Docs
description: Scambiare messaggi EDIFACT in formato EDI per l'integrazione aziendale B2B con App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/26/2016
ms.author: LADocs; jonfan
ms.openlocfilehash: fc9a0068de5f9464133eec0b043fbba1dc0fbde7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="exchange-edifact-messages-for-enterprise-integration-with-logic-apps"></a>Scambiare messaggi EDIFACT per l'integrazione Enterprise con le app per la logica

Per poter scambiare messaggi EDIFACT con App per la logica di Azure, è necessario creare un contratto EDIFACT e archiviarlo nell'account di integrazione. Di seguito viene illustrata la procedura per la creazione di un contratto EDIFACT.

> [!NOTE]
> Questa pagina illustra le funzionalità EDIFACT di App per la logica di Azure. Per altre informazioni, vedere [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) già definito e associato alla sottoscrizione di Azure.  
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.

> [!NOTE]
> Quando si crea un contratto, il contenuto dei messaggi che verranno ricevuti e inviati da e verso il partner deve corrispondere al tipo di contratto.

Dopo aver [creato un account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [aggiunto i partner](logic-apps-enterprise-integration-partners.md), è possibile creare un contratto EDIFACT attenendosi alla procedura seguente.

## <a name="create-an-edifact-agreement"></a>Creare un contratto EDIFACT 

1.  Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure"). Fare clic su **Altri servizi** nel menu a sinistra.

    > [!TIP]
    > Se **Altri servizi** non viene visualizzato, potrebbe essere necessario espandere il menu. Nella parte superiore del menu compresso, selezionare **Visualizza menu**.

    ![Fare clic su "Altri servizi" nel menu a sinistra](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

2. Nella casella di ricerca, digitare "integrazione" come filtro. Nell'elenco dei risultati selezionare **Account di integrazione**.

    ![Filtro su "integrazione", selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-edifact/edifact-1-3.png)

3. Nel pannello **Account di integrazione** visualizzato selezionare l'account di integrazione nel quale creare il contratto.
Se non viene visualizzato alcun account di integrazione, [crearne prima uno](../logic-apps/logic-apps-enterprise-integration-accounts.md "Tutte le informazioni sugli account di integrazione").  

    ![Selezionare l'account di integrazione in cui si vuole creare il contratto](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Selezionare il riquadro **Accordi**. Se non è presente il riquadro dei contratti, aggiungerlo.   

    ![Selezionare il riquadro "Contratti"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Nel pannello Contratti visualizzato, selezionare **Aggiungi**.

    ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. In **Aggiungi**, digitare un **nome** per il contratto. In **Tipo di contratto**selezionare **EDIFACT**. Selezionare il **Partner host**, l'**Identità host**, il **Partner guest**, e l'**Identità guest** per il contratto.

    ![Fornire i dettagli relativi al contratto](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

    | Proprietà | Descrizione |
    | --- | --- |
    | Nome |Nome del contratto |
    | Tipo di contratto | Deve essere EDIFACT |
    | Host Partner (Partner host) |Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
    | Host Identity (Identità host) |Un identificatore per il partner host |
    | Guest Partner (Partner guest) |Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
    | identità guest |Un identificatore per il partner guest |
    | Receive Settings (Impostazioni di ricezione) |Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto. |
    | Send Settings (Impostazioni di invio) |Queste proprietà si applicano a tutti i messaggi inviati da un contratto. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurare il modo in cui il contratto riceve i messaggi

Dopo aver impostato le proprietà del contratto, è possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in arrivo ricevuti dal partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di ricezione**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

    Il controllo **Impostazioni di ricezione** è suddiviso nelle sezioni seguenti: Identificatori, Riconoscimento, Schemi, Numeri di controllo, Convalide e Impostazioni interne.

    ![Configurare "Impostazioni di ricezione"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in arrivo conformi alle impostazioni selezionate.

### <a name="identifiers"></a>Identificatori

| Proprietà | Descrizione |
| --- | --- |
| UNB6.1 (password di riferimento destinatario) |Immettere un valore alfanumerico costituito da un numero di caratteri compreso tra 1 e 14. |
| UNB6.2 (qualificatore di riferimento destinatario) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo due. |

### <a name="acknowledgments"></a>Ringraziamenti

| Proprietà | Descrizione |
| --- | --- |
| Conferma recapito messaggi (CONTRL) |Selezionare questa casella di controllo per restituire un acknowledgment tecnico (CONTRL) al mittente dell'interscambio. L'acknowledgment viene inviato al mittente dell'interscambio in base alle impostazioni di invio del contratto. |
| Acknowledgement (CONTRL) |Selezionare questa casella di controllo per restituire un acknowledgment funzionale (CONTRL) al mittente dell'interscambio. L'acknowledgment viene inviato al mittente dell'interscambio in base alle impostazioni di invio del contratto. |

### <a name="schemas"></a>Schemi

| Proprietà | Descrizione |
| --- | --- |
| UNH2.1 (tipo) |Selezionare un tipo di set di transazioni. |
| UNH2.2 (versione) |Immettere il numero di versione del messaggio (da un minimo di un carattere a un massimo di tre). |
| UNH2.3 (versione) |Immettere il numero di rilascio del messaggio (da un minimo di un carattere a un massimo di tre). |
| UNH2.5 (codice assegnato associazione) |Immettere il codice assegnato (deve contenere al massimo sei caratteri, che devono essere alfanumerici). |
| UNH2.1 (ID mittente APP) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35. |
| UNH2.2 (qualificatore codice mittente APP) |Immettere un valore alfanumerico con lunghezza massima di quattro caratteri. |
| SCHEMA |Selezionare lo schema caricato in precedenza che si vuole usare dall'account di integrazione associato. |

### <a name="control-numbers"></a>Numeri di controllo
| Proprietà | Descrizione |
| --- | --- |
| Disallow Interchange Control Number duplicates (Non consentire duplicati di numeri di controllo interscambio) |Selezionare questa casella di controllo per bloccare gli interscambi duplicati. Se questa opzione è selezionata, l'azione di decodifica EDIFACT verifica che il numero di controllo (UNB5) dell'interscambio ricevuto non corrisponda al numero di controllo di un interscambio elaborato in precedenza. Se viene rilevata una corrispondenza, l'interscambio non viene elaborato. |
| Verifica UNB5 duplicati ogni (giorni) |Se si è scelto di non consentire numeri di controllo di interscambio duplicati, è possibile il numero di giorni in cui verrà eseguito il controllo assegnando il valore appropriato a questa impostazione. |
| Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo) |Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di gruppo duplicati (UNG5). |
| Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni) |Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di set di transazioni duplicati (UNH1). |
| Numero di controllo acknowledgement EDIFACT |Per designare i numeri di riferimento di set di transazioni da usare in un riconoscimento, immettere un valore per il prefisso, un intervallo di numeri di riferimento e un suffisso. |

### <a name="validations"></a>Convalide

Dopo aver completato ogni riga di convalida, ne viene aggiunta automaticamente un'altra. Se non si specifica alcuna regola, la convalida usa la riga predefinita.

| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Selezionare il tipo di messaggio EDI. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono mantenuti e non vengono rimossi. |
| Rimuovi zero iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono rimossi. |
| Criterio separatori finali |Consente di generare separatori finali. <p>Selezionare **Non consentiti** per non consentire delimitatori e separatori finali nell'interscambio ricevuto. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>Selezionare **Facoltativi** per accettare interscambi con o senza delimitatori e separatori finali. <p>Selezionare **Obbligatori** se l'interscambio ricevuto deve contenere delimitatori e separatori finali. |

### <a name="internal-settings"></a>Impostazioni interne

| Proprietà | Descrizione |
| --- | --- |
| Crea tag XML vuoti se sono consentiti separatori finali |Selezionare questa casella di controllo se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali. |
| Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Sospende solo i set di transazioni che non superano la convalida. |
| Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore|Consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. | 
| Mantieni interscambio - Sospendi set transazioni in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende solo i set di transazioni che non superano la convalida, pur continuando a elaborare tutti gli altri set di transazioni. |
| Mantieni interscambio - Sospendi interscambio in caso di errore |Mantiene l'interscambio intatto, crea un documento XML per l'intero interscambio in batch. Sospende tutto l'interscambio quando la convalida di uno o più set di transazioni dell'interscambio non riesce. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurare il modo in cui il contratto invia messaggi

È possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in uscita inviati ai partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di avvio**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere le tabelle in questa sezione.

    Il controllo **Impostazioni di invio** è suddiviso nelle sezioni seguenti: Identificatori, Riconoscimento, Schemi, Buste, Set di caratteri e separatori, Numeri di controllo e Convalida.

    ![Configurare "Impostazioni di invio"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in uscita conformi alle impostazioni selezionate.

### <a name="identifiers"></a>Identificatori

| Proprietà | Descrizione |
| --- | --- |
| UNB1.2 (versione sintassi) |Selezionare un valore compreso tra **1** e **4**. |
| UNB2.3 (indirizzo di routing inverso mittente) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14. |
| UNB3.3 (indirizzo di routing inverso destinatario) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14. |
| UNB6.1 (password di riferimento destinatario) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14. |
| UNB6.2 (qualificatore di riferimento destinatario) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo due. |
| UNB7 (ID riferimento applicazione) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14. |

### <a name="acknowledgment"></a>Acknowledgment (Riconoscimento)
| Proprietà | Descrizione |
| --- | --- |
| Conferma recapito messaggi (CONTRL) |Selezionare questa casella di controllo se il partner host prevede di ricevere un riconoscimento tecnico (CONTRL). Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest. |
| Acknowledgement (CONTRL) |Selezionare questa casella di controllo se il partner host prevede di ricevere un acknowledgment funzionale (CONTRL). Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest. |
| Genera il ciclo SG1/SG4 per i set di transazioni accettate |Se si è scelto di richiedere un acknowledgement funzionale, selezionare questa casella di controllo per forzare la generazione di cicli SG1/SG4 negli acknowledgement funzionali CONTRL per i set di transazioni accettati. |

### <a name="schemas"></a>Schemi
| Proprietà | Descrizione |
| --- | --- |
| UNH2.1 (tipo) |Selezionare un tipo di set di transazioni. |
| UNH2.2 (versione) |Immettere il numero di versione del messaggio. |
| UNH2.3 (versione) |Immettere il numero di rilascio del messaggio. |
| SCHEMA |Selezionare lo schema da usare. Gli schemi si trovano nell'account di integrazione. Per accedere agli schemi, collegare l'account di integrazione alla app per la logica. |

### <a name="envelopes"></a>Buste
| Proprietà | Descrizione |
| --- | --- |
| UNB8 (codice di priorità elaborazione) |Immettere un valore alfabetico di lunghezza non superiore a un carattere. |
| UNB10 (contratto comunicazioni) |Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 40. |
| UNB11 (indicatore test) |Selezionare questa casella di controllo per indicare che nell'interscambio vengono generati dati di test. |
| Applica segmento UNA (avviso stringa servizio) |Selezionare questa casella di controllo per generare un segmento UNA per l'interscambio da inviare. |
| Applica segmenti UNG (intestazione gruppo funzionale) |Selezionare questa casella di controllo per creare segmenti di raggruppamento nell'intestazione di gruppo funzionale nei messaggi inviati al partner guest. Per creare i segmenti UNG vengono usati i valori seguenti: <p>Per **UNG1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo sei. <p>Per **UNG2.1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35. <p>Per **UNG2.2** immettere un valore alfanumerico con lunghezza massima di quattro caratteri. <p>Per **UNG3.1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35. <p>Per **UNG3.2** immettere un valore alfanumerico con lunghezza massima di quattro caratteri. <p>Per **UNG6** immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre. <p>Per **UNG7.1** immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre. <p>Per **UNG7.2** immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre. <p>Per **UNG7.3** immettere un valore alfanumerico costituito da almeno 1 carattere e al massimo 6. <p>Per **UNG8**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14. |

### <a name="character-sets-and-separators"></a>Character Sets and Separators (Set di caratteri e separatori)

Oltre al set di caratteri, è possibile immettere un diverso set di delimitatori da usare per ogni tipo di messaggio. Se per un determinato schema del messaggio non è specificato un set di caratteri, viene usato il set di caratteri predefinito.

| Proprietà | Descrizione |
| --- | --- |
| UNB1.1 (identificatore di sistema) |Selezionare il set di caratteri EDIFACT da applicare all'interscambio in uscita. |
| SCHEMA |Selezionare uno schema nell'elenco a discesa. Dopo aver completato ogni riga, viene aggiunta automaticamente una nuova riga. Per lo schema selezionato, selezionare il set di separatori che si vuole usare, in base alle seguenti descrizioni di separatore. |
| Tipo di input |Selezionare un tipo di input nell'elenco a discesa. |
| Component Separator |Per separare elementi di dati compositi immettere un singolo carattere. |
| Data Element Separator |Per separare elementi di dati semplici in elementi di dati compositi Immettere un singolo carattere. |
| Segment Terminator |Per indicare la fine di un segmento EDI, immettere un singolo carattere. |
| Suffisso |Selezionare il carattere usato con l'identificatore di segmento. Se si designa un suffisso, l'elemento dati del terminatore di segmenti può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario designare un suffisso. |

### <a name="control-numbers"></a>Numeri di controllo
| Proprietà | Descrizione |
| --- | --- |
| UNB5 (numero di controllo interscambio) |Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare un interscambio in uscita. Il prefisso e il suffisso sono facoltativi, mentre il numero di controllo è obbligatorio. Il numero di controllo viene incrementato per ogni nuovo messaggio, mentre il prefisso e il suffisso rimangono invariati. |
| UNG5 (numero di controllo gruppo) |Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare il numero di controllo di gruppo. Il prefisso e il suffisso sono facoltativi, mentre il numero di controllo è obbligatorio. Il numero di controllo viene incrementato per ogni nuovo messaggio fino al raggiungimento del valore massimo, mentre il prefisso e il suffisso rimangono invariati. |
| UNH1 (numero di riferimento intestazione messaggio) |Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare il numero di riferimento dell'intestazione del messaggio. Il prefisso e il suffisso sono facoltativi, mentre il numero di riferimento è obbligatorio. Il numero di riferimento viene incrementato per ogni nuovo messaggio, mentre il prefisso e il suffisso rimangono invariati. |

### <a name="validations"></a>Convalide

Dopo aver completato ogni riga di convalida, ne viene aggiunta automaticamente un'altra. Se non si specifica alcuna regola, la convalida usa la riga predefinita.

| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Selezionare il tipo di messaggio EDI. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Tutti gli zero iniziali e finali e i caratteri di spazio vengono mantenuti e non vengono rimossi. |
| Rimuovi zero iniziali e finali |Tutti gli zero iniziali o finali vengono rimossi. |
| Criterio separatori finali |Consente di generare separatori finali. <p>Selezionare **Non consentiti** per non consentire delimitatori e separatori finali nell'interscambio inviato. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. <p>Selezionare **Facoltativi** per inviare interscambi con o senza delimitatori e separatori finali. <p>Selezionare **Obbligatori** se l'interscambio inviato deve contenere delimitatori e separatori finali. |

## <a name="find-your-created-agreement"></a>Individuare il contratto creato

1.  Dopo aver impostato tutte le proprietà del contratto, scegliere **OK** nel pannello **Aggiungi** per completare la creazione del contratto e tornare al pannello dell'account di integrazione.

    Il contratto appena aggiunto viene visualizzato nell'elenco **Contratti**.

2.  È anche possibile visualizzare i contratti nella panoramica dell'account di Integrazione. Nel pannello dell'account di integrazione, selezionare **Panoramica**, quindi selezionare il riquadro **Contratti**. 

    ![Scegliere il riquadro Contratti per visualizzare tutti i contratti](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Visualizzare il file Swagger
Per visualizzare i dettagli di Swagger per il connettore EDIFACT, vedere [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  

