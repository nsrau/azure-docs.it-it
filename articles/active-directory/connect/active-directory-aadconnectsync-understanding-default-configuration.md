---
title: 'Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita | Documentazione Microsoft'
description: Questo articolo descrive la configurazione predefinita nel servizio di sincronizzazione Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 6ba1739825a6f0898e417ca37fa6bf370ef17d6c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita
In questo articolo vengono illustrate le regole di configurazione predefinite, elencando le regole e spiegando come influiscono sulla configurazione. Questo articolo illustra anche la configurazione predefinita del servizio di sincronizzazione Azure AD Connect. Scopo dell'articolo è spiegare con un esempio reale il funzionamento del modello di configurazione, detto provisioning dichiarativo. Nell'articolo si presuppone che l'utente abbia già installato e configurato il servizio di sincronizzazione Azure AD Connect tramite l'Installazione guidata.

Per i dettagli del modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regole predefinite da locale ad Azure AD
Le espressioni seguenti sono disponibili nella configurazione predefinita.

### <a name="user-out-of-box-rules"></a>Regole utente predefinite
Queste regole vengono applicate anche al tipo di oggetto iNetOrgPerson.

Un oggetto utente deve soddisfare i seguenti requisiti per essere sincronizzato:

* Deve disporre di un sourceAnchor.
* Dopo la creazione dell'oggetto in Azure AD, l'attributo sourceAnchor non può essere modificato. Se il valore viene modificato in locale, l'oggetto interromperà la sincronizzazione fino a quando l'attributo sourceAnchor non verrà modificato di nuovo con il valore precedente.
* Deve disporre dell'attributo accountEnabled (userAccountControl) compilato. Con una versione di Active Directory locale, questo attributo sarà sempre presente e popolato.

Gli oggetti utente seguenti **non** vengono sincronizzati con Azure AD:

* `IsPresent([isCriticalSystemObject])`. Assicurarsi di non sincronizzare molti oggetti predefiniti in Active Directory, ad esempio l'account predefinito Administrator.
* `IsPresent([sAMAccountName]) = False`. Verificare che gli oggetti utente senza l'attributo sAMAccountName non vengano sincronizzati. Nella pratica, questo caso si verifica solo in un dominio aggiornato da NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Non sincronizzare l'account di servizio utilizzato dal servizio di sincronizzazione Azure AD Connect e dalle versioni precedenti.
* Non sincronizzare gli account di Exchange che non funzionerebbero in Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Non sincronizzare gli oggetti che non funzionerebbero in Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Questa maschera di bit (&amp;H21C07000) filtra gli oggetti seguenti:
  * Cartelle pubbliche abilitate alla posta elettronica
  * Cassetta postale Supervisore sistema
  * Cassetta postale Database cassette postali (cassetta postale di sistema)
  * Gruppo di protezione universale (non si applica a un utente, ma è presente per motivi di compatibilità)
  * Gruppo non universale (non si applica a un utente, ma è presente per motivi di compatibilità)
  * Piano della cassetta postale
  * Cassetta postale di individuazione
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Non sincronizzare oggetti generati dalla replica.

Si applicano le seguenti regole di attributo:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. L'attributo sourceAnchor non viene specificato da una cassetta postale collegata. Se è presente una cassetta postale collegata, si presuppone che l'account effettivo venga aggiunto in un secondo momento.
* Attributi relativi a Exchange vengono sincronizzati solo se l'attributo **mailNickName** ha un valore.
* Quando sono presenti più foreste, gli attributi vengono utilizzati nell'ordine seguente:
  1. Gli attributi correlati all'accesso, ad esempio userPrincipalName, vengono specificati dalla foresta con un account abilitato.
  2. Gli attributi che si possono trovare in un elenco indirizzi globale di Exchange verranno specificati dalla foresta con una cassetta postale di Exchange.
  3. Se non è possibile trovare una cassetta postale, questi attributi possono provenire da qualsiasi foresta.
  4. Gli attributi relativi a Exchange, ovvero attributi tecnici non visibili nell'elenco indirizzi globale, vengono specificati dalla foresta in cui `mailNickname ISNOTNULL`.
  5. Se sono presenti più foreste che soddisfano una di queste regole, verrà usato l'ordine di creazione (data/ora) dei connettori (foreste) per determinare quale foresta specificherà gli attributi.

### <a name="contact-out-of-box-rules"></a>Regole predefinite del contatto
Un oggetto contatto deve soddisfare i seguenti requisiti per essere sincronizzato:

