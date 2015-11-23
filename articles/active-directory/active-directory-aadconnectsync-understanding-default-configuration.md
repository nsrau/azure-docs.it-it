<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita | Microsoft Azure"
   description="Questo articolo descrive la configurazione predefinita nel servizio di sincronizzazione Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/10/2015"
   ms.author="andkjell"/>

# Informazioni sulla configurazione predefinita

Questo articolo illustra la configurazione predefinita del servizio di sincronizzazione Azure AD Connect. Scopo dell'articolo è spiegare con un esempio reale il funzionamento del modello di configurazione, detto provisioning dichiarativo. Nell'articolo si presuppone che l'utente abbia già installato e configurato Azure AD Sync tramite l'Installazione guidata.

## Descrizione dello scenario

Questo esempio usa una distribuzione con una foresta di account (A), una foresta di risorse (R) e una directory di Azure AD.

![scenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

In questa configurazione si presuppone di trovare un account abilitato nella foresta di account e un account disabilitato nella foresta di risorse con una cassetta postale collegata.

L'obiettivo della configurazione predefinita è il seguente:

- Le informazioni sugli attributi relativi all'account di accesso saranno sincronizzati dalla foresta con l'accesso abilitato.
- Gli attributi che si possono trovare nell'Elenco indirizzi globale saranno sincronizzati dalla foresta con la cassetta postale. Se non si trova una cassetta postale, verrà usata un'altra foresta.
- Se si trova una cassetta postale collegata, è necessario che venga trovato l'account abilitato collegato per consentire l'esportazione dell'oggetto in Azure AD.

## Editor delle regole di sincronizzazione

La configurazione può essere visualizzata e modificata usando l'editor delle regole di sincronizzazione (SRE, Synchronization Rules Editor). Nel menu Start è disponibile un collegamento.

![Editor regole di sincronizzazione](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE è uno strumento del Resource Kit e viene installato con il servizio di sincronizzazione Azure AD Connect. Per avviare lo strumento, è necessario essere membro del gruppo ADSyncAdmins. All'avvio viene visualizzata una schermata simile alla seguente:

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

In questa schermata sono riportate tutte le regole di sincronizzazione create per la configurazione in uso. Ogni riga nella tabella corrisponde a una regola di sincronizzazione. A sinistra in Tipi di regola sono elencati due tipi diversi: In ingresso e In uscita. I tipi In ingresso e In uscita derivano dalla visualizzazione dei metaverse. In questa panoramica saranno trattate principalmente le regole in ingresso. L'elenco delle regole di sincronizzazione effettivo dipenderà dallo schema rilevato in AD. Nella figura precedente la foresta di account (fabrikamonline.com) non ha alcun servizio, ad esempio Exchange e Lync, e non sono state create regole di sincronizzazione per questi servizi. Nella foresta di risorse (res.fabrikamonline.com) sono tuttavia disponibili regole di sincronizzazione per questi servizi. A seconda della versione rilevata, il contenuto delle regole sarà diverso. Ad esempio, in una distribuzione con Exchange 2013 saranno configurati più flussi di attributi rispetto a Exchange 2010 ed Exchange 2007.

## Regola di sincronizzazione

Una regola di sincronizzazione è un oggetto di configurazione con un set di attributi trasmessi in flusso quando una condizione risulta soddisfatta. Viene usata anche per descrivere il modo in cui un oggetto presente in uno spazio connettore è correlato a un oggetto del metaverse. Questa caratteristica viene chiamata join o associazione. Le regole di sincronizzazione hanno un valore di precedenza che indica come si correlano l’una all’altra. Una regola di sincronizzazione con un valore numerico di precedenza basso ha una precedenza più alta e, in caso di conflitto del flusso di attributi, la precedenza più alta avrà la priorità.

Come esempio verrà esaminata la regola di sincronizzazione **In from AD – User AccountEnabled**. Questa riga sarà contrassegnata in SRE e si sceglierà **Edit**.

Essendo una regola predefinita, verrà visualizzato un avviso quando la si apre. Poiché non è consigliabile apportare [modifiche alle regole predefinite](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), verrà chiesto come si vuole procedere. In questo caso si vuole solo visualizzare la regola, quindi scegliere **No**.

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Una regola di sincronizzazione include quattro sezioni di configurazione: descrizione, filtro per la definizione dell'ambito, regole di unione e trasformazioni.

### Descrizione

La prima sezione fornisce informazioni di base, ad esempio il nome e una descrizione.

![Modifica regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Sono inoltre disponibili informazioni sul sistema connesso a cui la regola è correlata, sul tipo di oggetto presente nel sistema connesso a cui si applica la regola e sul tipo di oggetto del metaverse. Il tipo di oggetto del metaverse è sempre una persona, indipendentemente dal fatto che il tipo di oggetto di origine sia un utente, iNetOrgPerson o un contatto. Il tipo di oggetto del metaverse deve rimanere invariato, pertanto, viene creato come tipo generico. Il tipo di collegamento può essere impostato su Join, StickyJoin o Provision. Questa impostazione interagisce con la sezione relativa alle regole di unione e verrà discussa più avanti.

Si può anche vedere che questa regola di sincronizzazione viene usata per la sincronizzazione della password. Se un utente rientra nell'ambito di questa regola di sincronizzazione, la password verrà sincronizzata da locale al cloud, presupponendo che la funzionalità di sincronizzazione della password sia stata abilitata.

### Filtro per la definizione dell'ambito

Questa sezione viene usata per configurare i tempi di applicazione di una regola di sincronizzazione. Dal momento che il nome della regola di sincronizzazione in esame indica che deve essere applicata solo per utenti abilitati, l'ambito viene configurato in modo che l'attributo AD **userAccountControl** non abbia il bit 2 impostato. Quando in AD viene trovato un utente, viene applicata questa regola di sincronizzazione se l'attributo **userAccountControl** è impostato sul valore decimale 512 (utente normale abilitato), mentre non viene applicata se l'utente trovato ha l'attributo **userAccountControl** impostato su 514 (utente normale disabilitato).

![Modifica regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Il filtro per la definizione dell'ambito contiene gruppi e clausole che possono essere nidificati. Per essere applicate, tutte le clausole all'interno di un gruppo devono essere soddisfatte per una regola di sincronizzazione. Quando si definiscono più gruppi, affinché la regola venga applicata è necessario che almeno un gruppo venga soddisfatto. In altri termini, un OR logico, viene valutato tra gruppi, mentre un AND logico viene valutato all'interno di un gruppo. Un esempio di quanto affermato è costituito dalla regola di sincronizzazione in uscita **Out to AAD – Group Join**, illustrata di seguito. Sono presenti diversi gruppi di filtri di sincronizzazione, ad esempio uno per i gruppi di sicurezza (securityEnabled EQUAL True) e uno per i gruppi di distribuzione (securityEnabled EQUAL False).

![Modifica regole di sincronizzazione in uscita](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Questa regola viene usata per definire i gruppi di cui deve essere effettuato il provisioning in AAD. I gruppi di distribuzione devono essere abilitati per la posta elettronica. Questa caratteristica non è invece necessaria per i gruppi di sicurezza. Come è possibile notare, vengono valutati anche diversi altri attributi aggiuntivi.

### Regole di unione

La terza sezione viene usata per configurare il modo in cui gli oggetti presenti nello spazio connettore sono correlati a oggetti presenti nel metaverse. La regola esaminata in precedenza non include alcuna configurazione per le regole di unione. Per questo motivo, ora viene presa in esame la regola **In from AD – User Join**.

![Modifica regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Il contenuto delle regole di unione dipende dall'opzione di associazione selezionata nell'Installazione guidata. Per una regola in ingresso la valutazione inizia con un oggetto presente nello spazio connettore di origine. Nelle regole di unione ciascun gruppo viene poi valutato in sequenza. Se, usando una delle regole di unione, la valutazione di un oggetto di origine indica che questo corrisponde esattamente a un oggetto presente nel metaverse, i due oggetti vengono uniti. Se dopo la valutazione di tutte le regole non risulta alcuna associazione, viene usato il tipo di collegamento indicato nella pagina di descrizione. Se per questa impostazione è specificato il valore Provision, nella destinazione, ovvero nel metaverse, viene creato un nuovo oggetto. Il provisioning di un nuovo oggetto nel metaverse viene definito anche "proiezione di un oggetto nel metaverse".

Le regole di unione vengono valutate una sola volta. Quando un oggetto dello spazio connettore viene unito a un oggetto del metaverse, i due oggetti rimangono uniti per tutto il tempo che la regola di sincronizzazione risulta soddisfatta.

Durante la valutazione delle regole di sincronizzazione, resterà in questo ambito solo una regola di sincronizzazione con regole di unione definite. Se per un oggetto vengono trovate più regole di sincronizzazione con regole di unione, viene generato un errore. Per questo motivo, quando nell'ambito sono presenti più regole di sincronizzazione per un oggetto, la procedura consigliata è di avere una sola regola di sincronizzazione con regole di unione definite. Nella configurazione predefinita di Azure Active Directory Sync è possibile trovare queste regole in base ai relativi nomi, cercando quelli che terminano con la parola Join. Una regola di sincronizzazione senza alcuna regola di unione definita applicherà il flusso di attributi se un'altra regola di sincronizzazione ha unito insieme degli oggetti o ha eseguito il provisioning di un oggetto nella destinazione.

Se si osserva l'immagine precedente, si noterà che la regola prova a creare un join **objectSID** con **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), che è la configurazione prevista in una topologia di foresta account o risorse. Si troverà la stessa regola in tutte le foreste, presupponendo che ogni foresta possa essere di tipo account o risorse. La regola funzionerà anche se si hanno account residenti in un'unica foresta e per i quali non è necessario creare un join.

### Trasformazioni

La sezione Trasformazioni definisce tutti i flussi di attributi che verranno applicati all'oggetto di destinazione quando gli oggetti vengono uniti e il filtro dell'ambito è soddisfatto. Prendendo di nuovo in esame la regola di sincronizzazione **In from AD – User AccountEnabled**, si noteranno le trasformazioni seguenti:

![Modifica regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Per contestualizzare, in una distribuzione con una foresta di account e una di risorse è probabile trovare un account abilitato nella foresta di account e un account disabilitato nella foresta di risorse con impostazioni Exchange e Lync. La regola di sincronizzazione ora presa in esame contiene gli attributi necessari per eseguire l'accesso. Si desidera che tali attributi vengano trasmessi dalla foresta in cui è presente un account abilitato. Tutti questi flussi di attributi vengono riuniti in una regola di sincronizzazione.

Una trasformazione può avere diversi tipi: Costante, Diretto ed Espressione.

- Un flusso Costante trasmetterà sempre un particolare valore. Nel caso sopra riportato, l'attributo del metaverse accountEnabled sarà sempre impostato su True.
- Un flusso Diretto trasmetterà il valore dell'attributo presente nell'origine all'attributo di destinazione.
- Il terzo tipo di trasformazione, Espressione, consente configurazioni più avanzate.

Il linguaggio dell'espressione è VBA (Visual Basic for Applications): gli utenti con conoscenze di Microsoft Office o VBScript riconosceranno il formato. Gli attributi vengono scritti tra parentesi quadre [attributeName]. Per i nomi di attributo e di funzione viene fatta distinzione tra maiuscole e minuscole. L'editor delle regole di sincronizzazione valuta tuttavia le espressioni e visualizza un avviso se una di esse non è valida. Tutte le espressioni sono scritte in una singola riga, con funzioni annidate. Per mostrare la potenza del linguaggio di configurazione, di seguito è riportato il flusso relativo a pwdLastSet, con commenti aggiuntivi inseriti:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Quello delle trasformazioni è un argomento molto vasto e riguarda in larga misura le configurazioni personalizzate che è possibile implementare con il servizio di sincronizzazione Azure AD Connect. Altre informazioni sull'argomento sono disponibili in altri articoli relativi al servizio di sincronizzazione Azure AD Connect.

## Precedenza

Fino ad ora sono state discusse singole regole di sincronizzazione, ma è necessario tenere presente che tali regole interagiscono nella configurazione. In alcuni casi il valore di un attributo è il risultato dell'interazione di diverse regole di sincronizzazione che contribuiscono alla definizione di uno stesso attributo di destinazione. In questo caso, per determinare l'attributo "vincente" viene usata la precedenza. Come esempio, viene preso in considerazione sourceAnchor, un attributo importante per l'accesso ad Azure AD. È possibile trovare un flusso di attributi per sourceAnchor in due diverse regole di sincronizzazione,**In from AD – User AccountEnabled** e **In from AD – User Common**. A causa della precedenza delle regole di sincronizzazione, se sono presenti diversi oggetti uniti all'oggetto del metaverse, alla definizione dell'attributo sourceAnchor contribuirà innanzitutto la foresta con un account abilitato. Se non sono presenti account abilitati, verrà usata la regola di sincronizzazione onnicomprensiva In **In from AD – User Common**. Questo approccio garantisce che anche per gli account disabilitati si continuerà a fornire un attributo sourceAnchor.

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La precedenza per le regole di sincronizzazione è impostata in gruppi mediante l'installazione guidata. Le regole di un gruppo hanno tutte lo stesso nome, ma sono collegate a diverse directory connesse. L'Installazione guidata assegnerà la precedenza più alta alla regola **In from AD – User Join** e ripeterà l'operazione per tutte le directory AD connesse. Procederà quindi con i successivi gruppi di regole secondo un ordine predefinito. All'interno di un gruppo le regole vengono aggiunte nell'ordine in cui i connettori sono stati aggiunti alla procedura guidata. Se con la procedura guidata viene aggiunto un altro connettore, le regole di sincronizzazione verranno riordinate e il nuovo connettore sarà inserito in ciascun gruppo all'ultimo posto.

## Riassumendo

Le informazioni fornite fino a questo punto dovrebbero essere sufficienti per comprendere il modo in cui le diverse regole di sincronizzazione agiscono sulla configurazione. Se si prendono in considerazione un utente e gli attributi che contribuiscono alla definizione del metaverse, le regole vengono applicate nell'ordine seguente:

| Nome | Commento |
| :------------- | :------------- |
| In from AD – User Join | Regola per l'unione degli oggetti dello spazio connettore con il metaverse. |
| In from AD – UserAccount Enabled | Attributi necessari per l'accesso a Azure AD e Office 365. Questi attributi dovranno essere rilevati dall'account abilitato. |
| In from AD – User Common from Exchange | Attributi trovati nell'Elenco indirizzi globale. Si presuppone che la qualità dei dati sia migliore nella foresta in cui è stata individuata la cassetta postale dell'utente. |
| In from AD – User Common | Attributi trovati nell'Elenco indirizzi globale. Se non è stata trovata una cassetta postale, alla definizione del valore dell'attributo può contribuire qualsiasi altro oggetto unito. |
| In from AD – User Exchange | È presente solo se è stato rilevato Exchange. Trasmetterà tutti gli attributi dell'infrastruttura di Exchange. |
| In from AD – User Lync | È presente solo se è stato rilevato Lync. Trasmetterà tutti gli attributi dell'infrastruttura di Lync. |

## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO3-->