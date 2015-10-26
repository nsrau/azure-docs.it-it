<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Configurare il filtro | Microsoft Azure"
	description="Illustra come configurare i filtri nel servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Configurare il filtro

Nel servizio di sincronizzazione Azure AD Connect è possibile abilitare il filtro in qualsiasi momento. Se sono già state eseguite le configurazioni predefinite del servizio di sincronizzazione della directory e successivamente è stato configurato il filtro, gli oggetti esclusi non verranno più sincronizzati con Azure AD. Di conseguenza, tutti gli oggetti in Azure AD sincronizzati in precedenza, ma poi esclusi, verranno eliminati in Azure AD. Se gli oggetti sono stati eliminati inavvertitamente a causa di un errore di filtro, è possibile ricrearli in Azure AD rimuovendo le configurazioni di filtro e quindi sincronizzando di nuovo le directory.

> [AZURE.IMPORTANT]Microsoft non supporta la modifica o l'utilizzo del servizio di sincronizzazione Azure AD Connect per azioni diverse da quelle formalmente documentate. Una qualsiasi di queste azioni può determinare uno stato non coerente o non supportato del servizio di sincronizzazione Azure AD Connect. Microsoft pertanto non può fornire il supporto tecnico per tali distribuzioni.

Fatta eccezione per i filtri basati sugli attributi in uscita, quando si installa o si esegue l'aggiornamento a una versione più recente di Azure AD Connect verranno conservate tutte le configurazioni. Prima di eseguire il primo ciclo di sincronizzazione, la procedura consigliata è verificare sempre che la configurazione non sia stata modificata inavvertitamente dopo un aggiornamento a una versione più recente.


## Opzioni di filtro

> [AZURE.NOTE]In questo argomento viene usato il termine SourceAD come nome del connettore di Servizi di dominio Active Directory. Nel caso siano disponibili più foreste, si avrà un solo connettore per ogni foresta e sarà necessario ripetere la configurazione per ogni foresta.

Allo Strumento di sincronizzazione della directory è possibile applicare i tre tipi di configurazione di filtro descritti di seguito:

