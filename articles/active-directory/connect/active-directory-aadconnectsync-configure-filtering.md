---
title: 'Servizio di sincronizzazione Azure AD Connect: configurare il filtro | Documentazione Microsoft'
description: Illustra come configurare i filtri nel servizio di sincronizzazione Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 0b5bdb5036024cc0e05b3845e0073b2ea66a0a73
ms.openlocfilehash: 9f615c3936cf3207ace787dd7293f94d2b760149
ms.lasthandoff: 02/21/2017

---

# <a name="azure-ad-connect-sync-configure-filtering"></a>Servizio di sincronizzazione Azure AD Connect: Configurare il filtro
L'applicazione di un filtro consente di controllare quali oggetti vengono visualizzati in Azure Active Directory (Azure AD) dalla directory locale. La configurazione predefinita considera tutti gli oggetti in tutti i domini delle foreste configurate. In generale, questa è la configurazione consigliata. Gli utenti che usano i carichi di lavoro di Office 365, come Exchange Online e Skype for Business, hanno a disposizione un elenco indirizzi globale completo per inviare messaggi di posta elettronica e chiamare chiunque. Con la configurazione predefinita possono usufruire della stessa esperienza resa disponibile da un'implementazione locale di Exchange o Lync.

In alcuni casi è tuttavia necessario apportare alcune modifiche alla configurazione predefinita. Di seguito sono riportati alcuni esempi:

* Si prevede di usare la [topologia con più directory di Azure AD](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). In tal caso è necessario applicare un filtro per controllare quali oggetti sincronizzare con una determinata directory di Azure AD.
* Si esegue una distribuzione pilota per Azure o Office 365 e si vuole solo un subset di utenti in Azure AD. In una distribuzione pilota di dimensioni ridotte la disponibilità di un elenco indirizzi globale completo per illustrare la funzionalità non è importante.
* Si dispone di molti account del servizio e di altri account non personali non desiderati in Azure AD.
* Per motivi di conformità, non si eliminano account utente locali, li si disabilita soltanto, ma si vuole che in Azure AD siano presenti solo account attivi.

Questo articolo illustra come configurare i diversi metodi di filtro.

> [!IMPORTANT]
> Microsoft non supporta la modifica o l'uso del servizio di sincronizzazione Azure AD Connect al di fuori delle azioni descritte in modo formale. Ognuna di queste azioni potrebbe provocare uno stato incoerente o non supportato del sevizio di sincronizzazione Azure AD Connect. Microsoft pertanto non offre il supporto tecnico per distribuzioni di questo tipo.

## <a name="basics-and-important-notes"></a>Nozioni di base e note importanti
Nel servizio di sincronizzazione Azure AD Connect è possibile abilitare il filtro in qualsiasi momento. Se si inizia con una configurazione predefinita del servizio di sincronizzazione della directory e successivamente si configura il filtro, gli oggetti esclusi non verranno più sincronizzati con Azure AD. A causa di questa modifica, tutti gli oggetti in Azure AD sincronizzati in precedenza, ma successivamente esclusi, verranno eliminati in Azure AD.

