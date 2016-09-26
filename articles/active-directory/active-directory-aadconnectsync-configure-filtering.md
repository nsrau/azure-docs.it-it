<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: configurare il filtro | Microsoft Azure"
	description="Illustra come configurare i filtri nel servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="andkjell;markvi"/>


# Servizio di sincronizzazione Azure AD Connect: Configurare il filtro
Con i filtri è possibile controllare quali oggetti visualizzare in Azure AD dalla directory locale. La configurazione predefinita considera tutti gli oggetti in tutti i domini delle foreste configurate. In generale, questa è la configurazione consigliata. Gli utenti finali che usano i carichi di lavoro di Office 365, come Exchange Online e Skype for Business, hanno a disposizione un elenco indirizzi globale completo per poter inviare messaggi di posta elettronica e chiamare chiunque. Con la configurazione predefinita usufruiranno della stessa esperienza che otterrebbero con un'implementazione locale di Exchange o Lync.

In alcuni casi, è necessario apportare alcune modifiche alla configurazione predefinita. Di seguito sono riportati alcuni esempi:

- Si prevede di usare la [topologia con più directory Azure AD](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). È necessario applicare un filtro per controllare gli oggetti da sincronizzare con una determinata directory di Azure AD.
- Si esegue una distribuzione pilota per Azure o Office 365 e si vuole solo un subset di utenti in Azure AD. In una piccola distribuzione pilota non è importante avere un elenco indirizzi globale completo per illustrare la funzionalità.
- Esiste un numero elevato di account del servizio e di altri account non personali che non devono essere presenti in Azure AD.
- Per motivi di conformità, non si elimina nessun account utente locale, li si disabilita soltanto, ma si vuole che in Azure AD siano presenti solo account attivi.

Questo articolo illustra come configurare i diversi metodi di filtro.

> [AZURE.IMPORTANT]Microsoft non supporta la modifica o l'utilizzo del servizio di sincronizzazione Azure AD Connect per azioni diverse da quelle formalmente documentate. Una qualsiasi di queste azioni può determinare uno stato non coerente o non supportato del servizio di sincronizzazione Azure AD Connect. Microsoft pertanto non può fornire il supporto tecnico per tali distribuzioni.

## Nozioni di base e note importanti
Nel servizio di sincronizzazione Azure AD Connect è possibile abilitare il filtro in qualsiasi momento. Se si inizia con una configurazione predefinita del servizio di sincronizzazione della directory e successivamente si configura il filtro, gli oggetti esclusi non verranno più sincronizzati con Azure AD. Come conseguenza di questa modifica, tutti gli oggetti in Azure AD sincronizzati in precedenza, ma poi esclusi, verranno eliminati in Azure AD.

