---
title: Panoramica di X12 ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i contratti X12 per creare app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 0d4e716242ce2474e0d8097cfbaa4bb546004ed9


---
# <a name="enterprise-integration-with-x12"></a>Enterprise integration con X12
> [!NOTE]
> Questa pagina illustra le funzionalità X12 delle app per la logica. Per informazioni su EDIFACT, fare clic [qui](../logic-apps/logic-apps-enterprise-integration-edifact.md).
> 
> 

## <a name="create-an-x12-agreement"></a>Creare un contratto X12
Prima di poter scambiare messaggi X12, è necessario creare un contratto X12 e archiviarlo nell'account di integrazione. Attenersi alla procedura seguente per creare un contratto X12.

### <a name="heres-what-you-need-before-you-get-started"></a>Di seguito sono riportati i prerequisiti necessari per iniziare
* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure  
* Almeno due [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione  

> [!NOTE]
> Quando si crea un contratto, il contenuto del file del contratto deve corrispondere al tipo di contratto.    
> 
> 

Dopo aver [creato un account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) e aver [aggiunto i partner](../logic-apps/logic-apps-enterprise-integration-partners.md), è possibile creare un contratto X12 seguendo questa procedura:  

### <a name="from-the-azure-portal-home-page"></a>Nella home page del portale di Azure
Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "portale di Azure"):  

1. Selezionare **Esplora** nel menu a sinistra.  

> [!TIP]
> Se non viene visualizzato il collegamento **Esplora** , potrebbe essere prima necessario espandere il menu. A tale scopo, selezionare il collegamento **Visualizza menu** nella parte superiore sinistra del menu compresso.  
> 
> 

