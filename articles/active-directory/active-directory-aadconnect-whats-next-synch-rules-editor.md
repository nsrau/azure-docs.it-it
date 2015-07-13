
<properties 
	pageTitle="Uso dell'Editor regole di sincronizzazione di Azure AD Connect" 
	description="Informazioni sull’uso dell'Editor regole di sincronizzazione di Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Editor regole di sincronizzazione di Azure AD


## Uso dell'Editor regole di sincronizzazione

In Azure AD Connect è possibile configurare e ottimizzare il flusso di oggetti e di attributi tra Azure AD e le directory locali mediante la configurazione di regole di sincronizzazione.

Una regola di sincronizzazione è un oggetto di configurazione con un set di attributi trasmessi in flusso quando una condizione risulta soddisfatta. Viene usata anche per descrivere il modo in cui un oggetto presente in uno spazio connettore è correlato a un oggetto del metaverse. Questa caratteristica viene chiamata join o associazione. Le regole di sincronizzazione hanno una precedenza che indica come si correlano l’una all’altra. Una regola di sincronizzazione con un valore numerico di precedenza inferiore ha una precedenza più alta e, in caso di conflitto del flusso di attributi, la precedenza più alta avrà la priorità. Le regole di sincronizzazione possono essere configurate utilizzando l'Editor delle regole di sincronizzazione.

Come esempio verrà esaminata la regola di sincronizzazione "In from AD – User AccountEnabled". La riga verrà contrassegnata in SRE e verrà selezionata la regola di sincronizzazione Edit.A, dotata di quattro sezioni di configurazione: Descrizione, Filtro per la definizione dell'ambito, Regole di unione e Trasformazioni.

### Descrizione
La prima sezione fornisce informazioni di base, ad esempio il nome e una descrizione.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

Sono inoltre disponibili informazioni sul sistema connesso a cui la regola è correlata, sul tipo di oggetto presente nel sistema connesso a cui si applica la regola e sul tipo di oggetto del metaverse. Il tipo di oggetto del metaverse è sempre una persona, indipendentemente dal fatto che il tipo di oggetto di origine sia un utente, iNetOrgPerson o un contatto. Il tipo di oggetto del metaverse deve rimanere invariato, pertanto, viene creato come tipo generico. Il tipo di collegamento può essere impostato su Join, StickyJoin o Provision. Questa impostazione interagisce con la sezione Regole di unione e verrà discussa più avanti.

### Filtro per la definizione dell’ambito

La sezione Filtro per la definizione dell'ambito viene usata per configurare i tempi di applicazione di una regola di sincronizzazione. Dal momento che il nome della regola di sincronizzazione in esame indica che deve essere applicata soltanto per utenti abilitati, l'ambito viene configurato in modo che l'attributo AD userAccountControl non possa avere il bit 2 impostato. Quando in AD viene individuato un utente, questa regola viene applicata se l'attributo userAccountControl è impostato sul valore decimale 512 (utente normale abilitato), mentre non viene applicata se l'utente trovato ha l'attributo userAccountControl impostato su 514 (utente normale disabilitato)..

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

Il filtro per la definizione dell'ambito contiene gruppi e clausole che possono essere nidificati. Per essere applicate, tutte le clausole all'interno di un gruppo devono essere soddisfatte per una regola di sincronizzazione. Quando si definiscono più gruppi, affinché la regola venga applicata è necessario che almeno un gruppo venga soddisfatto. In altri termini, un OR logico, viene valutato tra gruppi, mentre un AND logico viene valutato all'interno di un gruppo. Un esempio di quando affermato è costituito dalla regola di sincronizzazione in uscita Out to AAD – Group Join, mostrata di seguito. Sono presenti due gruppi di filtri di sincronizzazione, uno per i gruppi di sicurezza (securityEnabled EQUAL True) e uno per i gruppi di distribuzione (securityEnabled EQUAL False).

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

Questa regola viene usata per definire i gruppi di cui deve essere effettuato il provisioning in AAD. I gruppi di distribuzione devono essere abilitati per la posta elettronica. Questa caratteristica non è invece necessaria per i gruppi di sicurezza. Come è possibile notare, vengono valutati anche diversi altri attributi aggiuntivi.

###Regole di unione
La terza sezione viene usata per configurare il modo in cui gli oggetti presenti nello spazio connettore sono correlati a oggetti presenti nel metaverse. La regola esaminata in precedenza non dispone di alcuna configurazione per regole di unione. Per questo motivo, viene ora presa in esame la regola In from AD – User Join.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