* Il contatto deve essere abilitato alla posta elettronica. Viene verificato tramite le regole seguenti:
  * `IsPresent([proxyAddresses]) = True)`. L'attributo proxyAddresses deve essere compilato.
  * Un indirizzo di posta elettronica principale è disponibile nell'attributo proxyAddresses o nell'attributo mail. La presenza di un simbolo @ consente di verificare che il contenuto sia un indirizzo di posta elettronica. Una di queste due regole deve restituire True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. È presente una voce con "SMTP:" e, in caso affermativo, è presente un simbolo @ nella stringa?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. L'attributo mail è compilato e, in tal caso, è presente un simbolo @ nella stringa?

Gli oggetti contatto seguenti **non** vengono sincronizzati con Azure AD:

* `IsPresent([isCriticalSystemObject])`. Verificare che nessun oggetto contatto contrassegnato come critico venga sincronizzato. Non devono essere presenti oggetti contatto con una configurazione predefinita.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Questi oggetti non funzionerebbero in Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Non sincronizzare oggetti generati dalla replica.

### <a name="group-out-of-box-rules"></a>Regole di gruppo predefinite
Un oggetto gruppo deve soddisfare i seguenti requisiti per essere sincronizzato:

* Deve contenere meno di 50.000 membri. Si tratta del numero di membri del gruppo locale.
  * Se il numero di membri supera il limite prima che la prima sincronizzazione venga avviata, il gruppo non verrà sincronizzato.
  * Se il numero di membri aumenta rispetto al momento della creazione, la sincronizzazione si interromperà quando raggiunge i 50.000 membri, finché il numero dei membri non diventa nuovamente inferiore a 50.000.
  * Nota: il limite di 50.000 membri viene applicato anche da Azure AD. Non è possibile sincronizzare i gruppi con un numero maggiore di membri, anche se si modifica o si rimuove la regola.
