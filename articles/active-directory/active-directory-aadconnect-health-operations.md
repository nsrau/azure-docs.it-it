<properties
	pageTitle="Operazioni di Azure AD Connect Health."
	description="Questo articolo descrive le operazioni aggiuntive che possono essere eseguite dopo aver distribuito Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="femila"
	editor="curtand"/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="vakarand"/>

# Operazioni di Azure AD Connect Health

Il seguente argomento descrive le varie operazioni che possono essere eseguite con Azure AD Connect Health.

## Abilitare le notifiche tramite posta elettronica
È possibile configurare il servizio Azure AD Connect Health per l'invio di notifiche di posta elettronica quando vengono generati avvisi indicanti che l'infrastruttura di identità non è integra. Questo si verifica quando viene generato un avviso, ma anche quando viene contrassegnato come risolto. Seguire le istruzioni seguenti per configurare le notifiche di posta elettronica.

![Individuazione delle notifiche di posta elettronica di Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Le notifiche di posta elettronica sono disabilitate per impostazione predefinita.


### Per abilitare le notifiche di posta elettronica di Azure AD Connect Health

1. Aprire il pannello Avvisi per il servizio per cui si desidera ricevere una notifica di posta elettronica.
2. Fare clic sul pulsante "Impostazioni di notifica" sulla barra delle azioni.
3. Attivare l'opzione Notifica di posta elettronica.
4. Selezionare la casella di controllo per fare in modo che tutti gli amministratori globali ricevano notifiche di posta elettronica.
5. Se si desidera ricevere notifiche di posta elettronica ad altri indirizzi di posta elettronica, è possibile specificarli nella casella Destinatario di posta elettronica aggiuntivo. Per rimuovere un indirizzo di posta elettronica da questo elenco, fare clic con il pulsante destro del mouse sulla voce e scegliere Elimina.
6. Per finalizzare le modifiche, fare clic su "Salva". Tutte le modifiche verranno applicate solo dopo avere selezionato "Salva".

## Eliminare un server o un'istanza del servizio

### Eliminare un server dal servizio Azure AD Connect Health
In alcuni casi, può essere utile fare in modo che un server non venga più monitorato. Seguire le istruzioni seguenti per rimuovere un server dal servizio Azure AD Connect Health.

Quando si elimina un server, tenere presente quanto segue:

- Questa azione ARRESTERÀ la raccolta di altri dati da tale server. Questo server verrà rimosso dal servizio di monitoraggio. Dopo questa azione, non sarà possibile visualizzare nuovi avvisi né dati di monitoraggio o di analisi di utilizzo per questo server.
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità dal server. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore sul server correlato all'agente per l'integrità.
- Questa azione NON eliminerà i dati già raccolti da questo server. Tali dati verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure.
- Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server.


#### Per eliminare un server dal servizio Azure AD Connect Health

Azure AD Connect Health per AD FS e Azure AD Connect (Sincronizzazione):

1. Aprire il pannello Server dal pannello Elenco server selezionando il nome del server da rimuovere.
2. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
3. Confermare l'azione di eliminazione del server digitando il nome del server nella casella di conferma.
4. Fare clic sul pulsante "Elimina".

Azure AD Connect Health per Servizi di dominio Active Directory:

1. Aprire il dashboard dei controller di dominio.
2. Selezionare il controller di dominio da rimuovere.
3. Fare clic sul pulsante "Elimina elemento selezionato" sulla barra delle azioni.
4. Confermare l'azione per eliminare il server.
5. Fare clic sul pulsante "Elimina".

### Eliminare un'istanza del servizio dal servizio Azure AD Connect Health

In alcuni casi, può essere utile rimuovere un'istanza del servizio. Seguire le istruzioni seguenti per rimuovere un'istanza del servizio dal servizio Azure AD Connect Health.

Quando si elimina un'istanza del servizio, tenere presente quanto segue:

- Questa azione rimuoverà l'istanza corrente del servizio dal servizio di monitoraggio.
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità da nessun server monitorato come parte di questa istanza del servizio. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore su uno o più server correlati all'agente per l'integrità.
- Tutti i dati di questa istanza del servizio verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure.
- Dopo avere eseguito questa azione, se si desidera iniziare a monitorare il servizio, disinstallare e reinstallare l'agente per l'integrità in tutti i server che verranno monitorati. Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server.


#### Per eliminare un'istanza del servizio dal servizio Azure AD Connect Health

1. Aprire il pannello Servizio dal pannello Elenco servizi selezionando l'identificatore del servizio (nome farm) che si desidera rimuovere.
2. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
3. Confermare il nome del servizio digitandolo nella casella di conferma (ad esempio: sts.contoso.com).
4. Fare clic sul pulsante "Elimina". <br><br>


[//]: # "Inizio della sezione Controllo degli accessi in base al ruolo"
## Gestire l'accesso con il controllo degli accessi in base al ruolo
### Overview
Il [controllo degli accessi in base al ruolo](role-based-access-control-configure.md) per Azure AD Connect Health consente l'accesso al servizio Azure AD Connect Health agli utenti e/o ai gruppi che non sono amministratori globali. Questo avviene tramite l'assegnazione di ruoli agli utenti e/o ai gruppi previsti e si ottiene così un meccanismo per limitare gli amministratori globali all'interno della directory.

#### Ruoli
Azure AD Connect Health supporta i ruoli predefiniti seguenti.

| Ruolo | Autorizzazioni |
| ----------- | ---------- |
| Proprietario | I proprietari possono ***gestire l'accesso*** (ad esempio, assegnare ruoli a un utente/gruppo), ***visualizzare tutte le informazioni*** (ad esempio, visualizzare gli avvisi) dal portale e ***modificare le impostazioni *** (ad esempio notifiche di posta elettronica) all'interno di Azure AD Connect Health. <br>Per impostazione predefinita, gli amministratori globali di Azure AD vengono assegnati a questo ruolo e questa assegnazione non è modificabile. |
|Collaboratore| I collaboratori possono ***visualizzare tutte le informazioni*** (ad esempio, visualizzare gli avvisi) dal portale e ***modificare le impostazioni *** (ad esempio notifiche di posta elettronica) all'interno di Azure AD Connect Health.|
|Reader| I lettori possono ***visualizzare tutte le informazioni*** (ad esempio, visualizzare gli avvisi) dal portale all'interno di Azure AD Connect Health.|

Tutti gli altri ruoli, ad esempio gli amministratori dell'accesso utente o gli utenti DevTest Labs, anche se disponibili nell'esperienza del portale, non influiscono sull'accesso all'interno di Azure AD Connect Health.

#### Ambito di accesso

Azure AD Connect supporta la gestione dell'accesso su due livelli:

- ***Tutte le istanze del servizio***: questo è il percorso consigliato per la maggior parte dei clienti e controlla l'accesso a tutte le istanze del servizio (ad esempio, una farm AD FS) per tutti i tipi di ruolo monitorati da Azure AD Connect Health.

- ***Istanza del servizio***: in alcuni casi, potrebbe essere necessario separare l'accesso in base ai tipi di ruolo o per un'istanza del servizio. In questo caso, è possibile gestire l'accesso a livello di istanza del servizio.

L'autorizzazione viene concessa se un utente finale dispone di accesso a livello di directory o a livello di istanza del servizio.


### Come consentire l'accesso a utenti o gruppi ad Azure AD Connect Health
#### Passaggio 1: Selezionare l'ambito di accesso appropriato
Per consentire a un utente l'accesso a livello di *tutte le istanze del servizio* dall'interno di Azure AD Connect Health, aprire il pannello principale in Azure AD Connect Health.<br>
#### Passaggio 2: Aggiungere utenti, gruppi e assegnare ruoli
1. Fare clic sulla parte "Utenti" dalla sezione Configura.<br> ![Pannello principale del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selezionare "Aggiungi"
3. Selezionare il "Ruolo", ad esempio "Proprietario"<br> ![Aggiungere utente del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Digitare il nome o l'identificatore dell'utente o del gruppo. È possibile selezionare uno o più utenti o gruppi contemporaneamente. Fare clic su "seleziona". ![Selezionare utente del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selezionare "Ok".<br>

6. Una volta completata l'assegnazione del ruolo, gli utenti e/o i gruppi verranno visualizzati nell'elenco.<br> ![Elenco utenti del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Con questi passaggi verrà consentito l'accesso agli utenti e ai gruppi in base ai relativi ruoli assegnati.
>[AZURE.NOTE]
- Gli amministratori globali hanno sempre accesso completo a tutte le operazioni, ma gli account degli amministratori globali non saranno presenti nell'elenco precedente.
- La funzionalità "Invita utenti" NON è supportata in Azure AD Connect Health.

#### Passaggio 3: Condividere il percorso del pannello con utenti o gruppi
1. Dopo l'assegnazione delle autorizzazioni, un utente può accedere ad Azure AD Connect Health passando a [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Dopo l'accesso al pannello, l'utente può aggiungere il pannello o varie parti al dashboard facendo semplicemente clic su "Aggiungi al dashboard"<br> ![Pannello Pin del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Un utente assegnato al ruolo "Lettore" non potrà eseguire l'operazione di creazione per recuperare l'estensione Azure AD Connect Health da Azure Marketplace. L'utente potrà comunque ancora accedere al pannello tramite il collegamento sopra riportato. Per gli utilizzi successivi, l'utente può aggiungere il pannello al dashboard.

### Rimuovere utenti e/io gruppi
Per rimuovere un utente o un gruppo aggiunto alla parte del controllo degli accessi in base al ruolo di Azure AD Connect Health, è sufficiente fare clic con il pulsante destro del mouse e scegliere Rimuovi.<br> ![Rimuovere utente del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # "Fine della sezione Controllo degli accessi in base al ruolo"

## Collegamenti correlati

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0928_2016-->