Il contenuto delle regole di unione dipende dall'opzione di associazione selezionata nell'Installazione guidata. Per una regola in ingresso la valutazione inizia con un oggetto presente nello spazio connettore di origine. Nelle regole di unione ciascun gruppo viene poi valutato in sequenza. Se, usando una delle regole di unione, la valutazione di un oggetto di origine indica che questo corrisponde esattamente a un oggetto presente nel metaverse, i due oggetti vengono uniti. Se dopo la valutazione di tutte le regole non risulta alcuna associazione, viene usato il tipo di collegamento indicato nella pagina di descrizione. Se per questa impostazione è specificato il valore Provision, nella destinazione, ovvero nel metaverse, viene creato un nuovo oggetto. Il provisioning di un nuovo oggetto nel metaverse viene definito anche "proiezione di un oggetto nel metaverse". Le regole di unione vengono valutate una sola volta. Quando un oggetto dello spazio connettore viene unito a un oggetto del metaverse, i due oggetti rimangono uniti per tutto il tempo che la regola di sincronizzazione risulta soddisfatta. Durante la valutazione delle regole di sincronizzazione, resterà in questo ambito solo una regola di sincronizzazione con regole di unione definite. Se per un oggetto vengono trovate più regole di sincronizzazione con regole di unione, viene generato un errore. Per questo motivo, quando nell'ambito sono presenti più regole di sincronizzazione per un oggetto, la procedura consigliata è di avere una sola regola di sincronizzazione con regole di unione definite. Nella configurazione predefinita di Azure AD Connect è possibile individuare queste regole in base ai relativi nomi, cercando quelli che terminano con la parola Join. Una regola di sincronizzazione senza alcuna regola di unione definita applicherà il flusso di attributi se un'altra regola di sincronizzazione ha unito insieme degli oggetti o ha eseguito il provisioning di un oggetto nella destinazione.

###Trasformazioni
La sezione Trasformazioni definisce tutti i flussi di attributi che verranno applicati all'oggetto di destinazione quando gli oggetti vengono uniti e il filtro dell'ambito è soddisfatto. Prendendo di nuovo in esame la regola di sincronizzazione In from AD – User AccountEnabled, è possibile notare le seguenti trasformazioni:

<center>![Join Rule](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

Per contestualizzare, in una distribuzione con una foresta di account e una di risorse è probabile trovare un account abilitato nella foresta di account e un account disabilitato nella foresta di risorse con impostazioni Exchange e Lync. La regola di sincronizzazione ora presa in esame contiene gli attributi necessari per eseguire l'accesso. Si desidera che tali attributi vengano trasmessi dalla foresta in cui è presente un account abilitato. Tutti questi attributi vengono riuniti in un'unica regola di sincronizzazione. Di seguito sono indicati i diversi tipi di trasformazione: Costante, Diretto ed Espressione. Un flusso Costante trasmetterà sempre un particolare valore. Nel caso sopra riportato, l'attributo del metaverse accountEnabled sarà sempre impostato su True. Un flusso Diretto trasmetterà il valore dell'attributo presente nell'origine all'attributo di destinazione. Il terzo tipo di trasformazione, Espressione, consente configurazioni più avanzate. Il linguaggio dell'espressione è VBA (Visual Basic for Applications): gli utenti con conoscenze di Microsoft Office o VBScript riconosceranno il formato. Gli attributi vengono scritti tra parentesi quadre [attributeName]. Per i nomi di attributo e di funzione viene fatta distinzione tra maiuscole e minuscole. L'editor delle regole di sincronizzazione, tuttavia, valuta le espressioni e visualizza un avviso nel caso in cui una di esse non sia valida. Tutte le espressioni sono scritte in una singola riga, con funzioni nidificate. Per mostrare la potenza del linguaggio di configurazione, di seguito è riportato il flusso relativo a pwdLastSet, con commenti aggiuntivi inseriti:

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

Quello delle trasformazioni è un argomento molto vasto e riguarda in larga misura le configurazioni personalizzate che è possibile implementare con Azure AD Connect. Le configurazioni personalizzate non vengono discusse in questo argomento di panoramica. Nel corso del documento, tuttavia, verranno presi in esame alcuni flussi di attributi aggiuntivi.
 

<!---HONumber=62-->