- [**Basato su dominio**](#configure-domain-based filtering): è possibile usare questo tipo di filtro per gestire le proprietà del connettore SourceAD nel servizio di sincronizzazione Azure AD Connect. Questo tipo consente di selezionare i domini che possono eseguire la sincronizzazione con Azure AD.

- [**Configurare il filtro basato sull'unità organizzativa**](#configure-organizational-unit–based filtering): è possibile usare questo tipo di filtro per gestire le proprietà del connettore SourceAD nel servizio di sincronizzazione Azure AD Connect. Questo tipo di filtro consente di selezionare le unità organizzative che possono eseguire la sincronizzazione con Azure AD.

- [**Basato su attributi**](#configure-attribute-based-filtering): è possibile usare questo metodo per specificare filtri basati su attributi. e di conseguenza controllare gli oggetti che devono essere sincronizzati con il cloud.

> [AZURE.NOTE]Se si apporta una modifica a una configurazione di filtro e si usa il servizio di sincronizzazione delle password, sarà necessario attivare una sincronizzazione completa di tutte le password al termine della configurazione. Per informazioni su come attivare una sincronizzazione completa di tutte le password, vedere [Attivare una sincronizzazione completa di tutte le password](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords)

## Configurare il filtro basato su dominio

Questa sezione illustra i passaggi necessari per configurare il filtro basato su dominio.

Se sono stati aggiunti o rimossi domini nella foresta dopo aver installato Azure AD Connect, sarà necessario aggiornare anche la configurazione del filtro.


### Modifica del filtro per i domini esistenti

> [AZURE.NOTE]Se il filtro basato su dominio è stato modificato, è necessario aggiornare anche i profili di esecuzione.


**Per impostare il filtro basato su dominio, seguire questa procedura:**

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.

2. Nella schermata **Start** toccare o fare clic su **Servizio di sincronizzazione** per aprire **Synchronization Service Manager**.

3. Per aprire la visualizzazione dei connettori, scegliere **Connectors** dal menu **Tools**.

4. Nell'elenco **Connectors** selezionare il connettore per cui **Type** corrisponde a **Active Directory Domain Service**.

5. Per aprire la finestra di dialogo **Properties** scegliere **Properties** dal menu **Actions**.

6. Fare clic su **Configure Directory Partitions**.

7. Nell'elenco **Select directory partitions** verificare che siano selezionate solo le partizioni da sincronizzare. <br> Per rimuovere un dominio dal processo di sincronizzazione, deselezionare la casella di controllo corrispondente.

8. Fare clic su **OK** per chiudere la finestra di dialogo **Properties**.


Se il filtro basato su dominio è stato aggiornato, è necessario aggiornare anche i profili di esecuzione seguenti:

- Importazione completa
- Sincronizzazione completa
- Importazione differenziale
- Sincronizzazione differenziale
- Esporta


Se è stata **rimossa** una partizione dall'elenco delle partizioni di directory, è necessario assicurarsi di rimuovere anche tutte le istruzioni relative ai profili di esecuzione che fanno riferimento a tale partizione.

**Per rimuovere un'istruzione da un profilo di esecuzione, seguire questa procedura:**

1. Nell'elenco **Connectors** selezionare il connettore per cui **Type** corrisponde a **Active Directory Domain Service**.

2. Per aprire la finestra di dialogo **Configure Run Profiles for**, scegliere **Configure Run Profiles** dal menu **Actions**.

3. Nell'elenco **Connector run profiles** selezionare il profilo di esecuzione da configurare.

4. Effettuare i seguenti passaggi per ogni istruzione presente nel relativo elenco:

     4\.1. Se necessario, fare clic su un'istruzione per espanderne i dettagli.

     4\.2. Se l'opzione **Value** dell'attributo **Partition** è un GUID, fare clic su Delete Step.

5. Per chiudere la finestra di dialogo **Configure Run Profiles**, fare clic su **OK**.

Se è stata **aggiunta** una partizione all'elenco delle partizioni di directory, è necessario assicurarsi che un'istruzione relativa ai profili di esecuzione che fa riferimento a tale partizione sia disponibile in ciascuno dei profili di esecuzione contenuti nell'elenco.

**Per aggiungere un'istruzione a un profilo di esecuzione, seguire questa procedura:**

1. Nell'elenco **Connectors** selezionare il connettore per cui **Type** corrisponde a **Active Directory Domain Service**.

2. Per aprire la finestra di dialogo **Configure Run Profiles for**, scegliere **Configure Run Profiles** dal menu **Actions**.

3. Nell'elenco **Connector run profiles** selezionare il profilo di esecuzione da configurare.

4. Per aprire la finestra di dialogo **Configure Run Profile**, fare clic su **New Step**.

5. Nell'elenco dei tipi di istruzioni della pagina **Configure Step** selezionare un tipo di istruzione e fare clic su **Next**.

6. Nell'elenco **Partition** della pagina **Connector Configuration** selezionare il nome della partizione aggiunta al filtro basato su dominio.

7. Per chiudere la finestra di dialogo **Configure Run Profile**, fare clic su **Finish**.

8. Per chiudere la finestra di dialogo **Configure Run Profiles for**, fare clic su **OK**.

### Modifica del filtro per domini nuovi o rimossi
Se è stato aggiunto un nuovo dominio alla foresta o se ne è stato rimosso uno, è necessario aggiornare la configurazione del filtro.

**Procedura se è stato aggiunto un dominio**

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.

2. Nella schermata **Start** toccare o fare clic su **Servizio di sincronizzazione** per aprire **Synchronization Service Manager**.

3. Per aprire la visualizzazione dei connettori, scegliere **Connectors** dal menu **Tools**.

4. Nell'elenco **Connectors** selezionare il connettore per cui **Type** corrisponde a **Active Directory Domain Service**.

5. Per aprire la finestra di dialogo **Properties** scegliere **Properties** dal menu **Actions**.

6. Fare clic su **Configure Directory Partitions**.

7. Fare clic sul pulsante **Refresh** nella parte superiore della pagina.

8. Nell'elenco **Select directory partitions** selezionare il nuovo dominio.

9. Fare clic su OK per chiudere la finestra di dialogo **Properties**.

Assicurarsi che sia sempre selezionato lo stesso connettore. A questo punto, è necessario configurare i profili di esecuzione per il connettore.


- Importazione completa
- Sincronizzazione completa
- Importazione differenziale
- Sincronizzazione differenziale
- Esporta


10. Per aprire la finestra di dialogo **Configure Run Profiles for**, scegliere **Configure Run Profiles** dal menu **Actions**.

11. Nell'elenco **Connector run profiles** selezionare il profilo di esecuzione da configurare.

12. Per aprire la finestra di dialogo **Configure Run Profile**, fare clic su **New Step**.

13. Nell'elenco dei tipi di istruzioni della pagina **Configure Step** selezionare un tipo di istruzione e fare clic su **Next**.

14. Nell'elenco **Partition** della pagina **Connector Configuration** selezionare il nome della partizione aggiunta al filtro basato su dominio.

15. Per chiudere la finestra di dialogo **Configure Run Profile**, fare clic su **Finish**.

16. Per chiudere la finestra di dialogo **Configure Run Profiles for**, fare clic su **OK**.

17. Ripetere la procedura per tutti i profili di esecuzione.

**Procedura se è stato rimosso un dominio**

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.

2. Nella schermata **Start** toccare o fare clic su **Servizio di sincronizzazione** per aprire **Synchronization Service Manager**.

3. Per aprire la visualizzazione dei connettori, scegliere **Connectors** dal menu **Tools**.

4. Nell'elenco **Connectors** selezionare il connettore per cui **Type** corrisponde a **Active Directory Domain Service**.

5. Per aprire la finestra di dialogo **Properties** scegliere **Properties** dal menu **Actions**.

6. Fare clic su **Configure Directory Partitions**.

7. Fare clic sul pulsante **Refresh** nella parte superiore della pagina.

8. Fare clic su OK per chiudere la finestra di dialogo **Properties**. Verrà visualizzato un messaggio che indica che è stato rimosso un dominio e che verrà eseguita la pulizia della configurazione.

## Configurare il filtro basato sull'unità organizzativa

**Per configurare il filtro basato sull'unità organizzativa, eseguire la procedura seguente:**

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza **ADSyncAdmins**.

2. Nella schermata **Start** toccare o fare clic su **Servizio di sincronizzazione** per aprire **Synchronization Service Manager**.<br>

3. In **Synchronization Service Manager** fare clic su **Connectors**, quindi fare doppio clic su **SourceAD**.

4. Fare clic su **Configure Directory Partitions**, selezionare il dominio da configurare e quindi fare clic su **Containers**.

5. Quando richiesto, immettere le credenziali di dominio per la foresta Active Directory locale.<br> >[AZURE.NOTE]Quando verrà visualizzata la finestra di dialogo delle credenziali, verrà visualizzato l'account usato per importare ed esportare nei Servizi di dominio Active Directory. Se non si conosce la password dell'account, è possibile immettere un altro account da usare. L'account usato deve avere autorizzazioni di lettura per il dominio di cui è in corso la configurazione.

6. Nella finestra di dialogo **Select Containers** deselezionare le unità organizzative da non sincronizzare con la directory cloud e quindi fare clic su **OK**.

7. Fare clic su **OK** nella pagina **SourceAD Properties**.

8. Eseguire un'importazione completa e una sincronizzazione differenziale completando i passaggi seguenti:

     8\.1 Nell'elenco dei connettori selezionare **SourceAD**

     8\.2. Per aprire la finestra di dialogo **Run Connector**, scegliere **Run** dal menu **Actions**.

     8\.3. Nell'elenco **Run profiles** selezionare **Full Import** e quindi attendere il completamento del profilo di esecuzione.

     8\.4. Per aprire la finestra di dialogo **Run Connector**, scegliere **Run** dal menu **Actions**.

     8\.5. Nell'elenco **Run profiles** selezionare **Delta Synchronization** e quindi attendere il completamento del profilo di esecuzione.




## Configurare il filtro basato su attributo

Ci sono diversi modi per configurare il filtro basato su attributi. È consigliabile eseguire la configurazione in ingresso da AD poiché queste impostazioni verranno mantenute anche dopo aver eseguito l'aggiornamento a una versione più recente. È supportata anche la configurazione in uscita verso AAD, ma queste impostazioni non verranno conservate dopo aver eseguito l'aggiornamento a una versione più recente e dovranno essere usate solo quando è necessario esaminare l'oggetto combinato nel metaverse per determinare i filtri.

### Filtri in ingresso

I filtri in ingresso sfruttano la configurazione predefinita degli oggetti diretti ad AAD secondo la quale è necessario che l'attributo metaverse cloudFiltered non sia impostato su un valore e l'attributo metaverse sourceObjectType sia impostato su "User" o "Contact".

L'attributo cloudFiltered deve essere impostato su True quando l'oggetto non deve essere sincronizzato con Azure AD, e mantenuto vuoto in altri casi. Questo metodo consente di esaminare un oggetto per poi decidere di non sincronizzarlo (filtro negativo).

Nell'esempio seguente verranno filtrati tutti gli utenti in cui il valore di extensionAttribute15 è NoSync:

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza ADSyncAdmins.
2. Aprire **Synchronization Rules Editor** dal menu **Start**.
3. Assicurarsi che sia selezionata l'opzione **Inbound** e fare clic su **Add New Rule**.
4. Assegnare alla regola un nome descrittivo, ad esempio "*In ingresso da AD - Utente DoNotSyncFilter*", selezionare la foresta corretta, quindi selezionare il **tipo di oggetto CS** per **User** e il **tipo di oggetto MV** per **Person**. In **Link Type** selezionare **Join** e in Precedence Type immettere un valore che non sia attualmente usato da un'altra regola di sincronizzazione, ad esempio 50, quindi fare clic su **Next**.
5. In **Scoping filter** fare clic su **Add Group**, quindi su **Add Clause** e in attribute selezionare **ExtensionAttribute15**. Assicurarsi che Operator sia impostato su **EQUAL** e **digitare** il valore NoSync nella casella Value. Fare clic su **Next**.
6. Lasciare vuote le regole **Join** e quindi fare clic su **Next**.
7. Fare clic su **Add Transformation**, impostare **FlowType** su **Constant**, selezionare l'attributo di destinazione cloudFiltered e nella casella di testo Source digitare True. Fare clic su Add per salvare la regola.
8. Eseguire una sincronizzazione completa. A tale scopo, nella scheda **Connectors** fare clic con il pulsante destro del mouse su **SourceAD**, scegliere **Run**, fare clic su **Full Synchronization** e quindi su **OK**.


L'attributo **sourceObjectType** effettuerà il provisioning di un elemento **User** o **Contact** in Azure AD se questo attributo ha rispettivamente il valore **User** o **Contact**. Creando una regola di sincronizzazione con una precedenza maggiore rispetto a quella predefinita, è possibile ignorare il comportamento predefinito. Questo metodo offre anche l'opportunità di esprimere regole sia positive che negative.

Nell'esempio seguente si sincronizzeranno gli utenti solo quando l'attributo department è "*Sales*" oppure è vuoto:

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza ADSyncAdmins.
2. Aprire **Synchronization Rules Editor** dal menu **Start**.
3. Assicurarsi che l'opzione **Inbound** sia selezionata e quindi fare clic su **Add New Rule**.
4. Assegnare alla regola un nome descrittivo, ad esempio "*In ingresso da AD - Utente DoNotSyncFilter*", selezionare la foresta corretta, quindi selezionare il **tipo di oggetto CS** per **User** e il **tipo di oggetto MV** per **Person**. In **Link Type** selezionare **Join** e in **precedence type** immettere un valore che non sia attualmente usato da un'altra regola di sincronizzazione, ad esempio 60. Fare clic su **Next**.
5. Lasciare vuoti i campi **scoping filter** e **join rules** e fare clic su **Next** due volte.
6. Fare clic su **Add Transformation**, impostare **FlowType** su **Expression** e impostare **Target Attribute** su **sourceObjectType**. In **Source** digitare l'espressione seguente:<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Fare clic su Add per salvare la regola.
8. Eseguire una sincronizzazione completa. A tale scopo, nella scheda **Connectors** fare clic con il pulsante destro del mouse su **SourceAD**, scegliere **Run**, fare clic su **Full Synchronization** e quindi su **OK**. Di seguito è illustrato il possibile risultato:<br>

> [AZURE.NOTE]Notare che si sta usando una combinazione di attributi cloudFiltered e sourceObjectType per determinare quali oggetti sincronizzare con AAD.

Con l'uso delle espressioni, è possibile usufruire di opzioni di filtro molto potenti. Nelle espressioni sopra riportate, notare che è stato fornito il valore letterale NULL quando il reparto non è presente e quando il reparto era Sales. Ciò indica che questo attributo non ha contribuito con un valore e che le regole predefinite verranno valutate. Questa procedura è richiesta per determinare se sia necessario creare un elemento **User** o **Contact** in Azure AD.


## Filtro in uscita

In alcuni casi, è necessario applicare il filtro solo dopo aver unito gli oggetti a metaverse. Ad esempio, potrebbe essere opportuno cercare l'attributo mail nella foresta di risorse e l'attributo userPrincipalName nella foresta di account per determinare se è necessario sincronizzare un oggetto. In questi casi, verranno creati i filtri nella regola in uscita.

> [AZURE.NOTE]Questo metodo richiede la modifica delle regole di sincronizzazione predefinite. La modifica dell'ambito di una regola di sincronizzazione è supportata, ma potrebbe non essere mantenuta dopo aver eseguito l'aggiornamento a una versione più recente di Azure AD Connect. Se si usa il filtro in uscita, annotare le modifiche da apportare e, dopo un aggiornamento, assicurarsi che il filtro sia ancora presente, riapplicandolo se necessario.


In questo esempio il filtro verrà modificato in modo che vengano sincronizzati solo gli utenti in cui sia mail che userPrincipalName terminano con @contoso.com:

1. Eseguire l'accesso al computer che esegue il servizio di sincronizzazione Azure AD Connect usando un account membro del gruppo di sicurezza ADSyncAdmins.
2. Cercare Synchronization Rules Editor nel menu Start e aprirlo.
3. In Rule Types fare clic su Outbound.
4. Trovare la regola denominata Out to AAD – User Join. Fare clic su Edit.
5. Fare clic su Scoping filter a sinistra. Fare clic su Add clause e in Attribute selezionare mail, in Operator selezionare ENDSWITH e in Value digitare @contoso.com. Fare clic su Add clause e in Attribute selezionare userPrincipalName, in Operator selezionare ENDSWITH e in Value digitare @contoso.com.
6. Fare clic su Salva.
7. Eseguire una sincronizzazione completa. A tale scopo, nella scheda Connectors fare clic con il pulsante destro del mouse su SourceAD, scegliere Run, fare clic su Full Synchronization e quindi su OK.


## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->