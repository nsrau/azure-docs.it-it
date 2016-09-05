<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: come apportare modifiche alla configurazione predefinita | Microsoft Azure"
	description="Fornisce informazioni dettagliate su come apportare modifiche alla configurazione nel servizio di sincronizzazione Azure AD Connect."
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
	ms.date="08/23/2016"
	ms.author="andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: come apportare modifiche alla configurazione predefinita
Questo argomento illustra in dettaglio come apportare modifiche alla configurazione predefinita nel servizio di sincronizzazione Azure AD Connect. Include i passaggi per alcuni scenari comuni. Con queste informazioni si potranno apportare alcune semplici modifiche alla propria configurazione in base alle regole di business.

## Editor regole di sincronizzazione
L'editor delle regole di sincronizzazione viene usato per visualizzare e modificare la configurazione predefinita. È disponibile nel menu Start nel gruppo **Azure AD Connect**. ![Menu Start con l'editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando lo si apre, vengono visualizzate le regole predefinite.

![Editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### Spostamento nell'editor
Gli elenchi a discesa nella parte superiore dell'editor consentono di trovare rapidamente una regola particolare. Ad esempio, se si vogliono visualizzare le regole in cui è incluso l'attributo proxyAddresses, modificare gli elenchi a discesa come indicato di seguito: ![Filtro nell'editor delle regole di sincronizzazione](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) Per reimpostare il filtro e caricare una nuova configurazione, premere **F5** sulla tastiera.

In alto a destra è presente il pulsante **Add new Rule** (Aggiungi nuova regola). Questo pulsante viene usato per creare la regola personalizzata.

Nella parte inferiore sono presenti i pulsanti per eseguire operazioni su una regola di sincronizzazione selezionata. I pulsanti **Edit** (Modifica) e **Delete** (Elimina) eseguono le operazioni previste. **Export** (Esporta) genera uno script di PowerShell per ricreare la regola di sincronizzazione. Questa procedura consente di spostare una regola di sincronizzazione da un server a un altro.

## Creare la prima regola personalizzata
La modifica più comune riguarda i flussi degli attributi. I dati nella directory di origine potrebbero non quelli che si vogliono usare in Azure AD. Nell'esempio in questa sezione si vuole assicurare che formato il nome di un utente sia sempre nel**formato corretto**.

### Disabilitare l'utilità di pianificazione
Per impostazione predefinita, l'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) viene eseguita ogni 30 minuti. Si vuole garantire che non venga avviata mentre si apportano modifiche e si risolvono i problemi delle nuove regole. Per disabilitare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`

![Disabilitare l'utilità di pianificazione](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### Creare la regola

1. Fare clic su **Add new rule** (Aggiungi nuova regola).
2. Nella pagina **Description** (Descrizione) immettere le informazioni seguenti: ![Filtro delle regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- Name: (Nome) assegnare alla regola un nome descrittivo.
	- Description: (Descrizione) alcuni chiarimenti che permettono a un altro utente può comprendere la funzione della regola.
	- Connected system: (Sistema connesso) sistema in cui è possibile trovare l'oggetto. In questo caso viene selezionato Active Directory Connector.
	- Connected System/Metaverse Object Type: (Tipo di oggetto sistema connesso/Tipo di oggetto metaverse) selezionare rispettivamente **User** (Utente) e **Person** (Persona).
	- Link Type: (Tipo di collegamento) modificare questo valore in **Join** (Unisci).
	- Precedence: (Precedenza) fornire un valore univoco nel sistema. Un valore numerico inferiore indica una precedenza maggiore.
	- Tag: lasciare vuoto. È necessario popolare questa casella con un valore solo per le regole predefinite di Microsoft.
3. Nella pagina **Scoping filter** (Filtro di ambito) immettere **givenName ISNOTNULL**. ![Filtro dell'ambito per le regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) Questa sezione viene usata per definire gli oggetti a cui dovrà essere applicata la regola. Se lasciata vuota, la regola verrà applicata a tutti gli oggetti utente, ma includerà sale riunioni, account del servizio e altri oggetti utente non di persone.
4. Lasciare vuoto il campo in **Join rules** (Regole di unione).
5. Nella pagina **Transformations** (Trasformazioni) modificare FlowType in **Expression** (Espressione). Per Target Attribute (Attributo di destinazione) selezionare **giveName** e in Source (Origine) immettere `PCase([givenName])`. ![Trasformazioni di regole in ingresso](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) Il motore di sincronizzazione fa distinzione tra maiuscole e minuscole sia nel nome della funzione che nel nome dell'attributo. Nel caso di un errore di digitazione, viene visualizzato un avviso quando si aggiunge la regola. L'editor consente di salvare e continuare, quindi si dovrà riaprire la regola e correggerla.
6. Fare clic su **Add** (Aggiungi) per salvare la regola.

La nuova regola personalizzata sarà visibile insieme alle altre regole di sincronizzazione nel sistema.

### Verificare la modifica
Con questa nuova modifica si vuole verificare che funzioni come previsto e non generi errori. A seconda del numero di oggetti disponibili, esistono due modi diversi per eseguire questo passaggio.

1. Eseguire una sincronizzazione completa in tutti gli oggetti
2. Eseguire un'anteprima e una sincronizzazione completa in un singolo oggetto

Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu Start. I passaggi in questa sezione fanno tutti parte di questo strumento.

1. **Sincronizzazione completa in tutti gli oggetti** Selezionare **Connectors** (Connettori) nella parte superiore. Identificare il connettore modificato nella sezione precedente, in questo caso Servizi di dominio Active Directory, e selezionarlo. Selezionare **Run** (Esegui) in Actions (Azioni) e selezionare **Full Synchronization** (Sincronizzazione completa), quindi fare clic su **OK**. ![Sincronizzazione completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)Gli oggetti vengono aggiornati nel metaverse. Ora si vuole esaminare l'oggetto nel metaverse.

2. **Anteprima e sincronizzazione completa in un singolo oggetto** Selezionare **Connectors** (Connettori) nella parte superiore. Identificare il connettore modificato nella sezione precedente, in questo caso Servizi di dominio Active Directory, e selezionarlo. Selezionare **Search Connector Space** (Cerca spazio connettore). Usare l'ambito per trovare un oggetto che si vuole usare per testare la modifica. Selezionare l'oggetto e fare clic su **Preview** (Anteprima). Nella nuova schermata selezionare **Commit Preview** (Anteprima commit). ![Anteprima commit](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) Viene eseguito il commit della modifica nel metaverse.

**Esaminare l'oggetto nel metaverse** Si vogliono selezionare alcuni oggetti di esempio per assicurarsi che il valore sia previsto e la regola applicata. Selezionare **Metaverse Search** (Ricerca metaverse) nella parte superiore. Aggiungere il filtro necessario per trovare gli oggetti pertinenti. Dai risultati della ricerca aprire un oggetto. Esaminare i valori dell'attributo e verificare anche che nella colonna **Sync Rules** (Regole di sincronizzazione) la regola sia applicata come previsto. ![Ricerca metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### Abilitare l'utilità di pianificazione
Se è tutto come previsto, è possibile abilitare di nuovo l'utilità di pianificazione. In PowerShell eseguire `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## Altre modifiche comuni del flusso dell'attributo
La sezione precedente descrive come apportare modifiche a un flusso dell'attributo. In questa sezione vengono forniti alcuni esempi aggiuntivi. La procedura per creare la regola di sincronizzazione è abbreviata, ma è possibile trovare quella completa nella sezione precedente.

### Usare un attributo diverso da quello predefinito
In Fabrikam è disponibile una foresta in cui viene usato l'alfabeto locale per nome, cognome e nome visualizzato. La rappresentazione in caratteri latini di questi attributi è disponibile negli attributi dell'estensione. Quando viene compilato l'elenco indirizzi globale in Azure AD e Office 365, l'organizzazione vuole invece usare questi attributi.

Con una configurazione predefinita, un oggetto della foresta locale avrà un aspetto simile al seguente: ![Flusso dell'attributo 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Per creare una regola con altri flussi di attributi, eseguire queste operazioni:

- Avviare l'**editor delle regole di sincronizzazione** dal menu Start.
- Con la voce **Inbound** (In ingresso) ancora selezionata a sinistra, fare clic sul pulsante **Add new rule** (Aggiungi nuova regola).
- Assegnare alla regola un nome e una descrizione. Selezionare la directory Active Directory locale e i tipi di oggetto pertinenti. In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci). Per la precedenza scegliere un numero non usato da un'altra regola. Le regole predefinite iniziano con 100, quindi in questo esempio si può usare il valore 50. ![Flusso dell'attributo 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Lasciare vuoto l'ambito, perché deve essere applicato a tutti gli oggetti utente della foresta.
- Lasciare vuote le regole di unione, per consentire alla regola predefinita di gestire tutte le unioni.
- In Transformations (Trasformazioni) creare i flussi seguenti. ![Flusso dell'attributo 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Fare clic su **Add** (Aggiungi) per salvare la regola.
- Passare a **Synchronization Service Manager**. In **Connectors** (Connettori) selezionare il connettore in cui è stata aggiunta la regola. Selezionare **Run** (Esegui) e **Full Synchronization** (Sincronizzazione completa). Una sincronizzazione completa ricalcola tutti gli oggetti usando le regole correnti.

Di seguito è riportato il risultato per lo stesso oggetto con questa regola personalizzata: ![Flusso dell'attributo 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### Lunghezza degli attributi
Per impostazione predefinita, gli attributi di stringa sono configurati come indicizzabili e la lunghezza massima è di 448 caratteri. Se si utilizzano attributi di stringa che potrebbero contenere più caratteri, assicurarsi di includere il codice seguente nel flusso di attributi: `attributeName` <- `Left([attributeName],448)`

### Modifica di userPrincipalSuffix
L'attributo userPrincipalName in Active Directory non è sempre noto agli utenti e potrebbe non essere adatto come ID di accesso. L'installazione guidata del servizio di sincronizzazione Azure AD Connect consente di scegliere un attributo diverso, ad esempio mail. In alcuni casi è però necessario calcolare l'attributo. Ad esempio, Contoso ha due directory di Azure AD, una per la produzione e una per i test. L'azienda vuole che gli utenti del tenant di test usino un altro suffisso nell'ID di accesso. `userPrincipalName``Word([userPrincipalName],1,"@") & "@contosotest.com"`

In questa espressione selezionare tutti gli elementi che si trovano a sinistra della prima chiocciola (Word) e concatenarli con una stringa fissa.

### Convertire un multivalore in un valore singolo
Alcuni attributi in Active Directory sono di tipo multivalore nello schema, anche se compaiono come valore singolo in Utenti e computer di Active Directory, ad esempio l'attributo description. `description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Se l'attributo ha un valore in questa espressione, si seleziona il primo elemento (Item) dell'attributo, si rimuovono gli spazi iniziali e finali (Trim) e quindi si mantengono i primi 448 caratteri (Left) della stringa.

### Non trasmettere un attributo
Per informazioni generali sullo scenario per questa sezione, vedere [Controllare il processo del flusso dell'attributo](#control-the-attribute-flow-process).

Sono disponibili due modalità per non trasmettere un attributo. Il primo è disponibile nell'installazione guidata e consente di [rimuovere gli attributi selezionati](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Questa opzione funziona se l'attributo non è mai stato sincronizzato in precedenza. Tuttavia, se si è iniziato a sincronizzare questo attributo e lo si rimuove in seguito con questa funzionalità, il motore di sincronizzazione interrompe la gestione dell'attributo e i valori esistenti rimangono in Azure AD.

Se si vuole rimuovere il valore di un attributo e assicurarsi che non venga trasmesso in futuro, creare invece una regola personalizzata.

In Fabrikam si è notato che alcuni degli attributi sincronizzati nel cloud non avrebbero dovuto esserlo. Si vuole accertarsi che questi attributi vengano rimossi da Azure AD. ![Attributi di estensione non validi](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Creare una nuova regola di sincronizzazione in ingresso e popolare la descrizione ![Descrizioni](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Creazione flussi di attributi di tipo **Expression** e con origine **AuthoritativeNull**. Il valore letterale **AuthoritativeNull** indica che il valore dovrebbe essere vuoto in MV anche se una regola di sincronizzazione di precedenza inferiore tenta di popolare il valore. ![Trasformazione per gli attributi di estensione](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Salvare la regola di sincronizzazione Avviare il **servizio di sincronizzazione**, trovare il connettore, selezionare **Esegui** e avviare una **sincronizzazione completa**. Questo passaggio consente di ricalcolare tutti i flussi degli attributi.
- Verificare che le modifiche richieste stiano per essere esportate mediante la ricerca dello spazio connettore. ![Eliminazione di gestione temporanea](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## Concetto avanzato

### Controllare il processo del flusso dell'attributo
Quando vengono configurate più regole di sincronizzazione in ingresso per lo stesso attributo metaverse, per determinare la regola prioritaria viene usata la precedenza. La regola di sincronizzazione con la precedenza più alta (valore numerico inferiore) fornisce il valore. Lo stesso concetto si applica alle regole in uscita. La regola di sincronizzazione con la precedenza più alta ha la priorità e fornisce il valore alla directory connessa.

In alcuni casi, invece di fornire un valore, la regola di sincronizzazione deve determinare il comportamento delle altre regole. In questo caso vengono usati alcuni valori letterali speciali.

Per le regole di sincronizzazione in ingresso è possibile usare il valore letterale **NULL** per indicare che nel flusso non è disponibile un valore da fornire. Un'altra regola con una precedenza inferiore può fornire un valore. Se nessuna regola ha fornito valori, l'attributo metaverse verrà rimosso. Per una regola in uscita, se **NULL** è il valore finale dopo l'elaborazione di tutte le regole di sincronizzazione, il valore viene rimosso nella directory connessa.

Il valore letterale **AuthoritativeNull** è simile a **NULL**, con la differenza che nessuna regola con una precedenza inferiore può fornire un valore.

Un flusso dell'attributo può anche usare **IgnoreThisFlow**. È simile a NULL in quanto indica che non è disponibile alcun valore da fornire, con la differenza che non rimuove un valore già esistente nella destinazione. È come se il flusso dell'attributi non sia mai stato presente.

Di seguito è fornito un esempio:

Nella regola *Out to AD - User Exchange hybrid* (In uscita ad AD - Utente Exchange ibrido) è possibile trovare il flusso seguente: `IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)` Questa espressione deve essere letta come: se la cassetta postale dell'utente si trova in Azure AD, trasmettere l'attributo da Azure AD ad Active Directory. In caso contrario, non ritrasmettere nulla ad Active Directory. In questo caso, il valore esistente rimarrà in Active Directory.

### ImportedValue
La funzione ImportedValue è diversa da tutte le altre funzioni, perché il nome dell'attributo deve essere racchiuso tra virgolette invece che tra parentesi quadre: `ImportedValue("proxyAddresses")`.

In genere, durante la sincronizzazione un attributo usa il valore previsto, anche se non è stato ancora esportato o se è stato visualizzato un errore durante l'esportazione ("livello massimo"). Una sincronizzazione in ingresso presuppone che un attributo che non ha ancora raggiunto una directory connessa la raggiungerà prima o poi. In alcuni casi è importante sincronizzare solo un valore confermato dalla directory connessa ("livello di importazione ologrammi e delta").

Un esempio di questa funzione è disponibile nella regola di sincronizzazione predefinita *In from AD – User Common from Exchange* (In ingresso da AD - Utente comune di Exchange). Nella regola per Exchange ibrido il valore aggiunto da Exchange online deve essere sincronizzato solo quando è stato confermato che l'esportazione del valore è riuscita: `proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## Passaggi successivi

Altre informazioni sulle [espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) usate per i flussi degli attributi.

Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0824_2016-->