![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    

1. Digitare *integrazione* nella casella di ricerca Filtro e quindi selezionare **Account di integrazione** nell'elenco dei risultati.       
   ![](./media/logic-apps-enterprise-integration-x12/x12-1-3.png)    
2. Nel pannello **Account di integrazione** visualizzato selezionare l'account di integrazione in cui verrà creato il contratto. Se non viene visualizzato alcun account di integrazione, [crearne prima uno](../logic-apps/logic-apps-enterprise-integration-accounts.md "All about integration accounts").  
   ![](./media/logic-apps-enterprise-integration-x12/x12-1-4.png)  
3. Selezionare il riquadro **Accordi** . Se il riquadro Accordi non viene visualizzato, aggiungerlo.   
   ![](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)     
4. Selezionare il pulsante **Aggiungi** nel pannello Accordi visualizzato.  
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. Nel pannello Accordi visualizzato immettere un **nome** per il contratto e quindi selezionare **Tipo di contratto**, **Partner host**, **Identità host**, **Partner guest** e **Identità guest**.  
   ![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  
6. Dopo aver impostato le proprietà delle impostazioni di ricezione, selezionare il pulsante **OK**  
   Per continuare:  
7. Selezionare **Impostazioni di ricezione** per configurare la gestione dei messaggi ricevuti tramite questo contratto.  
8. Il controllo delle impostazioni di ricezione è suddiviso nelle sezioni seguenti, inclusi Identificatori, Acknowledgment (Riconoscimento), Schemi, Buste, Numeri di controllo, Convalide e Impostazioni interne. Configurare queste proprietà in base al contratto con il partner con cui si scambieranno i messaggi. Ecco una visualizzazione di questi controlli. Configurarli in base a come si vuole che il contratto identifichi e gestisca i messaggi in ingresso:  
   ![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

![](./media/logic-apps-enterprise-integration-x12/x12-3.png)  

1. Selezionare il pulsante **OK** per salvare le impostazioni.  

### <a name="identifiers"></a>Identificatori
| Proprietà | Descrizione |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |Facoltativo. Immettere il valore relativo alle informazioni di autorizzazione. Se il valore immesso per ISA1 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| Qualificatore di sicurezza (ISA3) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |Facoltativo. Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA3 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgments"></a>Ringraziamenti
| Proprietà | Descrizione |
| --- | --- |
| TA1 expected (Previsto TA1) |Selezionare questa casella di controllo per restituire un riconoscimento tecnico (TA1) al mittente dell'interscambio. I riconoscimenti vengono inviati al mittente dell'interscambio in base alle impostazioni di invio definite per il contratto. |
| FA expected (Previsto FA) |Selezionare questa casella di controllo per restituire un riconoscimento funzionale (FA) al mittente dell'interscambio. Specificare quindi se si vuole il riconoscimento 997 o 999, in base alle versioni dello schema in uso. I riconoscimenti vengono inviati al mittente dell'interscambio in base alle impostazioni di invio definite per il contratto. |
| Include AK2/IK2 Loop (Includi ciclo AK2/IK2) |Selezionare questa casella di controllo per abilitare la generazione di cicli AK2 nei riconoscimenti funzionali per i set di transazioni accettati. Nota: questa casella di controllo è abilitata solo è stata selezionata la casella di controllo FA expected (Previsto FA). |

### <a name="schemas"></a>Schemi
Scegliere uno schema per ogni tipo di transazione (ST1) e di applicazione mittente (GS2). Il messaggio in arrivo viene disassemblato dalla pipeline di ricezione mediante associazione dei valori ST1 e GS2 del messaggio a quelli definiti in questa pagina e dello schema del messaggio in arrivo a quello definito in questa pagina.

| Proprietà | Descrizione |
| --- | --- |
| Versione |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Sender Application (GS02) (Applicazione mittente (GS02)) |Selezionare l'applicazione del mittente |
| Schema |Selezionare il file di schema che si vuole usare. I file di schema si trovano nell'account di integrazione. |

### <a name="envelopes"></a>Buste
| Proprietà | Descrizione |
| --- | --- |
| Utilizzo ISA11 |Usare questo campo per specificare il separatore in un set di transazioni:</br></br>Selezionare l'identificatore Standard per usare la notazione decimale "." al posto della notazione decimale del documento in ingresso nella pipeline di ricezione EDI.</br></br>Selezionare il separatore ripetizioni per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Il carattere (^) è ad esempio usato in genere come separatore di ripetizioni. Per gli schemi HIPAA, è possibile usare solo (^). |

### <a name="control-numbers"></a>Numeri di controllo
| Proprietà | Descrizione |
| --- | --- |
| Disallow Interchange Control Number duplicates (Non consentire duplicati di numeri di controllo interscambio) |Selezionare questa opzione per bloccare gli interscambi duplicati. Se questa opzione è selezionata, il portale dei servizi BizTalk verifica che il numero di controllo (ISA13) dell'interscambio ricevuto non corrisponda al numero di controllo di interscambio. Se viene rilevata una corrispondenza, la pipeline di ricezione non elabora l'interscambio.<br/>Se si è scelto di non consentire numeri di controllo di interscambio duplicati, è possibile specificare l'intervallo di giorni con cui verrà eseguito il controllo assegnando il valore appropriato all'opzione Verifica ISA13 duplicati ogni (giorni). |
| Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo) |Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di gruppo duplicati. |
| Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni) |Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di set di transazioni duplicati. |

### <a name="validations"></a>Convalide
| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Tipo di messaggio EDI, ad esempio 850-Ordine d'acquisto o 999-Riconoscimento implementazione. |
| EDI Validation (Convalida EDI) |Esegue la convalida EDI sui tipi di dati secondo quanto definito dalle proprietà EDI dello schema, le restrizioni di lunghezza, gli elementi dati vuoti e i separatori finali. |
| Convalida estesa |Se il tipo di dati non è EDI, la convalida viene eseguita sul requisito dell'elemento dati e sulla ripetizione consentita, nonché sulle enumerazioni e sulla convalida della lunghezza dell'elemento dati (min/max). |
| Consenti zeri iniziali e finali |Eventuali spazi e zeri aggiuntivi iniziali o finali vengono mantenuti e non vengono rimossi. |
| Criterio separatori finali |Genera separatori finali nell'interscambio ricevuto. Le opzioni includono Non consentiti, Facoltativi e Obbligatori. |

### <a name="internal-settings"></a>Impostazioni interne
| Proprietà | Descrizione |
| --- | --- |
| Converti formato decimale implicito Nn in valore numerico in base 10 |Converte un numero EDI specificato nel formato Nn in un valore numerico in base&10; nell'XML intermedio del Portale dei servizi BizTalk. |
| Crea tag XML vuoti se sono consentiti separatori finali |Selezionare questa casella di controllo se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali. |
| Inbound batching processing (Elaborazione batch in ingresso) |Suddividi interscambio in set di transazioni - sospendi set di transazioni in caso di errore: consente di analizzare ogni set di transazioni in un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Con questa opzione, se la convalida di uno o più set di transazioni non riesce, Servizi BizTalk sospende solo i set di transazioni interessati. </br></br>Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore: consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, Servizi BizTalk sospende l'intero interscambio.</br></br>Mantieni interscambio - Sospendi set transazioni in caso di errore: lascia intatto l'interscambio, creando un documento XML per l'intero interscambio batch. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, Servizi BizTalk sospende solo i set di transazioni interessati e continua a elaborare tutti gli altri.</br></br>Mantieni interscambio - Sospendi interscambio in caso di errore: lascia intatto l'interscambio, creando un documento XML per l'intero interscambio batch. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, Servizi BizTalk sospende l'intero interscambio.</br></br> |

Il contratto è pronto per gestire i messaggi in ingresso conformi allo schema selezionato.

Per configurare le impostazioni che gestiscono i messaggi inviati ai partner:  

1. Selezionare **Impostazioni di invio** per configurare la gestione dei messaggi inviati tramite questo contratto.  

Il controllo Impostazioni di invio è suddiviso nelle sezioni Identificatori, Acknowledgment, Schemi, Buste, Numeri di controllo, Set di caratteri e separatori, Numeri di controllo e Convalida. 

Ecco una visualizzazione di questi controlli. Effettuare le selezioni in base a come si vogliono gestire i messaggi inviati ai partner tramite il contratto:    
![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

![](./media/logic-apps-enterprise-integration-x12/x12-6.png)  

1. Selezionare il pulsante **OK** per salvare le impostazioni.  

### <a name="identifiers"></a>Identificatori
| Proprietà | Descrizione |
| --- | --- |
| ISA1 (Qualificatore di autorizzazione) |Selezionare il valore relativo al qualificatore di autorizzazione nell'elenco a discesa. |
| ISA2 |Immettere il valore relativo alle informazioni di autorizzazione. Se questo valore è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |
| ISA3 (Qualificatore di sicurezza) |Selezionare il valore relativo al qualificatore di sicurezza nell'elenco a discesa. |
| ISA4 |Immettere il valore relativo alle informazioni di sicurezza. Se il valore immesso per ISA4 è diverso da 00, immettere almeno uno e al massimo 10 caratteri alfanumerici. |

### <a name="acknowledgment"></a>Acknowledgment (Riconoscimento)
| Proprietà | Descrizione |
| --- | --- |
| TA1 expected (Previsto TA1) |Selezionare questa casella di controllo per restituire un riconoscimento tecnico (TA1) al mittente dell'interscambio. Questa impostazione specifica che il partner host che invia il messaggio richiede un riconoscimento dal partner guest nel contratto. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| FA expected (Previsto FA) |Selezionare questa casella di controllo per restituire un riconoscimento funzionale (FA) al mittente dell'interscambio, quindi specificare se si vuole il riconoscimento 997 o 999, in base alle versioni dello schema in uso. Questi riconoscimenti sono attesi dal partner host in base alle impostazioni di ricezione del contratto. |
| Versione FA |Selezionare la versione FA |

### <a name="schemas"></a>Schemi
| Proprietà | Descrizione |
| --- | --- |
| Versione |Selezionare la versione X12 |
| Tipo di transazione (ST01) |Selezionare il tipo di transazione |
| Schema |Selezionare lo schema da usare. Gli schemi si trovano nell'account di integrazione. Per accedere agli schemi, collegare l'account di integrazione alla app per la logica. |

### <a name="envelopes"></a>Buste
| Proprietà | Descrizione |
| --- | --- |
| Utilizzo ISA11 |Usare questo campo per specificare il separatore in un set di transazioni:</br></br>Selezionare l'identificatore Standard per usare la notazione decimale "." al posto della notazione decimale del documento in ingresso nella pipeline di ricezione EDI.</br></br>Selezionare il separatore ripetizioni per specificare il separatore di occorrenze ripetute di un elemento dati semplice o di una struttura dati ripetuta. Il carattere (^) è ad esempio usato in genere come separatore di ripetizioni. Per gli schemi HIPAA, è possibile usare solo (^).</br> |
| Separatore ripetizioni |Immettere il separatore di ripetizioni |
| Numero versione controllo (ISA12) |Selezionare la versione dello standard X12 usato dal Portale dei servizi BizTalk per generare un interscambio in uscita. |
| Indicatore di utilizzo (ISA15) |Specificare se il contesto di un interscambio è di tipo informazioni (I), dati di produzione (P) o dati di test (T). La pipeline di ricezione EDI alza di livello questa proprietà nel contesto. |
| Schema |È ora possibile specificare il modo in cui il portale di Servizi BizTalk genererà i segmenti GS e ST per un interscambio con codifica X12 inviato alla pipeline di trasmissione.</br></br>È possibile associare i valori degli elementi dati GS1, GS2, GS3, GS4, GS5, GS7 e GS8 ai valori del tipo di transazione e agli elementi dati Versione/Rilascio. Quando il Portale dei servizi BizTalk determina che per un messaggio XML sono impostati i valori per il tipo di transazione e che in una riga della griglia sono presenti gli elementi relativi alla versione, gli elementi dati GS1, GS2, GS3, GS4, GS5, GS7 e GS8 vengono immessi nella busta dell'interscambio in uscita in base ai valori della stessa riga della griglia. I valori del tipo di transazione e gli elementi Versione/Rilascio devono essere univoci.</br></br>Facoltativo. Per GS1, selezionare un valore per il codice funzionale nell'elenco a discesa.</br></br>Obbligatorio. Per GS2, immettere un valore alfanumerico per il mittente dell'applicazione costituito da almeno due caratteri e al massimo 15.</br></br>Obbligatorio. Per GS3, immettere un valore alfanumerico per il ricevitore dell'applicazione costituito da almeno due caratteri e al massimo 15.</br></br>Facoltativo. Per GS4, selezionare CCYYMMDD o YYMMDD.</br></br>Facoltativo. Per GS5, selezionare HHMM, HHMMSS o HHMMSSdd.</br></br>Facoltativo. Per GS7, selezionare un valore per l'agenzia responsabile nell'elenco a discesa.</br></br>Facoltativo. Per GS8, immettere un valore alfanumerico per il documento identificato, costituito da almeno un carattere e al massimo 12.</br></br>**Nota**: questi valori vengono immessi dal portale di Servizi BizTalk nei campi GS dell'interscambio creato se il tipo di transazione e gli elementi Versione/Rilascio nella stessa riga corrispondono a quelli associati all'interscambio. |

### <a name="control-numbers"></a>Numeri di controllo
| Proprietà | Descrizione |
| --- | --- |
| Numero di controllo interscambio (ISA13) |Obbligatorio. Immettere un intervallo di valori per il numero di controllo dell'interscambio usato dal Portale dei servizi BizTalk per la generazione di un interscambio in uscita. Immettere un valore numerico compreso tra 1 e 999999999. |
| Numero di controllo gruppo (GS06) |Obbligatorio. Immettere l'intervallo di numeri da usare nel Portale dei servizi BizTalk per il numero di controllo del gruppo. Immettere un valore numerico costituito da un numero di caratteri compreso tra&1; e&9;. |
| Numero di controllo set transazioni (ST02) |Per il numero di controllo del set di transazioni (ST02), immettere un intervallo di valori numerici per i campi centrali obbligatori e di valori alfanumerici per il prefisso e il suffisso facoltativi. La lunghezza massima di tutti i quattro campi è di nove caratteri. |
| Prefisso |Per definire l'intervallo dei numeri di controllo del set di transazioni usati in un riconoscimento, immettere i valori nei campi Numero di controllo ACK (ST02). Immettere un valore numerico per i due campi centrali e, se si vuole, un valore alfanumerico per i campi relativi al prefisso e al suffisso. I campi centrali sono obbligatori e contengono i valori minimo e massimo per il numero di controllo. I campi relativi al prefisso e al suffisso sono facoltativi. La lunghezza massima di tutti i tre campi è di nove caratteri. |
| Suffisso |Per definire l'intervallo dei numeri di controllo del set di transazioni usati in un riconoscimento, immettere i valori nei campi Numero di controllo ACK (ST02). Immettere un valore numerico per i due campi centrali e, se si vuole, un valore alfanumerico per i campi relativi al prefisso e al suffisso. I campi centrali sono obbligatori e contengono i valori minimo e massimo per il numero di controllo. I campi relativi al prefisso e al suffisso sono facoltativi. La lunghezza massima di tutti i tre campi è di nove caratteri. |

### <a name="character-sets-and-separators"></a>Character Sets and Separators (Set di caratteri e separatori)
Oltre al set di caratteri, è possibile immettere un diverso set di delimitatori da usare per ogni tipo di messaggio. Se per un determinato schema del messaggio non è specificato un set di caratteri, viene usato il set di caratteri predefinito.

| Proprietà | Descrizione |
| --- | --- |
| Set di caratteri da usare |Selezionare il set di caratteri X12 per convalidare le proprietà immesse per il contratto.</br></br>**Nota**: il portale di Servizi BizTalk usa questa impostazione solo per convalidare i valori immessi nelle proprietà correlate del contratto. La pipeline di ricezione o di invio ignora questa proprietà per il set di caratteri durante l'elaborazione in fase di runtime. |
| Schema |Selezionare il simbolo (+) e uno schema nell'elenco a discesa. Selezionare i separatori da usare per lo schema selezionato:</br></br>Separatore elementi componenti: immettere un singolo carattere per separare elementi di dati compositi.</br></br>Separatore elementi dati: immettere un singolo carattere per separare elementi di dati semplici in elementi di dati compositi.</br></br></br></br>.Replacement Character (Carattere di sostituzione): selezionare questa casella di controllo se i dati di payload contengono caratteri usati anche come separatori di dati, segmenti o componenti. È quindi possibile immettere un carattere di sostituzione. Quando si genera il messaggio X12 in uscita, tutte le istanze dei caratteri separatori nei dati di payload vengono sostituite con il carattere specificato.</br></br>Carattere di terminazione segmento: immettere un singolo carattere per indicare la fine di un segmento EDI.</br></br>Suffisso: selezionare il carattere usato con l'identificatore di segmento. Se si designa un suffisso, l'elemento dati del terminatore di segmenti può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario designare un suffisso. |

### <a name="validation"></a>Convalida
| Proprietà | Descrizione |
| --- | --- |
| Tipo messaggio |Selezionando questa opzione si consente la convalida nel ricevitore dell'interscambio. Questa operazione esegue la convalida EDI degli elementi dati del set di transazioni, convalidando i tipi di dati, le restrizioni relative alla lunghezza, gli elementi dati vuoti e i separatori finali. |
| EDI Validation (Convalida EDI) | |
| Convalida estesa |Se si seleziona questa opzione, viene abilitata la convalida estesa degli interscambi ricevuti dal mittente dell'interscambio, inclusi la convalida della lunghezza dei campi, la facoltatività e il conteggio ripetizioni, oltre alla convalida dei tipi di dati XSD. È possibile abilitare la convalida dell'estensione senza abilitare la convalida EDI o viceversa. |
| Consenti zeri iniziali/finali |Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo quando l'elemento dati in un interscambio EDI non rispetta il requisito di lunghezza a causa della presenza di spazi iniziali o finali, ma lo rispetta quando questi vengono rimossi. |
| Separatore finale |Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo se un elemento dati in un interscambio EDI non rispetta il requisito di lunghezza solo a causa della presenza di zero iniziali (o finali) o di spazi finali e lo rispetta quando questi vengono rimossi.</br></br>Selezionare Non consentiti per non consentire delimitatori e separatori finali in un interscambio ricevuto dal mittente dell'interscambio. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido.</br></br>Selezionare Facoltativi per accettare interscambi con o senza delimitatori e separatori finali.</br></br>Selezionare Obbligatori se l'interscambio ricevuto deve contenere delimitatori e separatori finali. |

Dopo aver selezionato **OK** nei pannelli aperti:  

1. Selezionare il riquadro **Agreements** (Contratti) nel pannello Integration Account (Account di integrazione) e verrà visualizzato il nuovo contratto aggiunto.  
   ![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  




<!--HONumber=Jan17_HO3-->