Prima di iniziare ad apportare modifiche al filtro, [disabilitare l'attività pianificata](#disable-scheduled-task) in modo da non esportare accidentalmente modifiche di cui non si è ancora verificata la correttezza.

Poiché il filtro può rimuovere molti oggetti contemporaneamente, prima di iniziare a esportare le modifiche in Azure AD è opportuno verificare che i nuovi filtri siano corretti. Dopo aver completato i passaggi di configurazione, è consigliabile seguire i [passaggi di verifica](#apply-and-verify-changes) prima di esportare e apportare modifiche in Azure AD .

Per evitare che si elimino numerosi oggetti per errore, la funzionalità che [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) è attiva per impostazione predefinita. Se si eliminano molti oggetti a causa del filtro (500, per impostazione predefinita), è necessario seguire i passaggi di questo articolo per consentire alle eliminazioni di giungere ad Azure AD.

Se si usa una build precedente a quella di novembre 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), si apporta una modifica a una configurazione di filtro e si usa il servizio di sincronizzazione delle password, è necessario attivare una sincronizzazione completa di tutte le password al termine della configurazione. Per informazioni su come attivare una sincronizzazione completa di tutte le password, vedere [Attivare una sincronizzazione completa di tutte le password](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se si usa la build 1.0.9125 o versione successiva, la normale azione di **sincronizzazione completa** consente anche di stabilire se le password devono essere sincronizzate e se questo passaggio aggiuntivo non è più necessario.

Se in Azure AD sono stati eliminati inavvertitamente oggetti **utente** a causa di un errore di filtro, è possibile ricrearli in Azure AD rimuovendo le configurazioni di filtro e successivamente sincronizzare nuovamente le directory. Questa azione ripristina gli utenti dal Cestino in Azure AD. Non è tuttavia possibile annullare l'eliminazione di altri tipi di oggetto. Se ad esempio si elimina accidentalmente un gruppo di sicurezza usato per inserire una risorsa in un elenco di controllo di accesso, il gruppo e gli elenchi di controllo di accesso relativi non possono essere ripristinati.

Azure AD Connect elimina solo gli oggetti considerati come presenti nell'ambito. Se in Azure AD sono presenti oggetti creati da un altro motore di sincronizzazione, ma non compresi nell'ambito, l'aggiunta del filtro non ne determina la rimozione. Se ad esempio si inizia con un server DirSync che ha creato una copia completa dell'intera directory in Azure AD e si installa un nuovo server del servizio di sincronizzazione Azure AD Connect contemporaneamente al filtro abilitato all'inizio, Azure AD Connect non rimuove gli oggetti aggiuntivi creati da DirSync.

Quando si installa o si esegue l'aggiornamento a una versione più recente di Azure AD Connect, vengono conservate le configurazioni del filtro. Prima di eseguire il primo ciclo di sincronizzazione, è consigliabile verificare sempre che la configurazione non sia stata modificata inavvertitamente dopo un aggiornamento a una versione più recente.

Se si dispone di più di una foresta, è necessario applicare a ognuna le configurazioni di filtro descritte in questo argomento, presupponendo che si desideri la stessa configurazione per ogni foresta.

### <a name="disable-the-scheduled-task"></a>Disabilitare l'attività pianificata
Per disabilitare l'utilità di pianificazione predefinita che attiva un ciclo di sincronizzazione ogni 30 ore, seguire questi passaggi:

1. Passare a un prompt di PowerShell.
2. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $False` per disabilitare l'utilità di pianificazione.
3. Apportare le modifiche descritte in questo articolo.
4. Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $True` per abilitare di nuovo l'utilità di pianificazione.

**Se si usa una build di Azure AD Connect precedente a 1.1.105.0**  
Per disabilitare l'attività pianificata che attiva un ciclo di sincronizzazione ogni tre, seguire questa procedura:

1. Avviare **Utilità di pianificazione** dal menu **Start**.
2. In **Libreria Utilità di pianificazione** trovare l'attività denominata **Azure AD Sync Scheduler**, fare clic con il pulsante destro del mouse e scegliere **Disabilita**.  
   ![Utilità di pianificazione](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. È ora possibile apportare modifiche alla configurazione ed eseguire manualmente il motore di sincronizzazione dalla console **Synchronization Service Manager**.

Dopo aver completato tutte le modifiche del filtro, tornare indietro e **abilitare** nuovamente l'attività.

## <a name="filtering-options"></a>Opzioni di filtro
Allo strumento di sincronizzazione della directory è possibile applicare i tipi di configurazione di filtro seguenti:

* [**Basato su gruppo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): il filtro basato su un singolo gruppo può essere configurato solo durante l'installazione iniziale usando l'installazione guidata. L'argomento non viene trattato ulteriormente in questo articolo.
* [**Basato su domini**](#domain-based-filtering): consente di selezionare i domini da sincronizzare con Azure AD. È anche possibile aggiungere e rimuovere domini dalla configurazione del motore di sincronizzazione quando si apportano modifiche all'infrastruttura locale dopo aver installato il servizio di sincronizzazione Azure AD Connect.
* [**Basato su unità organizzative**](#organizational-unitbased-filtering): consente di selezionare le unità organizzative da sincronizzare con Azure AD. Questa opzione è disponibile in tutti i tipi di oggetto nelle unità organizzative selezionate.
* [**Basato su attributi**](#attribute-based-filtering): consente di filtrare gli oggetti in base ai valori di attributo relativi. È anche possibile avere filtri diversi a seconda del tipo di oggetto.

È possibile usare più opzioni di filtro contemporaneamente. È possibile ad esempio usare il filtro basato su unità organizzative per includere gli oggetti solo in un'unità organizzativa. Allo stesso tempo, è possibile usare il filtro basato su attributi per filtrare ulteriormente gli oggetti. Quando si usano più metodi di filtro, viene usato un operatore AND logico tra i filtri.

## <a name="domain-based-filtering"></a>Filtro basato su dominio
Questa sezione illustra i passaggi necessari per configurare il filtro basato su dominio. Se sono stati aggiunti o rimossi domini nella foresta dopo l'installazione di Azure AD Connect, è necessario aggiornare anche la configurazione del filtro.

Il modo migliore per modificare i filtri basati su domini consiste nell'eseguire l'installazione guidata e cambiare i [filtri basati su domini e su unità organizzative](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata consente di eseguire automaticamente tutte le attività descritte in questo argomento.

È consigliabile seguire questa procedura solo se non è possibile eseguire l'installazione guidata.

La configurazione del filtro basato su dominio prevede questi passaggi:

1. [Selezionare i domini](#select-domains-to-be-synchronized) da includere nella sincronizzazione.
2. Per ogni dominio aggiunto e rimosso, modificare i [profili di esecuzione](#update-run-profiles).
3. [Applicare e verificare le modifiche](#apply-and-verify-changes)

### <a name="select-the-domains-to-be-synchronized"></a>Selezionare i domini da sincronizzare
Per impostare il filtro basato su dominio, seguire questa procedura:

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu **Start**.
3. Selezionare **Connectors** (Connettori) e nell'elenco **Connectors** selezionare il connettore con il tipo **Active Directory Domain Services**. In **Actions** (Azioni) selezionare **Properties** (Proprietà).  
   ![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Fare clic su **Configure Directory Partitions**.
5. Nell'elenco **Select directory partitions** (Selezionare le partizioni di directory) selezionare e deselezionare i domini in base alle esigenze. Verificare che siano selezionate solo le partizioni da sincronizzare.  
   ![Partitions](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Se è stata modificata l'infrastruttura Active Directory locale e se sono stati aggiunti o rimossi domini dalla foresta, fare clic sul pulsante **Refresh** (Aggiorna) per ottenere un elenco aggiornato. Quando si esegue l'aggiornamento, vengono richieste le credenziali. Specificare le credenziali con accesso in lettura a Windows Server Active Directory. Tali credenziali non devono necessariamente appartenere all'utente già inserito nella finestra di dialogo.  
   ![Aggiornamento necessario](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Al termine, fare clic su **OK** per chiudere la finestra di dialogo **Properties** (Proprietà). Se sono stati rimossi domini dalla foresta, viene visualizzato un messaggio in cui è indicato che è stato rimosso un dominio e che verrà eseguita la pulizia della configurazione.
7. Continuare a modificare i [profili di esecuzione](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Aggiornare i profili di esecuzione
Se il filtro basato su domini è stato aggiornato, è necessario aggiornare anche i profili di esecuzione.

1. Nell'elenco **Connectors** (Connettori) verificare che sia selezionato il connettore modificato nel passaggio precedente. In **Actions** (Azioni) selezionare **Configure Run Profiles** (Configura profili di esecuzione).  
   ![Profili di esecuzione del connettore 1](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Trovare e identificare i profili seguenti:
    * Importazione completa
    * Sincronizzazione completa
    * Importazione differenziale
    * Sincronizzazione differenziale
    * Esporta
3. Per ogni profilo modificare i domini **aggiunti** e **rimossi**.
    1. Per ognuno dei cinque profili, seguire questa procedura per ogni dominio **aggiunto**:
        1. Selezionare il profilo di esecuzione e fare clic su **New Step**.
        2. Nel menu a discesa **Type** (Tipo) della pagina **Configure Step** (Configura passaggio) selezionare il tipo di passaggio con lo stesso nome del profilo da configurare. Quindi fare clic su **Next**.  
        ![Profili di esecuzione del connettore 2](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. Nell'elenco a discesa **Partition** (Partizione) della pagina **Connector Configuration** (Configurazione connettore) selezionare il nome del dominio aggiunto al filtro basato su domini.  
        ![Profili di esecuzione del connettore 3](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Per chiudere la finestra di dialogo **Configure Run Profile**, fare clic su **Finish**.
    2. Per ognuno dei cinque profili, seguire questa procedura per ogni dominio **rimosso**:
        1. Selezionare il profilo di esecuzione.
        2. Se l'opzione **Value** dell'attributo **Partition** è un GUID, selezionare il passaggio di esecuzione e fare clic su **Delete Step**.  
        ![Profili di esecuzione del connettore 4](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Verificare la modifica. Ogni dominio da sincronizzare deve essere elencato come un passaggio in ogni profilo di esecuzione.
4. Per chiudere la finestra di dialogo **Configure Run Profiles**, fare clic su **OK**.
5.  Per completare la configurazione, è necessario eseguire un'operazione **Full import** (Importazione completa) e un'operazione **Delta sync** (Sincronizzazione delta). Per continuare, vedere la sezione [Applicare e verificare le modifiche](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtro basato su unità organizzative
Il modo migliore per modificare i filtri basati su unità organizzative consiste nell'eseguire l'installazione guidata e cambiare i [filtri basati su domini e su unità organizzative](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L'installazione guidata consente di eseguire automaticamente tutte le attività descritte in questo argomento.

È consigliabile seguire questa procedura solo se non è possibile eseguire l'installazione guidata.

Per configurare il filtro basato su unità organizzative, seguire questa procedura:

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu **Start**.
3. Selezionare **Connectors** (Connettori) e nell'elenco **Connectors** selezionare il connettore con il tipo **Active Directory Domain Services**. In **Actions** (Azioni) selezionare **Properties** (Proprietà).  
   ![Proprietà del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Fare clic su **Configure Directory Partitions** (Configurare le partizioni di directory) selezionare il dominio da configurare e quindi fare clic su **Containers** (Contenitori).
5. Quando richiesto, specificare le credenziali con accesso in lettura all'istanza di Active Directory locale. Tali credenziali non devono necessariamente appartenere all'utente già inserito nella finestra di dialogo.
6. Nella finestra di dialogo **Select Containers** deselezionare le unità organizzative da non sincronizzare con la directory cloud e quindi fare clic su **OK**.  
   ![Unità organizzative nella finestra di dialogo Select Containers (Seleziona contenitori)](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * Per sincronizzare i computer Windows 10 con Azure AD, è necessario che il contenitore **Computers** sia selezionato. Se i computer aggiunti a un dominio si trovano in altre unità organizzative, verificare che queste ultime siano selezionate.
   * Se sono presenti più foreste con trust, il contenitore **ForeignSecurityPrincipals** deve essere selezionato. Ciò consente di risolvere l'appartenenza al gruppo di sicurezza tra foreste.
   * Se è stata abilitata la funzionalità di writeback dei dispositivi, è necessario che l'unità organizzativa **RegisteredDevices** sia selezionata. Se si usa un'altra funzionalità di writeback, ad esempio il writeback dei gruppi, verificare che queste posizioni siano selezionate.
   * Selezionare le altre unità organizzative in cui si trovano Users, iNetOrgPersons, Groups, Contacts e Computers. Nella figura tutte le unità organizzative si trovano nell'unità organizzativa ManagedObjects.
   * Se si usa il filtro basato sui gruppi, è necessario includere l'unità organizzativa in cui si trova il gruppo.
   * Si noti che è possibile configurare l'eventuale sincronizzazione di nuove unità organizzative aggiunte al termine della configurazione del filtro. Per informazioni dettagliate, vedere la sezione che segue.
7. Al termine, fare clic su **OK** per chiudere la finestra di dialogo **Properties** (Proprietà).
8. Per completare la configurazione, è necessario eseguire un'operazione **Full import** (Importazione completa) e un'operazione **Delta sync** (Sincronizzazione delta). Per continuare, vedere la sezione [Applicare e verificare le modifiche](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizzare nuove unità organizzative
Le nuove unità organizzative create dopo la configurazione del filtro vengono sincronizzate per impostazione predefinita. Questo stato è indicato da una casella di controllo selezionata. È anche possibile deselezionare alcune unità organizzative secondarie. A tale scopo, fare clic sulla casella per farla diventare bianca con un segno di spunta blu (stato predefinito). Deselezionare le unità organizzative secondarie da non sincronizzare.

Se tutte le unità organizzative secondarie sono sincronizzate, la casella è bianca con un segno di spunta blu.  
![Unità organizzativa con tutte le caselle selezionate](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Se alcune unità organizzative secondarie sono state deselezionate, la casella è grigia con un segno di spunta bianco.  
![Unità organizzativa con alcune unità organizzative secondarie deselezionate](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

In questa configurazione una nuova unità organizzativa creata in ManagedObjects è sincronizzata.

L'installazione guidata di Azure AD Connect crea sempre questa configurazione.

### <a name="dont-synchronize-new-ous"></a>Non sincronizzare nuove unità organizzative
È possibile configurare il motore di sincronizzazione in modo da non sincronizzare nuove unità organizzative dopo che la configurazione del filtro è stata completata. Nell'interfaccia utente questo stato è indicato da una casella grigia a tinta unita senza alcun segno di spunta. Per ottenere questo comportamento, fare clic sulla casella per farla diventare bianca senza alcun segno di spunta. Selezionare quindi le unità organizzative secondarie da sincronizzare.

![Unità organizzativa con la radice deselezionata](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

In questa configurazione una nuova unità organizzativa creata in ManagedObjects non è sincronizzata.

## <a name="attribute-based-filtering"></a>Filtro basato su attributo
Per il funzionamento corretto di questi passaggi, verificare di usare la build di novembre 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) o versione successiva.

Il filtro basato su attributi è il modo più flessibile per filtrare gli oggetti. È possibile usare la funzionalità di [provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) per controllare quasi tutti gli aspetti dei casi in cui un oggetto viene sincronizzato con Azure AD.

È possibile applicare il filtro [in ingresso](#inbound-filtering) da Active Directory al metaverse e il filtro [in uscita](#outbound-filtering) dal metaverse ad Azure AD. È consigliabile applicare il filtro in ingresso perché è più semplice da gestire, mentre il filtro in uscita deve essere usato solo se è necessario per unire oggetti da più di una foresta prima che venga eseguita la valutazione.

### <a name="inbound-filtering"></a>Filtri in ingresso
I filtri in ingresso usano la configurazione predefinita in base alla quale per gli oggetti diretti ad Azure AD l'attributo metaverse cloudFiltered non deve essere impostato su un valore da sincronizzare. Se il valore di questo attributo è impostato su **True**, l'oggetto non è sincronizzato. Per progettazione, il valore non deve essere impostato su **False**. Per verificare che altre regole possano fornire un valore, questo attributo dovrebbe avere solo i valori **True** o **NULL** (assente).

Per determinare gli oggetti da sincronizzare o da non sincronizzare, durante l'applicazione del filtro in ingresso si usa l'**ambito** dove si apportano le modifiche necessarie per rispettare i requisiti dell'organizzazione. Nel modulo di ambito sono disponibili un **gruppo** e una **clausola** per determinare quando una regola di sincronizzazione è nell'ambito. Un gruppo contiene una o più clausole. Tra più clausole viene inserito un operatore AND logico e tra più gruppi un operatore OR logico.

Ecco un esempio:   
![Ambito](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
Deve essere letto come **(department = IT) OR (department = Sales AND c = US)**.

Negli esempi e nei passaggi seguenti viene usato l'oggetto utente come esempio, ma l'uso può essere esteso a tutti i tipi di oggetto.

Negli esempi seguenti il valore di precedenza inizia con 500 per essere certi che tali regole vengano valutate dopo quelle predefinite (una precedenza più bassa corrisponde a un valore numerico più elevato).

#### <a name="negative-filtering-do-not-sync-these"></a>Filtro negativo (non sincronizzare gli elementi indicati)
Nell'esempio seguente vengono filtrati (non sincronizzati) tutti gli utenti per cui il valore di **extensionAttribute15** è **NoSync**.

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare l'**editor delle regole di sincronizzazione** dal menu **Start**.
3. Verifica che sia selezionata l'opzione **Inbound** (In ingresso) e fare clic su **Add New Rule** (Aggiungi nuova regola).
4. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User DoNotSyncFilter*". Selezionare la foresta corretta, quindi selezionare **User** (Utente) come **CS object type** (Tipo di oggetto CS) e **Person** (Persona) come **MV object type** (Tipo di oggetto MV). In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci). In **Precedence** (Precedenza) digitare un valore attualmente non usato da un'altra regola di sincronizzazione, ad esempio 500, e quindi fare clic su **Next** (Avanti).  
   ![Descrizione in ingresso 1](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. In **Scoping filter** (Filtro ambito) fare clic su **Add Group** (Aggiungi gruppo) e quindi fare clic su **Add Clause** (Aggiungi clausola). In **Attribute** (Attributo) selezionare **ExtensionAttribute15**. Verificare che il valore del campo **Operator** (Operatore) sia impostato su **EQUAL** (UGUALE) e quindi digitare **NoSync** nella casella **Value** (Valore). Fare clic su **Avanti**.  
   ![Ambito in ingresso 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
7. Fare clic su **Add Transformation** (Aggiungi trasformazione), selezionare **Constant** per **FlowType** e **cloudFiltered** come **Target Attribute** (Attributo di destinazione). Nella casella di testo **Source** (Origine) digitare **True**. Fare clic su **Add** (Aggiungi) per salvare la regola.  
   ![Trasformazione in ingresso 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Per completare la configurazione, è necessario eseguire un'operazione **Full sync** (Sincronizzazione completa). Per continuare, vedere la sezione [Applicare e verificare le modifiche](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtro positivo (sincronizzare solo gli elementi indicati)
La creazione di un filtro positivo può essere più complessa perché è necessario considerare anche gli oggetti la cui sincronizzazione non è scontata, ad esempio le sale riunioni.

L'opzione di filtro positivo richiede due regole di sincronizzazione. Sono necessarie una o più regole con l'ambito corretto di oggetti da sincronizzare. È necessaria anche una seconda regola di sincronizzazione catch-all che esclude tutti gli oggetti non ancora identificati come oggetti da sincronizzare.

Nell'esempio seguente vengono sincronizzati solo gli oggetti per i quali l'attributo department ha il valore **Sales**.

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare l'**editor delle regole di sincronizzazione** dal menu **Start**.
3. Verifica che sia selezionata l'opzione **Inbound** (In ingresso) e fare clic su **Add New Rule** (Aggiungi nuova regola).
4. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User Sales sync*". Selezionare la foresta corretta, quindi selezionare **User** (Utente) come **CS object type** (Tipo di oggetto CS) e **Person** (Persona) come **MV object type** (Tipo di oggetto MV). In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci). In **Precedence** (Precedenza) digitare un valore attualmente non usato da un'altra regola di sincronizzazione, ad esempio 501, e quindi fare clic su **Next** (Avanti).  
   ![Destinazione in ingresso 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. In **Scoping filter** (Filtro ambito) fare clic su **Add Group** (Aggiungi gruppo) e quindi fare clic su **Add Clause** (Aggiungi clausola). In **Attribute** (Attributo) selezionare **department**. Verificare che il valore del campo Operato (Operatore) sia impostato su **EQUAL** (UGUALE) e quindi digitare **Sales** nella casella **Value** (Valore). Fare clic su **Avanti**.  
   ![Ambito in ingresso 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
7. Fare clic su **Add Transformation** (Aggiungi trasformazione), selezionare **Constant** per **FlowType** e **cloudFiltered** come **Target Attribute** (Attributo di destinazione). Nella casella **Source** (Origine) digitare **False**. Fare clic su **Add** (Aggiungi) per salvare la regola.  
   ![Trasformazione in ingresso 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Questo è un caso particolare in cui cloudFiltered viene impostato in modo esplicito su **False**.
8. Ora è necessario creare la regola di sincronizzazione catch-all. Assegnare alla regola un nome descrittivo, ad esempio "*In from AD - User Catch-all filter*". Selezionare la foresta corretta, quindi selezionare **User** (Utente) come **CS object type** (Tipo di oggetto CS) e **Person** (Persona) come **MV object type** (Tipo di oggetto MV). In **Link Type** (Tipo di collegamento) selezionare **Join** (Unisci). In **Precedence** (Precedenza) digitare un valore attualmente non usato da un'altra regola di sincronizzazione, ad esempio 600. È stato selezionato un valore di precedenza più alto (precedenza inferiore) rispetto alla regola di sincronizzazione precedente, ma è stato lasciato anche spazio per aggiungere in seguito più regole di sincronizzazione del filtro quando si vuole avviare la sincronizzazione di altri reparti. Fare clic su **Avanti**.  
   ![Descrizione in ingresso 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Lasciare vuoto **Scoping filter** e fare clic su **Next**. Un filtro vuoto indica che la regola deve essere applicata a tutti gli oggetti.
10. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
11. Fare clic su **Add Transformation** (Aggiungi trasformazione), selezionare **Constant** per **FlowType** e **cloudFiltered** come **Target Attribute** (Attributo di destinazione). Nella casella **Source** (Origine) digitare **True**. Fare clic su **Add** (Aggiungi) per salvare la regola.  
    ![Trasformazione in ingresso 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Per completare la configurazione, è necessario eseguire un'operazione **Full sync** (Sincronizzazione completa). Per continuare, vedere la sezione [Applicare e verificare le modifiche](#apply-and-verify-changes).

Se necessario, è possibile creare più regole del primo tipo per includere altri oggetti nella sincronizzazione.

### <a name="outbound-filtering"></a>Filtri in uscita
In alcuni casi è necessario applicare il filtro solo dopo aver unito gli oggetti al metaverse. Per determinare se è necessario sincronizzare un oggetto, potrebbe ad esempio essere opportuno cercare l'attributo mail nella foresta di risorse e l'attributo userPrincipalName nella foresta di account. In questi casi, vengono creati i filtri nella regola in uscita.

In questo esempio si modifica il filtro in modo che vengano sincronizzati solo gli utenti per cui entrambi gli attributi mail e userPrincipalName terminano in @contoso.com:

1. Accedere al server che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins** .
2. Avviare l'**editor delle regole di sincronizzazione** dal menu **Start**.
3. In **Rules Type** (Tipo di regola) fare clic su **Outbound** (In uscita).
4. Trovare la regola denominata **Out to AAD – User Join SOAInAD** e quindi fare clic **Edit** (Modifica).
5. Nel popup selezionare **Yes** (Sì) per creare una copia della regola.
6. Nella pagina **Description** (Descrizione) modificare il campo **Precedence** (Precedenza) su un valore non usato, ad esempio 50.
7. Fare clic su **Scoping filter** (Filtro ambito) nel riquadro di spostamento a sinistra e quindi fare clic su **Add clause** (Aggiungi clausola). In **Attribute** (Attributo) selezionare **mail**. In **Operator** (Operatore) selezionare **ENDSWITH** (TERMINACON). In **Value** (Valore) digitare **@contoso.com** e quindi fare clic su **Add clause** (Aggiungi clausola). In **Attribute** (Attributo) selezionare **userPrincipalName**. In **Operator** (Operatore) selezionare **ENDSWITH** (TERMINACON). In **Value** (Valore) digitare **@contoso.com**.
8. Fare clic su **Salva**
9. Per completare la configurazione, è necessario eseguire un'operazione **Full sync** (Sincronizzazione completa). Per continuare, vedere la sezione [Applicare e verificare le modifiche](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Applicare e verificare le modifiche
Dopo aver apportato le modifiche alla configurazione, è necessario applicarle agli oggetti già presenti nel sistema. È possibile anche che gli oggetti non presenti attualmente nel motore di sincronizzazione debbano essere elaborati e che il motore di sincronizzazione debba leggere di nuovo il sistema di origine per verificarne il contenuto.

Se la configurazione è stata modificata usando il filtro basato su **dominio** o su **unità organizzativa**, è necessario eseguire un'operazione **Full import** (Importazione completa), seguita da un'operazione **Delta synchronization** (Sincronizzazione delta).

Se la configurazione è stata modificata usando il filtro basato su **attributi**, è necessario eseguire un'operazione **Full synchronization** (Sincronizzazione completa).

Seguire anche questa procedura:

1. Avviare **Synchronization Service** (Servizio di sincronizzazione) dal menu **Start**.
2. Selezionare **Connectors** (Connettori). Nell'elenco **Connectors** (Connettori) selezionare il connettore in cui in precedenza è stata apportata una modifica alla configurazione. In **Actions** (Azioni) selezionare **Run** (Esegui).  
   ![Esecuzione del connettore](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. In **Run profiles** (Profili di esecuzione) selezionare l'operazione indicata nella sezione precedente. Se è necessario eseguire due azioni, eseguire la seconda dopo il completamento della prima. Il valore della colonna **State** (Stato) è impostato su **Idle** (Inattivo) per il connettore selezionato.

Dopo la sincronizzazione, tutte le modifiche vengono inserite temporaneamente per essere esportate. Prima di apportare effettivamente le modifiche in Azure AD, è opportuno verificare che siano tutte corrette.

1. Avviare un prompt dei comandi e passare a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Eseguire `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Il nome del connettore si trova nel servizio di sincronizzazione. Il nome sarà simile a "contoso.com - AAD" per Azure AD.
3. Eseguire `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. A questo punto si avrà un file denominato export.csv in %temp%, che può essere esaminato in Microsoft Excel. Questo file contiene tutte le modifiche in fase di esportazione.
5. Apportare le modifiche necessarie ai dati o alla configurazione ed eseguire di nuovo questi passaggi (importazione, sincronizzazione e verifica) finché le modifiche che verranno esportate non saranno quelle previste.

Al termine, esportare le modifiche in Azure AD.

1. Selezionare **Connectors** (Connettori). Nell'elenco **Connectors** (Connettori) selezionare il connettore Azure AD. In **Actions** (Azioni) selezionare **Run** (Esegui).
2. In **Run profiles** (Profili di esecuzione) selezionare **Export** (Esporta).
3. Se le modifiche della configurazione comportano l'eliminazione di molti oggetti, viene visualizzato un errore nell'esportazione se il numero è superiore alla soglia configurata (500 per impostazione predefinita). Se viene visualizzato questo errore, è necessario disabilitare temporaneamente la funzionalità che [impedisce eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

A questo punto è possibile abilitare di nuovo l'utilità di pianificazione.

1. Avviare **Utilità di pianificazione** dal menu **Start**.
2. In **Libreria Utilità di pianificazione** trovare l'attività denominata **Azure AD Sync Scheduler**, fare clic con il pulsante destro del mouse e scegliere **Abilita**.

## <a name="group-based-filtering"></a>Filtri basati sui gruppi
È possibile configurare il filtro basato su gruppi la prima volta che si installa Azure AD Connect tramite l'installazione personalizzata. Tale filtro è progettato per una distribuzione pilota in cui si desidera solo un piccolo gruppo di oggetti da sincronizzare. Dopo essere stato disabilitato, il filtro basato su gruppi non può essere abilitato nuovamente. L'uso del filtro basato su gruppi *non è supportato* in una configurazione personalizzata, ma è supportato solo per configurare questa funzionalità tramite l'installazione guidata. Dopo aver completato il progetto pilota, usare solo una delle altre opzioni di filtro descritte in questo argomento.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla configurazione del [servizio di sincronizzazione Azure AD Connect](active-directory-aadconnectsync-whatis.md).
- Altre informazioni sull'[integrazione di identità locali con Azure AD](active-directory-aadconnect.md).

