<properties 
	pageTitle="Operazioni di Azure AD Connect Health." 
	description="Questa pagina relativa ad Azure AD Connect Health descrive le operazioni aggiuntive che possono essere eseguite dopo avere distribuito Azure AD Connect Health." 
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
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Operazioni di Azure AD Connect Health

Il seguente argomento descrive le varie operazioni che possono essere eseguite con Azure AD Connect Health.

## Notifiche tramite posta elettronica
È possibile configurare il servizio Azure AD Connect Health per l'invio di notifiche di posta elettronica quando vengono generati avvisi indicanti che l'infrastruttura del servizio federativo non è integra. Questo si verifica quando viene generato un avviso, ma anche quando viene contrassegnato come risolto. Seguire le istruzioni seguenti per configurare le notifiche di posta elettronica. Si noti che le notifiche di posta elettronica sono disabilitate per impostazione predefinita.


### Per abilitare le notifiche di posta elettronica di Azure AD Connect Health

1. Aprire il pannello Avvisi per la farm per cui si desidera ricevere una notifica di posta elettronica.
1. Fare clic sul pulsante "Impostazioni di notifica" sulla barra delle azioni.
1. Attivare l'opzione Notifica di posta elettronica.
1. Selezionare la casella di controllo per fare in modo che tutti gli amministratori del tenant globali ricevano notifiche di posta elettronica.
1. Se si desidera ricevere notifiche di posta elettronica ad altri indirizzi di posta elettronica, è possibile specificarli nelle caselle Destinatari di posta elettronica aggiuntivi. Per rimuovere un indirizzo di posta elettronica da questo elenco, fare clic con il pulsante destro del mouse sulla voce e scegliere Elimina.
1. Per finalizzare le modifiche, fare clic su "Salva". Tutte le modifiche verranno applicate solo dopo avere selezionato "Salva".






## Eliminare un server dal servizio Azure AD Connect Health

In alcuni casi, può essere utile fare in modo che un server non venga più monitorato. Seguire le istruzioni seguenti per rimuovere un server dal servizio Azure AD Connect Health.

Quando si elimina un server, tenere presente quanto segue:

- Questa azione ARRESTERÀ la raccolta di altri dati da tale server. Questo server verrà rimosso dal servizio di monitoraggio. Dopo questa azione, non sarà possibile visualizzare nuovi avvisi né dati di monitoraggio o di analisi di utilizzo per questo server.
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità dal server. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore sul server correlato all'agente per l'integrità.
- Questa azione NON eliminerà i dati già raccolti da questo server. Tali dati verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure. 
- Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server. 


	### Per eliminare un server dal servizio Azure AD Connect Health
<ol>
1. Aprire il pannello Server dal pannello Elenco server selezionando il nome del server da rimuovere. 
1. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
1. Confermare l'azione di eliminazione del server digitando il nome del server nella casella di conferma.
1. Fare clic sul pulsante "Elimina".







## Eliminare un'istanza del servizio dal servizio Azure AD Connect Health

In alcuni casi, può essere utile rimuovere un'istanza del servizio. Seguire le istruzioni seguenti per rimuovere un'istanza del servizio dal servizio Azure AD Connect Health.

Quando si elimina un'istanza del servizio, tenere presente quanto segue:

- Questa azione rimuoverà l'istanza corrente del servizio dal servizio di monitoraggio. 
- Questa azione NON disinstallerà o rimuoverà l'agente per l'integrità da nessun server monitorato come parte di questa istanza del servizio. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati eventi di errore su uno o più server correlati all'agente per l'integrità. 
- Tutti i dati di questa istanza del servizio verranno eliminati secondo i criteri di conservazione dati di Microsoft Azure. 
- Dopo avere eseguito questa azione, se si desidera iniziare a monitorare il servizio, disinstallare e reinstallare l'agente per l'integrità in tutti i server che verranno monitorati. Dopo avere eseguito questa azione, se si desidera ricominciare a monitorare lo stesso server, sarà necessario disinstallare e reinstallare l'agente per l'integrità in questo server.







	### Per eliminare un'istanza del servizio dal servizio Azure AD Connect Health
<ol>
1. Aprire il pannello Servizio dal pannello Elenco servizi selezionando l'identificatore del servizio (nome farm) che si desidera rimuovere. 
1. Nel pannello Server fare clic sul pulsante "Elimina" sulla barra delle azioni.
1. Confermare il nome del servizio digitandolo nella casella di conferma (ad esempio: sts.contoso.com). 
1. Fare clic sul pulsante "Elimina".

<!---HONumber=July15_HO5-->