* Se il gruppo è un **gruppo di distribuzione**deve essere anche abilitato per la posta elettronica. Per informazioni sull’applicazione di questa regola vedere [Regole predefinite del contatto](#contact-out-of-box-rules) .

Gli oggetti di gruppo seguenti **non** vengono sincronizzati con Azure AD:

* `IsPresent([isCriticalSystemObject])`. Assicurarsi di non sincronizzare molti oggetti predefiniti in Active Directory, ad il gruppo predefinito Administrators.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Gruppo legacy usato da DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Gruppo di ruoli.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Non sincronizzare oggetti generati dalla replica.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regole predefinite di ForeignSecurityPrincipal
Le entità di protezione esterne vengono aggiunte a qualsiasi oggetto (\*) nel metaverse. Questa unione si verifica in realtà solo per gli utenti e i gruppi di sicurezza. Questa configurazione assicura che l'appartenenza a più foreste venga risolta e rappresentata correttamente in Azure AD.

### <a name="computer-out-of-box-rules"></a>Regole predefinite del computer
Un oggetto computer deve soddisfare i seguenti requisiti per essere sincronizzato:

* `userCertificate ISNOTNULL`. Solo i computer Windows 10 popolano questo attributo. Tutti gli oggetti computer con un valore in questo attributo vengono sincronizzati.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Informazioni sullo scenario delle regole predefinite
Questo esempio usa una distribuzione con una foresta di account (A), una foresta di risorse (R) e una directory di Azure AD.

![Immagine con descrizione dello scenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

In questa configurazione si presuppone di trovare un account abilitato nella foresta di account e un account disabilitato nella foresta di risorse con una cassetta postale collegata.

L'obiettivo della configurazione predefinita è il seguente:

* Gli attributi correlati all'accesso verranno sincronizzati dalla foresta con l'account abilitato.
* Gli attributi che si possono trovare nell'elenco indirizzi globale verranno sincronizzati dalla foresta con la cassetta postale. Se non si trova una cassetta postale, verrà usata un'altra foresta qualsiasi.
* Se si trova una cassetta postale collegata, è necessario che venga trovato l'account abilitato collegato per consentire l'esportazione dell'oggetto in Azure AD.

### <a name="synchronization-rule-editor"></a>Editor delle regole di sincronizzazione
La configurazione può essere visualizzata e modificata usando l'editor delle regole di sincronizzazione (SRE, Synchronization Rules Editor). Nel menu Start è disponibile un apposito collegamento.

![Icona dell'editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE è uno strumento del Resource Kit e viene installato con il servizio di sincronizzazione Azure AD Connect. Per avviare lo strumento è necessario essere membro del gruppo ADSyncAdmins. All'avvio viene visualizzato un pannello simile al seguente:

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

In questo pannello sono riportate tutte le regole di sincronizzazione create per la configurazione. Ogni riga nella tabella corrisponde a una regola di sincronizzazione. In Rule Types (Tipi di regola) a sinistra sono elencati due tipi diversi: Inbound (In ingresso) e Outbound (In uscita). I tipi In ingresso e In uscita derivano dalla visualizzazione dei metaverse. In questa panoramica verranno esaminate soprattutto le regole in ingresso. L'elenco effettivo delle regole di sincronizzazione dipende dallo schema rilevato in AD. Nella figura precedente, la foresta di account (fabrikamonline.com) non ha alcun servizio, ad esempio Exchange e Lync, e non sono state create regole di sincronizzazione per questi servizi. Nella foresta di risorse (res.fabrikamonline.com) sono tuttavia disponibili regole di sincronizzazione per questi servizi. A seconda della versione rilevata, il contenuto delle regole sarà diverso. In una distribuzione con Exchange 2013 saranno ad esempio configurati più flussi di attributi rispetto a Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regola di sincronizzazione
Una regola di sincronizzazione è un oggetto di configurazione con un set di attributi trasmessi in flusso quando una condizione risulta soddisfatta. Viene usata anche per descrivere il modo in cui un oggetto presente in uno spazio connettore è correlato a un oggetto del metaverse. Questa caratteristica viene chiamata **join** o **associazione**. Le regole di sincronizzazione hanno un valore di precedenza che indica come si correlano l’una all’altra. Una regola di sincronizzazione con un valore numerico più basso ha una precedenza più alta e, in caso di conflitto tra flussi di attributi, la precedenza più alta avrà la priorità.

A titolo di esempio, esaminare la regola di sincronizzazione **In from AD – User AccountEnabled**. Selezionare questa riga in SRE e scegliere **Edit**(Modifica).

Essendo una regola predefinita, verrà visualizzato un avviso quando la si apre. Poiché non è consigliabile apportare [modifiche alle regole predefinite](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), verrà chiesto come si vuole procedere. In questo caso si vuole solo visualizzare la regola. Selezionare **No**.

![Avviso delle regole di sincronizzazione](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Una regola di sincronizzazione include quattro sezioni di configurazione: descrizione, filtro per la definizione dell'ambito, regole di unione e trasformazioni.

#### <a name="description"></a>DESCRIZIONE
La prima sezione fornisce informazioni di base, ad esempio il nome e una descrizione.

![Scheda Description (Descrizione) nell'editor delle regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Sono anche disponibili informazioni sul sistema connesso a cui la regola è correlata, sul tipo di oggetto presente nel sistema connesso a cui si applica la regola e sul tipo di oggetto del metaverse. Il tipo di oggetto del metaverse è sempre una persona, indipendentemente dal fatto che il tipo di oggetto di origine sia un utente, iNetOrgPerson o un contatto. Il tipo di oggetto del metaverse deve rimanere invariato, pertanto, viene creato come tipo generico. Il tipo di collegamento può essere impostato su Join, StickyJoin o Provision. Questa impostazione interagisce con la sezione Join rules (Regole di unione) e verrà discussa più avanti.

Si può anche vedere che questa regola di sincronizzazione viene usata per la sincronizzazione della password. Se un utente rientra nell'ambito di questa regola di sincronizzazione, la password verrà sincronizzata da locale al cloud, presupponendo che la funzionalità di sincronizzazione delle password sia stata abilitata.

#### <a name="scoping-filter"></a>Filtro per la definizione dell'ambito
La sezione Filtro per la definizione dell'ambito viene usata per configurare i tempi di applicazione di una regola di sincronizzazione. Dal momento che il nome della regola di sincronizzazione in esame indica che deve essere applicata solo per utenti abilitati, l'ambito viene configurato in modo che l'attributo AD **userAccountControl** non abbia il bit 2 impostato. Quando il motore di sincronizzazione trova un utente in AD, applica questa regola di sincronizzazione quando **userAccountControl** è impostato sul valore decimale 512, ovvero utente normale abilitato. Non applica la regola quando l'utente ha **userAccountControl** impostato su 514, ovvero utente normale disabilitato.

![Scheda Scoping filter (Filtro di ambito) nell'editor delle regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Il filtro per la definizione dell'ambito contiene gruppi e clausole che possono essere annidati. Per essere applicate, tutte le clausole all'interno di un gruppo devono essere soddisfatte per una regola di sincronizzazione. Quando si definiscono più gruppi, affinché la regola venga applicata è necessario che almeno un gruppo venga soddisfatto. In altri termini, un OR logico viene valutato tra gruppi, mentre un AND logico viene valutato all'interno di un gruppo. Un esempio di questa configurazione è costituito dalla regola di sincronizzazione in uscita **Out to AAD – Group Join**. Sono presenti diversi gruppi di filtri di sincronizzazione, ad esempio uno per i gruppi di sicurezza (`securityEnabled EQUAL True`) e uno per i gruppi di distribuzione (`securityEnabled EQUAL False`).

![Scheda Scoping filter (Filtro di ambito) nell'editor delle regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Questa regola viene usata per definire i gruppi di cui deve essere effettuato il provisioning in Azure AD. I gruppi di distribuzione devono essere abilitati per la posta elettronica per essere sincronizzati con Azure AD. La posta elettronica non è invece necessaria per i gruppi di sicurezza.

#### <a name="join-rules"></a>Regole di unione
La terza sezione viene usata per configurare il modo in cui gli oggetti presenti nello spazio connettore sono correlati a oggetti presenti nel metaverse. La regola esaminata in precedenza non include alcuna configurazione per le regole di unione. Per questo motivo, verrà presa in esame la regola **In from AD – User Join**.

![Scheda Join rules (Regole di unione) nell'editor delle regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Il contenuto della regola di unione dipende dall'opzione corrispondente selezionata nell'installazione guidata. Per una regola in ingresso, la valutazione inizia con un oggetto presente nello spazio connettore di origine. Ogni gruppo nelle regole di unione viene valutato in sequenza. Se, usando una delle regole di unione, la valutazione di un oggetto di origine indica che questo corrisponde esattamente a un oggetto presente nel metaverse, i due oggetti verranno uniti. Se dopo la valutazione di tutte le regole non risulta alcuna associazione, viene usato il tipo di collegamento indicato nella pagina di descrizione. Se la configurazione è impostata su **Provision** (Provisioning), nella destinazione verrà creato un nuovo oggetto, il metaverse. Il provisioning di un nuovo oggetto nel metaverse viene definito anche **proiezione** di un oggetto nel metaverse.

Le regole di unione vengono valutate una sola volta. Quando un oggetto dello spazio connettore viene unito a un oggetto del metaverse, i due oggetti rimangono uniti finché l'ambito della regola di sincronizzazione risulta soddisfatto.

Durante la valutazione delle regole di sincronizzazione, nell'ambito deve essere presente una sola regola di sincronizzazione con regole di unione definite. Se per un oggetto vengono trovate più regole di sincronizzazione con regole di unione, viene generato un errore. Quando nell'ambito sono presenti più regole di sincronizzazione per un oggetto, la procedura consigliata prevede quindi di avere una sola regola di sincronizzazione con regole di unione definite. Nella configurazione predefinita del servizio di sincronizzazione Azure AD Connect è possibile trovare queste regole in base ai relativi nomi, cercando quelli che terminano con la parola **Join** . Una regola di sincronizzazione senza alcuna regola di unione definita applica il flusso di attributi se un'altra regola di sincronizzazione ha unito gli oggetti o ha effettuato il provisioning di un nuovo oggetto nella destinazione.

Se si osserva l'immagine precedente, si noterà che la regola prova a creare un join **objectSID** con **msExchMasterAccountSid** (Exchange) e **msRTCSIP-OriginatorSid** (Lync), ovvero la configurazione prevista in una topologia di foresta account o risorse. La stessa regola è presente in tutte le foreste. Si presuppone che ogni foresta possa essere di tipo account o risorse. Questa configurazione funziona anche se si hanno account residenti in un'unica foresta e per i quali non è necessario creare un join.

#### <a name="transformations"></a>Trasformazioni
La sezione Transformations (Trasformazioni) definisce tutti i flussi di attributi applicati all'oggetto di destinazione quando gli oggetti vengono uniti e il filtro dell'ambito è soddisfatto. Prendendo di nuovo in esame la regola di sincronizzazione **In from AD – User AccountEnabled** , si noteranno le trasformazioni seguenti:

![Scheda Transformations (Trasformazioni) nell'editor delle regole di sincronizzazione ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Per contestualizzare questa configurazione, in una distribuzione con una foresta di account e una di risorse si prevede di trovare un account abilitato nella foresta di account e un account disabilitato nella foresta di risorse con impostazioni Exchange e Lync. La regola di sincronizzazione presa in esame contiene gli attributi necessari per eseguire l'accesso. Questi attributi devono essere trasmessi dalla foresta in cui è presente un account abilitato. Tutti questi flussi di attributi vengono riuniti in una regola di sincronizzazione.

Una trasformazione può avere diversi tipi: Costante, Diretto ed Espressione.

* Un flusso costante passa sempre un valore hardcoded. Nel caso precedente viene sempre impostato il valore **True** nell'attributo del metaverse denominato **accountEnabled**.
* Un flusso diretto trasmette sempre il valore dell'attributo presente nell'origine all'attributo di destinazione così com'è.
* Il terzo tipo di trasformazione, Espressione, consente configurazioni più avanzate.

Il linguaggio delle espressioni è VBA (Visual Basic for Applications), quindi gli utenti con conoscenze di Microsoft Office o VBScript riconosceranno il formato. Gli attributi vengono scritti tra parentesi quadre [attributeName]. I nomi di attributo e di funzione distinguono tra maiuscole e minuscole. L'editor delle regole di sincronizzazione valuta tuttavia le espressioni e visualizza un avviso se una di esse non è valida. Tutte le espressioni sono scritte in una singola riga, con funzioni annidate. Per mostrare la potenza del linguaggio di configurazione, di seguito è riportato il flusso relativo a pwdLastSet, con commenti aggiuntivi inseriti:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Per altre informazioni sul linguaggio delle espressioni per i flussi degli attributi, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Precedenza
Sinora sono state esaminate singole regole di sincronizzazione, ma queste regole interagiscono nella configurazione. In alcuni casi, il valore di un attributo proviene da più regole di sincronizzazione per uno stesso attributo di destinazione. In questo caso si usa la precedenza per stabilire quale attributo abbia la priorità. Come esempio viene preso in considerazione sourceAnchor, un attributo importante per l'accesso ad Azure AD. È possibile trovare un flusso di attributi per questo attributo in due diverse regole di sincronizzazione, **In from AD – User AccountEnabled** e **In from AD – User Common**. A causa della precedenza delle regole di sincronizzazione, l'attributo sourceAnchor verrà specificato prima dalla foresta con un account abilitato, se sono presenti più oggetti uniti nell'oggetto del metaverse. Se non sono presenti account abilitati, il motore di sincronizzazione userà la regola di sincronizzazione onnicomprensiva **In from AD – User Common**. Questa configurazione assicura che anche per gli account disabilitati sia ancora presente un attributo sourceAnchor.

![Regole di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

La precedenza per le regole di sincronizzazione è impostata in gruppi mediante l'installazione guidata. Le regole di un gruppo hanno tutte lo stesso nome, ma sono collegate a diverse directory connesse. L'installazione guidata assegna la precedenza massima alla regola **In from AD – User Join** e ripete l'operazione per tutte le directory AD connesse. Procede quindi con i successivi gruppi di regole secondo un ordine predefinito. All'interno di un gruppo, le regole vengono aggiunte nell'ordine in cui i connettori sono stati aggiunti alla procedura guidata. Se con la procedura guidata viene aggiunto un altro connettore, le regole di sincronizzazione verranno riordinate e le regole del nuovo connettore verranno inserite per ultime in ogni gruppo.

### <a name="putting-it-all-together"></a>Riassumendo
Le informazioni fornite fino a questo punto dovrebbero essere sufficienti per comprendere il modo in cui le diverse regole di sincronizzazione agiscono sulla configurazione. Se si prendono in considerazione un utente e gli attributi passati al metaverse, le regole vengono applicate nell'ordine seguente:

| NOME | Comment |
|:--- |:--- |
| In from AD – User Join |Regola per l'unione degli oggetti dello spazio connettore con il metaverse. |
| In from AD – UserAccount Enabled |Attributi necessari per l'accesso ad Azure AD e Office 365. Questi attributi dovranno essere rilevati dall'account abilitato. |
| In from AD – User Common from Exchange |Attributi trovati nell'Elenco indirizzi globale. Si presuppone che la qualità dei dati sia migliore nella foresta in cui è stata individuata la cassetta postale dell'utente. |
| In from AD – User Common |Attributi trovati nell'Elenco indirizzi globale. Se non è stata trovata una cassetta postale, alla definizione del valore dell'attributo può contribuire qualsiasi altro oggetto unito. |
| In from AD – User Exchange |Presente solo se è stato rilevato Exchange. Trasmette tutti gli attributi dell'infrastruttura di Exchange. |
| In from AD – User Lync |Presente solo se è stato rilevato Lync. Trasmette tutti gli attributi dell'infrastruttura di Lync. |

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Per altre informazioni sul linguaggio delle espressioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Per approfondire il funzionamento della configurazione predefinita, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni su utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Per informazioni su come apportare una modifica pratica con il provisioning dichiarativo, vedere [Servizio di sincronizzazione Azure AD Connect: come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md).

**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

