<properties
	pageTitle="Operazioni di Azure AD Connect Health."
	description="Questo articolo descrive le operazioni aggiuntive che possono essere eseguite dopo aver distribuito Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Operazioni di Azure AD Connect Health

Il seguente argomento descrive le varie operazioni che possono essere eseguite con Azure AD Connect Health.

## Abilitare le notifiche tramite posta elettronica
È possibile configurare il servizio Azure AD Connect Health per l'invio di notifiche di posta elettronica quando vengono generati avvisi indicanti che l'infrastruttura di identità non è integra. Questo si verifica quando viene generato un avviso, ma anche quando viene contrassegnato come risolto. Seguire le istruzioni seguenti per configurare le notifiche di posta elettronica. Si noti che le notifiche di posta elettronica sono disabilitate per impostazione predefinita.


### Per abilitare le notifiche di posta elettronica di Azure AD Connect Health

1. Aprire il pannello Avvisi per il servizio per cui si desidera ricevere una notifica di posta elettronica.
2. Fare clic sul pulsante "Impostazioni di notifica" sulla barra delle azioni.
3. Attivare l'opzione Notifica di posta elettronica.
4. Selezionare la casella di controllo per fare in modo che tutti gli amministratori globali ricevano notifiche di posta elettronica.
5. Se si desidera ricevere notifiche di posta elettronica ad altri indirizzi di posta elettronica, è possibile specificarli nella casella Destinatario di posta elettronica aggiuntivo. Per rimuovere un indirizzo di posta elettronica da questo elenco, fare clic con il pulsante destro del mouse sulla voce e scegliere Elimina.
6. Per finalizzare le modifiche, fare clic su "Salva". Tutte le modifiche verranno applicate solo dopo avere selezionato "Salva".






## Eliminare un server dal servizio Azure AD Connect Health

In alcuni casi, può essere utile fare in modo che un server non venga più monitorato. Seguire le istruzioni seguenti per rimuovere un server dal servizio Azure AD Connect Health.

Quando si elimina un server, tenere presente quanto segue:

- Questa azione ARRESTERÀ la raccolta di altri dati da tale server. Questo server verrà rimosso dal servizio di monitoraggio. Dopo questa azione, non sarà possibile visualizzare nuovi avvisi né dati di monitoraggio o di analisi di utilizzo per questo server.
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità dal server. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore sul server correlato all'agente per l'integrità.
- Questa azione NON eliminerà i dati già raccolti da questo server. Tali dati verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure.
- Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server.


### Per eliminare un server dal servizio Azure AD Connect Health

1. Aprire il pannello Server dal pannello Elenco server selezionando il nome del server da rimuovere.
2. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
3. Confermare l'azione di eliminazione del server digitando il nome del server nella casella di conferma.
4. Fare clic sul pulsante "Elimina".







## Eliminare un'istanza del servizio dal servizio Azure AD Connect Health

In alcuni casi, può essere utile rimuovere un'istanza del servizio. Seguire le istruzioni seguenti per rimuovere un'istanza del servizio dal servizio Azure AD Connect Health.

Quando si elimina un'istanza del servizio, tenere presente quanto segue:

- Questa azione rimuoverà l'istanza corrente del servizio dal servizio di monitoraggio.
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità da nessun server monitorato come parte di questa istanza del servizio. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore su uno o più server correlati all'agente per l'integrità.
- Tutti i dati di questa istanza del servizio verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure.
- Dopo avere eseguito questa azione, se si desidera iniziare a monitorare il servizio, disinstallare e reinstallare l'agente per l'integrità in tutti i server che verranno monitorati. Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server.


### Per eliminare un'istanza del servizio dal servizio Azure AD Connect Health

1. Aprire il pannello Servizio dal pannello Elenco servizi selezionando l'identificatore del servizio (nome farm) che si desidera rimuovere.
2. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
3. Confermare il nome del servizio digitandolo nella casella di conferma (ad esempio: sts.contoso.com).
4. Fare clic sul pulsante "Elimina".

## Abilitare il controllo per AD FS

Per poter usare questa funzionalità per raccogliere dati e analizzarli, l'agente di Azure AD Connect Health deve poter accedere alle informazioni presenti nei log di controllo di ADFS. Questi log non sono abilitati per impostazione predefinita. Questo requisito si applica solo ai server federativi ADFS. Non è necessario abilitare il controllo nei server proxy ADFS o nei server proxy applicazione Web. Usare le procedure seguenti per abilitare il controllo di ADFS e individuare i log di controllo di ADFS.

#### Per abilitare il controllo per ADFS 2.0

1. Fare clic su **Start**, scegliere **Programmi**, **Strumenti di amministrazione** e quindi fare clic su **Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Gestione diritti utente** e quindi fare doppio clic su Generazione di controlli di sicurezza.
3. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio AD FS 2.0. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Chiudere Criteri di sicurezza locali e quindi aprire lo snap-in Gestione. Per aprire lo snap-in Gestione, fare clic su **Start**, scegliere **Programmi**, scegliere **Strumenti di amministrazione** e quindi fare clic su Gestione AD FS 2.0.
6. Nel riquadro Azioni fare clic su Modifica proprietà servizio federativo.
7. Nella finestra di dialogo **Proprietà servizio federativo** fare clic sulla scheda **Eventi**.
8. Selezionare le caselle di controllo **Operazioni riuscite** e **Operazioni non riuscite**.
9. Fare clic su **OK**.

#### Per abilitare il controllo per ADFS in Windows Server 2012 R2

1. Per passare a **Criteri di sicurezza locali**, aprire **Server Manager** nella schermata Start oppure Server Manager nella barra delle applicazioni sul desktop, quindi fare clic su **Strumenti/Criteri di sicurezza locali**.
2. Passare alla cartella **Impostazioni sicurezza\\Criteri locali\\Assegnazione diritti utente** e quindi fare doppio clic su **Generazione di controlli di sicurezza**.
3. Nella scheda **Impostazioni sicurezza locale** verificare che sia elencato l'account del servizio AD FS. Se l'account non è presente, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco, quindi fare clic su **OK**.
4. Per abilitare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Chiudere **Criteri di sicurezza locali** e quindi aprire lo snap-in di **Gestione AD FS**. In Server Manager fare clic su Strumenti e quindi selezionare Gestione AD FS.
6. Nel riquadro Azioni fare clic su **Modifica proprietà servizio federativo**.
7. Nella finestra di dialogo Proprietà servizio federativo fare clic sulla scheda **Eventi**.
8. Selezionare le caselle di controllo **Operazioni riuscite e Operazioni non riuscite** e quindi fare clic su **OK**.






#### Per individuare i log di controllo di ADFS


1. Aprire il **Visualizzatore eventi**.
2. Passare a Registri di Windows e selezionare **Sicurezza**.
3. A destra fare clic su **Filtra registri correnti**.
4. In Origine evento selezionare **Controllo di ADFS**.

![Log di controllo di ADFS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Se sono presenti criteri di gruppo che disabilitano il controllo ADFS, l'agente di Azure AD Connect Health non sarà in grado di raccogliere informazioni. Assicurarsi che non siano presenti criteri di gruppo che potrebbero disabilitare il controllo.


## Collegamenti correlati

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health per AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO2-->