Prima di iniziare ad apportare modifiche al filtro, [disabilitare l'attività pianificata](#disable-scheduled-task) in modo da non esportare accidentalmente modifiche di cui non si è ancora verificata la correttezza.

Poiché il filtro può rimuovere molti oggetti contemporaneamente, è opportuno verificare che i nuovi filtri siano corretti prima di iniziare a esportare le modifiche in Azure AD. Dopo avere completato i passaggi di configurazione, è consigliabile seguire i [passaggi di verifica](#apply-and-verify-changes) prima di esportare e apportare modifiche in Azure AD.

Per evitare di eliminare numerosi oggetti per errore, la funzionalità che [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) è attiva per impostazione predefinita. Se si eliminano molti oggetti a causa del filtro (500, per impostazione predefinita), è necessario seguire i passaggi di questo articolo per consentire alle eliminazioni di giungere ad Azure AD.

Se si usa una build precedente a quella di novembre 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), si apporta una modifica a una configurazione di filtro e si usa il servizio di sincronizzazione delle password, sarà necessario attivare una sincronizzazione completa di tutte le password al termine della configurazione. Per informazioni su come attivare una sincronizzazione completa di tutte le password, vedere [Attivare una sincronizzazione completa di tutte le password](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se si usa la versione 1.0.9125 o successiva, la normale azione di **sincronizzazione completa** calcola anche se le password devono essere sincronizzate, quindi questo ulteriore passaggio non è più necessario.

Se gli oggetti **utente** sono stati eliminati inavvertitamente in Azure AD a causa di un errore di filtro, è possibile ricrearli in Azure AD rimuovendo le configurazioni di filtro e quindi sincronizzando di nuovo le directory. Questa azione ripristina gli utenti dal Cestino in Azure AD. Non è però possibile annullare l'eliminazione di altri tipi di oggetto. Se, ad esempio, si elimina accidentalmente un gruppo di sicurezza usato per inserire una risorsa in un ACL, il gruppo e gli ACL non possono essere recuperati.

Azure AD Connect elimina solo gli oggetti che ha considerato come presenti nell'ambito. Se in Azure AD sono presenti oggetti creati da un altro motore di sincronizzazione e che non sono compresi nell'ambito, l'aggiunta del filtro non li rimuoverà. Se, ad esempio, si inizia con un server DirSync che ha creato una copia completa dell'intera directory in Azure AD e si installa un nuovo server di sincronizzazione di Azure AD Connect parallelamente al filtro abilitato all'inizio, gli oggetti aggiuntivi creati da DirSync non vengono rimossi.

Quando si installa o si esegue l'aggiornamento a una versione più recente di Azure AD Connect, vengono conservate le configurazioni del filtro. Prima di eseguire il primo ciclo di sincronizzazione, la procedura consigliata è verificare sempre che la configurazione non sia stata modificata inavvertitamente dopo un aggiornamento a una versione più recente.

Se è presente più di una foresta, le configurazioni del filtro descritte in questo argomento devono essere applicate a ogni foresta, supponendo che si voglia la stessa configurazione per tutte.

### Disabilitare l'attività pianificata
Per disabilitare l'utilità di pianificazione predefinita che attiva un ciclo di sincronizzazione ogni 30 ore, seguire questi passaggi:

1. Passare a un prompt di PowerShell.
2. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $False` per disabilitare l'utilità di pianificazione.
3. Apportare le modifiche indicate in questo argomento.
4. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $True` per abilitare di nuovo l'utilità di pianificazione.

**Se si usa una build di Azure AD Connect precedente a 1.1.105.0** Per disabilitare l'attività pianificata che attiva un ciclo di sincronizzazione ogni 3 ore, seguire questi passaggi:

1. Avviare **Utilità di pianificazione** dal menu Start.
2. In **Libreria Utilità di pianificazione** trovare l'attività denominata **Azure AD Sync Scheduler**, fare clic con il pulsante destro del mouse e scegliere **Disabilita**. ![Utilità di pianificazione attività](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)
3. È ora possibile apportare modifiche alla configurazione ed eseguire manualmente il motore di sincronizzazione dalla console **Synchronization Service Manager**.

Dopo avere completato tutte le modifiche del filtro, non dimenticare di tornare indietro e di **abilitare** di nuovo l'attività.

## Opzioni di filtro
Allo Strumento di sincronizzazione della directory è possibile applicare i tipi di configurazione di filtro descritti di seguito:

- [**Basato su gruppo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): il filtro basato su un singolo gruppo può essere configurato solo durante l'installazione iniziale usando l'installazione guidata. Non sono disponibili altri approfondimenti in questo argomento.

- [**Basato su dominio**](#domain-based-filtering): questa opzione permette di selezionare i domini che verranno sincronizzati con Azure AD. Consente anche di aggiungere e rimuovere domini dalla configurazione del motore di sincronizzazione se si apportano modifiche all'infrastruttura locale dopo avere installato il servizio di sincronizzazione Azure AD Connect.

- [**Organizational-Unit–based**](#organizational-unitbased-filtering) (Basato su unità organizzativa): questa opzione di filtro permette di selezionare le unità organizzative che verranno sincronizzate con Azure AD. Questa opzione è disponibile in tutti i tipi di oggetto nelle unità organizzative selezionate.

- [**Basato su attributo**](#attribute-based-filtering): questa opzione permette di filtrare gli oggetti in base ai valori degli attributi negli oggetti. È anche possibile avere filtri diversi a seconda del tipo di oggetto.

È possibile usare più opzioni di filtro contemporaneamente. È possibile, ad esempio, usare il filtro basato sulle unità organizzative per includere solo gli oggetti in un'unità organizzativa e nello stesso tempo un filtro basato su un attributo ora per filtrare gli oggetti con maggiore precisione. Quando si usano più metodi di filtro, viene usato un operatore AND logico tra i filtri.

## Filtro basato su dominio
Questa sezione illustra i passaggi necessari per configurare il filtro basato su dominio. Se sono stati aggiunti o rimossi domini nella foresta dopo avere installato Azure AD Connect, è necessario aggiornare anche la configurazione del filtro.

Il modo migliore per modificare i filtri basati su dominio consiste nell'eseguire l'installazione guidata e cambiare i [filtri basati su dominio e su unità organizzativa](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata automatizza tutte le attività documentate in questo argomento.

È consigliabile seguire questa procedura solo se per qualche motivo non è possibile eseguire l'installazione guidata.

La configurazione del filtro basato su dominio prevede questi passaggi:

- [Selezionare i domini](#select-domains-to-be-synchronized) da includere nella sincronizzazione.
- Per ogni dominio aggiunto e rimosso, modificare i [profili di esecuzione](#update-run-profiles).
- [Applicare e verificare le modifiche](#apply-and-verify-changes)

### Selezionare i domini da sincronizzare
**Per impostare il filtro basato su dominio, seguire questa procedura:**

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.
2. Avviare **Servizio di sincronizzazione** dal menu Start.
3. Selezionare **Connectors** e nell'elenco **Connectors** selezionare il connettore con il tipo **Active Directory Domain Services**. In **Actions** (Azioni) selezionare **Properties** (Proprietà). ![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
4. Fare clic su **Configure Directory Partitions**.
5. Nell'elenco **Select directory partitions** selezionare e deselezionare i domini in base alle esigenze. Verificare che siano selezionate solo le partizioni da sincronizzare. ![Partitions](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Se è stata modificata l'infrastruttura AD locale e sono stati aggiunti o rimossi domini nella foresta, fare clic sul pulsante **Refresh** (Aggiorna) per ottenere un elenco aggiornato. Quando si esegue l'aggiornamento, vengono richieste le credenziali. Specificare credenziali con accesso in lettura alla directory di Active Directory locale. Non deve essere necessariamente l'utente inserito in precedenza nella finestra di dialogo. ![Aggiornamento necessario](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)
6. Al termine, chiudere la finestra di dialogo **Properties** facendo clic su **OK**. Se sono stati rimossi domini dalla foresta, viene visualizzato un messaggio che indica che è stato rimosso un dominio e che verrà eseguita la pulizia della configurazione.
7. Continuare a modificare i [profili di esecuzione](#update-run-profiles).

### Aggiornare i profili di esecuzione
Se il filtro basato su dominio è stato aggiornato, è necessario aggiornare anche i profili di esecuzione.

1. Nell'elenco **Connectors** verificare che sia selezionato il connettore modificato nel passaggio precedente. In **Actions** (Azioni) selezionare **Configure Run Profiles** (Configura profili di esecuzione). ![Profili di esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)

È necessario modificare i profili seguenti:

- Importazione completa
- Sincronizzazione completa
- Importazione differenziale
- Sincronizzazione differenziale
- Esporta

Per ognuno dei cinque profili, eseguire questi passaggi per ogni dominio **aggiunto**:

1. Selezionare il profilo di esecuzione e fare clic su **New Step**.
2. Nell'elenco a discesa **Type** della pagina **Configure Step** selezionare il tipo di passaggio con lo stesso nome del profilo da configurare. Quindi fare clic su **Avanti**. ![Profili di esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)
3. Nell'elenco a discesa **Partition** della pagina **Connector Configuration** selezionare il nome del dominio aggiunto al filtro basato su dominio. ![Profili di esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)
4. Per chiudere la finestra di dialogo **Configure Run Profile**, fare clic su **Finish**.

Per ognuno dei cinque profili, eseguire questi passaggi per ogni dominio **rimosso**:

1. Selezionare il profilo di esecuzione.
2. Se l'opzione **Value** dell'attributo **Partition** è un GUID, selezionare il passaggio di esecuzione e fare clic su **Delete Step**. ![Profili di esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)

Il risultato corretto prevede che ogni dominio da sincronizzare sia elencato come passaggio in ogni profilo di esecuzione.

Per chiudere la finestra di dialogo **Configure Run Profiles**, fare clic su **OK**.

- Per completare la configurazione, [applicare e verificare le modifiche](#apply-and-verify-changes).

## Filtro basato su unità organizzativa
Il modo migliore per modificare i filtri basati su unità organizzativa consiste nell'eseguire l'installazione guidata e cambiare i [filtri basati su dominio e su unità organizzativa](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata automatizza tutte le attività documentate in questo argomento.

È consigliabile seguire questa procedura solo se per qualche motivo non è possibile eseguire l'installazione guidata.

**Per configurare il filtro basato sull'unità organizzativa, seguire questa procedura:**

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.
2. Avviare **Servizio di sincronizzazione** dal menu Start.
3. Selezionare **Connectors** e nell'elenco **Connectors** selezionare il connettore con il tipo **Active Directory Domain Services**. In **Actions** (Azioni) selezionare **Properties** (Proprietà). ![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
4. Fare clic su **Configure Directory Partitions**, selezionare il dominio da configurare e quindi fare clic su **Containers**.
5. Quando viene richiesto, specificare credenziali con accesso in lettura alla directory di Active Directory locale. Non deve essere necessariamente l'utente inserito in precedenza nella finestra di dialogo.
6. Nella finestra di dialogo **Select Containers** deselezionare le unità organizzative da non sincronizzare con la directory cloud e quindi fare clic su **OK**. ![Unità organizzativa](./media/active-directory-aadconnectsync-configure-filtering/ou.png)
  - Per sincronizzare i computer Windows 10 con Azure AD, è necessario che il contenitore **Computers** sia selezionato. Se i computer aggiunti a un dominio si trovano in altre unità organizzative, verificare che siano selezionate.
  - Se sono presenti più foreste con trust, il contenitore **ForeignSecurityPrincipals** deve essere selezionato. Ciò consente di risolvere l'appartenenza al gruppo di sicurezza tra foreste.
  - Se è stata abilitata la funzionalità di writeback dei dispositivi, l'unità organizzativa **RegisteredDevices** deve essere selezionata. Se si usa un'altra funzionalità di writeback, ad esempio il writeback dei gruppi, verificare che queste posizioni siano selezionate.
  - Selezionare le altre unità organizzative in cui si trovano Users, iNetOrgPersons, Groups, Contacts e Computers. Nella figura si trovano tutti nell'unità organizzativa ManagedObjects.
7. Al termine, chiudere la finestra di dialogo **Properties** facendo clic su **OK**.
8. Per completare la configurazione, [applicare e verificare le modifiche](#apply-and-verify-changes).

## Filtro basato su attributo
Assicurarsi di usare la build di novembre 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) o successive per eseguire correttamente questi passaggi.

Il filtro basato sugli attributi è il modo più flessibile per filtrare gli oggetti. È possibile usare la funzionalità di [provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) per controllare praticamente ogni aspetto dei casi in cui un oggetto deve essere sincronizzato con Azure AD.

Il filtro può essere applicato sia in [ingresso](#inbound-filtering) da Active Directory al metaverse sia in [uscita](#outbound-filtering) dal metaverse ad Azure AD. È consigliabile applicare il filtro in ingresso perché è il più facile da gestire. Il filtro in uscita dovrebbe essere usato solo se è necessario per unire oggetti da più foreste prima che venga eseguita la valutazione.

### Filtri in ingresso
I filtri in ingresso usano la configurazione predefinita degli oggetti diretti ad Azure AD secondo la quale è necessario che l'attributo metaverse cloudFiltered non sia impostato su un valore da sincronizzare. Se il valore di questo attributo è impostato su **True**, l'oggetto non viene sincronizzato. Il valore non deve essere impostato su **False** in base alla progettazione. Per verificare che altre regole possano fornire un valore, questo attributo dovrebbe avere solo i valori **True** o **NULL** (assente).

Per determinare gli oggetti da sincronizzare o meno, nel filtro in ingresso viene usata la funzionalità di **ambito**, dove si apportano le modifiche necessarie per rispettare i requisiti dell'organizzazione. Il modulo di ambito include un **gruppo** e una **clausola** per determinare se una regola di sincronizzazione deve essere inclusa nell'ambito. Un **gruppo** contiene una o più **clausole**. Tra più clausole viene inserito un operare AND logico e tra più gruppi un operatore OR logico.

Ecco un esempio: ![Scope](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Il risultato sarà **(department = IT) OR (department = Sales AND c = US)**.

Negli esempi e nei passaggi seguenti viene usato l'oggetto utente come esempio, ma è possibile usarlo per tutti i tipi di oggetto.

Negli esempi seguenti i valori di precedenza iniziano con 500 per essere certi che vengano valutati dopo le regole predefinite. Una precedenza più bassa corrisponde a un valore numerico più elevato.

#### Filtro negativo (non sincronizzare gli elementi indicati)
Nell'esempio seguente vengono filtrati (non sincronizzati) tutti gli utenti per i quali il valore di **extensionAttribute15** è **NoSync**.

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.
2. Avviare **Synchronization Rules Editor** dal menu Start.
3. Assicurarsi che sia selezionata l'opzione **Inbound** e fare clic su **Add New Rule**.
4. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User DoNotSyncFilter*". Selezionare la foresta corretta, **User** per **CS object type** e **Person** per **MV object type**. In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci) e nel tipo di precedenza immettere un valore che non sia attualmente usato da un'altra regola di sincronizzazione, ad esempio 500, quindi fare clic su **Next** (Avanti). ![Descrizione in ingresso 1](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)
5. In **Scoping filter** (Filtro di ambito) fare clic su **Add Group** (Aggiungi gruppo), quindi su **Add Clause** (Aggiungi clausola) e in attribute selezionare **ExtensionAttribute15**. Verificare che Operator sia impostato su **EQUAL** e digitare il valore **NoSync** nella casella Value. Fare clic su **Next**. ![Ambito in ingresso 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)
6. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
7. Fare clic su **Add Transformation** (Aggiungi trasformazione), impostare **FlowType** su **Constant**, selezionare l'attributo di destinazione **cloudFiltered** e nella casella di testo Source (Origine) digitare **True**. Fare clic su **Add** per salvare la regola. ![Trasformazione in ingresso 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Per completare la configurazione, [applicare e verificare le modifiche](#apply-and-verify-changes).

#### Filtro positivo (sincronizzare solo gli elementi indicati)
La creazione di un filtro positivo può essere più complessa perché è necessario considerare anche gli oggetti la cui sincronizzazione non è scontata, ad esempio le sale riunioni.

L'opzione di filtro positivo richiede due regole di sincronizzazione. Una o più con l'ambito corretto degli oggetti da sincronizzare e una seconda regola di sincronizzazione catch-all che filtra tutti gli oggetti non ancora identificati come oggetto da sincronizzare.

Nell'esempio seguente vengono sincronizzati solo gli oggetti per i quali l'attributo department ha il valore **Sales**.

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.
2. Avviare **Synchronization Rules Editor** dal menu Start.
3. Assicurarsi che sia selezionata l'opzione **Inbound** e fare clic su **Add New Rule**.
4. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User Sales sync*". Selezionare la foresta corretta, **User** per **CS object type** e **Person** per **MV object type**. In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci) e nel tipo di precedenza immettere un valore che non sia attualmente usato da un'altra regola di sincronizzazione, ad esempio 501, quindi fare clic su **Next** (Avanti). ![Destinazione in ingresso 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)
5. In **Scoping filter** (Filtro di ambito) fare clic su **Add Group** (Aggiungi gruppo), quindi su **Add Clause** (Aggiungi clausola) e in Attribute selezionare **department**. Verificare che Operator sia impostato su **EQUAL** e digitare il valore **Sales** nella casella Value. Fare clic su **Next**. ![Ambito in ingresso 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)
6. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
7. Fare clic su **Add Transformation** (Aggiungi trasformazione), impostare **FlowType** su **Constant**, selezionare l'attributo di destinazione **cloudFiltered** e nella casella di testo Source digitare **False**. Fare clic su **Add** per salvare la regola. ![Trasformazione in ingresso 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Questo è un caso particolare in cui cloudFiltered viene impostato in modo esplicito su False.

	Ora è necessario creare la regola di sincronizzazione catch-all.

8. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User Catch-all filter*". Selezionare la foresta corretta, **User** per **CS object type** e **Person** per **MV object type**. In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci) e nel tipo di precedenza immettere un valore che non sia attualmente usato da un'altra regola di sincronizzazione, ad esempio 600. È stato selezionato un valore di precedenza superiore (precedenza più bassa) di quello della precedente regola di sincronizzazione, ma è anche stato lasciato spazio per poter aggiungere altre regole di sincronizzazione filtro in seguito quando sarà necessario avviare la sincronizzazione di altri reparti. Fare clic su **Next**. ![Descrizione in ingresso 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
9. Lasciare vuoto **Scoping filter** e fare clic su **Next**. Un filtro vuoto indica che la regola deve essere applicata a tutti gli oggetti.
10. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
11. Fare clic su **Add Transformation** (Aggiungi trasformazione), impostare **FlowType** su **Constant**, selezionare l'attributo di destinazione **cloudFiltered** e nella casella di testo Source (Origine) digitare **True**. Fare clic su **Add** per salvare la regola. ![Trasformazione in ingresso 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
12. Per completare la configurazione, [applicare e verificare le modifiche](#apply-and-verify-changes).

Se necessario, è possibile creare altre regole del primo tipo per includere ancora più oggetti nella sincronizzazione.

### Filtri in uscita
In alcuni casi, è necessario applicare il filtro solo dopo aver unito gli oggetti a metaverse. Ad esempio, potrebbe essere opportuno cercare l'attributo mail nella foresta di risorse e l'attributo userPrincipalName nella foresta di account per determinare se è necessario sincronizzare un oggetto. In questi casi, vengono creati i filtri nella regola in uscita.

In questo esempio il filtro viene modificato in modo che vengano sincronizzati solo gli utenti in cui sia mail che userPrincipalName terminano con @contoso.com:

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.
2. Avviare **Synchronization Rules Editor** dal menu Start.
3. In **Rules Type** (Tipo di regola) fare clic su **Outbound** (In uscita).
4. Trovare la regola denominata **Out to AAD - User Join SOAInAD** (In uscita ad AAD - Aggiunta utente). Fare clic su **Modifica**.
5. Nel popup selezionare **Yes** (Sì) per creare una copia della regola.
6. Nella pagina **Description** (Descrizione) sostituire la precedenza con un valore non usato, ad esempio 50.
7. Fare clic su **Scoping filter** (Filtro di ambito) a sinistra. Fare clic su **Add clause** (Aggiungi clausola) e in Attribute selezionare **mail**, in Operator selezionare **ENDSWITH** e in Value digitare **@contoso.com**. Fare clic su **Add clause** (Aggiungi clausola) e in Attribute selezionare **userPrincipalName**, in Operator selezionare **ENDSWITH** e in Value digitare **@contoso.com**.
8. Fare clic su **Save**.
9. Per completare la configurazione, [Applicare e verificare le modifiche](#apply-and-verify-changes).

## Applicare e verificare le modifiche
Dopo avere apportato le modifiche alla configurazione, è necessario applicarle agli oggetti già presenti nel sistema. È possibile che gli oggetti attualmente non presenti nel motore di sincronizzazione debbano essere elaborati e che il motore di sincronizzazione debba leggere di nuovo il sistema di origine per verificarne il contenuto.

Se la configurazione è stata modificata usando il filtro basato su **dominio** o su **unità organizzativa**, è necessario eseguire un'**importazione completa** seguita dalla **sincronizzazione differenziale**.

Se la configurazione è stata modificata usando il filtro basato su **attributo**, è necessario eseguire la **sincronizzazione completa**.

Eseguire questa procedura:

1. Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu Start.
2. Selezionare **Connectors** (Connettori) e nell'elenco **Connectors** (Connettori) selezionare il connettore in cui prima è stata apportata una modifica alla configurazione. In **Actions** (Azioni) selezionare **Run** (Esegui). ![Esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)
3. In **Run profiles** (Esegui profili) selezionare l'operazione indicata nella sezione precedente. Se è necessario eseguire due azioni, eseguire la seconda al termine della prima, ovvero quando nella colonna **State** (Stato) viene visualizzato il valore **Idle** (Inattivo) per il connettore selezionato.

Dopo la sincronizzazione, tutte le modifiche vengono inserite temporaneamente per essere esportate. Prima di apportare effettivamente le modifiche in Azure AD, è opportuno verificare che siano tutte corrette.

1. Avviare un prompt dei comandi e passare a `%Program Files%\Microsoft Azure AD Sync\bin`
2. Eseguire: `csexport "Name of Connector" %temp%\export.xml /f:x` Il nome del connettore si trova nel servizio di sincronizzazione. Il nome sarà simile a "contoso.com - AAD" per Azure AD.
3. Eseguire: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. A questo punto si avrà un file denominato export.csv in %temp%, che può essere esaminato in Microsoft Excel. Questo file contiene tutte le modifiche in fase di esportazione.
5. Apportare le modifiche necessarie ai dati o alla configurazione ed eseguire di nuovo questi passaggi (importazione, sincronizzazione e verifica) finché le modifiche che verranno esportate non saranno quelle previste.

Quando si è soddisfatti, esportare le modifiche in Azure AD.

1. Selezionare **Connectors** (Connettori) e nell'elenco **Connectors** (Connettori) selezionare Azure AD Connector. In **Actions** (Azioni) selezionare **Run** (Esegui).
2. In **Run profiles** (Esegui profili) selezionare **Export** (Esporta).
3. Se le modifiche della configurazione eliminano molti oggetti, viene visualizzato un errore nell'esportazione se il numero è superiore alla soglia configurata (per impostazione predefinita, 500). Se viene visualizzato questo errore, è necessario disabilitare temporaneamente la funzionalità che [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Ora è possibile abilitare di nuovo l'utilità di pianificazione.

1. Avviare l'**Utilità di pianificazione** dal menu Start.
2. In **Libreria Utilità di pianificazione** trovare l'attività denominata **Azure AD Sync Scheduler**, fare clic con il pulsante destro del mouse e scegliere **Abilita**.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0914_2016-->