<properties
	pageTitle="Domande frequenti su Azure AD Connect Health"
	description="Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto."
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
	ms.date="07/14/2016"
	ms.author="vakarand"/>


# Domande frequenti su Azure AD Connect Health

Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## Domande generali



**D: Quando si gestiscono più directory di Azure AD, come è possibile passare a quella con Azure Active Directory Premium?**

È possibile spostarsi tra directory di Azure AD diverse selezionando il nome dell'utente connesso nell'angolo in alto a destra e scegliendo l'account appropriato. Se l'account non è incluso nell'elenco, disconnettersi e quindi usare le credenziali di amministratore globale della directory con Azure Active Directory Premium abilitato per l'accesso.

## Domande sull'installazione



**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione dell'agente per l'integrità delle identità Microsoft nei server AD FS è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda della rete e archiviazione.

I numeri indicati di seguito sono approssimativi.

- Utilizzo della CPU: ~1% di aumento
- Utilizzo della memoria: fino a al 10% della memoria di sistema totale
- Utilizzo della larghezza di banda di rete: ~1 MB/1000 richieste ADFS

>[AZURE.NOTE]Nel caso l'agente non sia in grado di comunicare con Azure, l'agente archivierà i dati in locale, fino a un limite massimo definito. Quando l'agente raggiunge il limite, se l'agente non è riuscito a caricare i dati nel servizio, le nuove transazioni AD FS sovrascriveranno eventuali transazioni "memorizzate nella cache" in base all'ordine di gestione a partire da quelle elaborate meno di recente.

- Buffer locale per l'agente per l'integrità di AD: ~20 MB
- Archiviazione dei dati necessaria per il canale di controllo


È consigliabile eseguire il provisioning di 1024 MB (1 GB) di spazio su disco per il canale di controllo di ADFS perché gli agenti per l'integrità di AD siano in grado di elaborare tutti i dati.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No, per l'installazione degli agenti non è necessario il riavvio del server. Tuttavia, l'installazione di alcuni passaggi preliminari necessari può richiedere un riavvio del server.

Ad esempio, in Windows Server 2008 R2 l'installazione di .Net 4.5 Framework richiede un riavvio del server.


**Q: Il servizio Azure AD Connect Health funziona tramite un proxy HTTP pass-through?**

Sì. Per le operazioni in corso è possibile configurare l'agente di Health per l'inoltro delle richieste HTTP in uscita tramite un proxy HTTP. Per altre informazioni, vedere [Configurare gli agenti di Azure AD Connect Health per l'uso del proxy HTTP](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se è necessario configurare un proxy durante la registrazione dell'agente, occorre modificare le impostazioni del proxy di Internet Explorer. <br> Aprire Internet Explorer -> Impostazioni -> Opzioni Internet -> Connessioni -> Impostazioni LAN.<br> Selezionare Utilizza un server proxy per le connessioni LAN.<br> Selezionare Avanzate SE sono presenti porte proxy diverse per HTTP e HTTPS/Protetto.<br>


**D: I servizi di Azure AD Connect Health supportano l'autenticazione di base durante la connessione a proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.


**D: Quali versioni di Servizi di dominio Active Directory sono supportate da Azure AD Connect Health per Servizi di dominio Active Directory?**

Il monitoraggio di Servizi di dominio Active Directory è supportato durante l'installazione nelle versioni del sistema operativo seguenti:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## Domande sulle operazioni



**Q: È necessario abilitare il controllo nei server proxy applicazione ADFS o nei server proxy applicazione Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione ADFS né nei server proxy applicazione Web, ma solo nei server federati ADFS.



**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. Ovvero, se entro 72 ore dalla generazione dell'avviso non viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.




**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

È necessario che siano aperte le porte TCP/UDP 80, 443 e 5671 perché l'agente di Azure AD Connect Health possa comunicare con gli endpoint del servizio Azure AD Connect Health.


**D: Perché vengono visualizzati due server con lo stesso nome nel portale di Azure AD Connect Health?**

Quando si rimuove un agente da un server, il server non viene rimosso automaticamente dal portale di Azure AD Connect. Se è stato quindi rimosso manualmente un agente da un server o se è stato rimosso il server stesso, sarà necessario eliminare manualmente la voce relativa al server dal portale di Azure AD Connect Health. Per altre informazioni, vedere [Eliminare un server o un'istanza del servizio](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance). Se è stata inoltre creata una nuova immagine di un server o se è stato creato un nuovo server con gli stessi dettagli, ad esempio il nome del computer, ma non è stato rimosso il server dal portale di Azure AD Connect Health e quindi è stato installato l'agente sul nuovo server, è possibile che siano ora visualizzate due voci per il server. In questo caso è consigliabile eliminare manualmente la voce appartenente al server precedente. I dati associati a questa voce saranno in genere obsoleti.

## Collegamenti correlati

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0720_2016-->