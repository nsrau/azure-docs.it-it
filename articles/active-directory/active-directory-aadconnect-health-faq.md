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
	ms.date="08/14/2015"
	ms.author="billmath"/>


# Domande frequenti su Azure AD Connect Health

Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## Domande generali



**D: Si dispone di diversi tenant in Azure Active Directory. Come è possibile passare a quello con Azure Active Directory Premium?**

Per passare al tenant di Azure AD, selezionare "Home" nella barra di spostamento sinistra, selezionare l'utente attualmente connesso in Nome utente nell'angolo in alto a destra e scegliere l'account del tenant corretto. Se l'account del tenant non è elencato qui, disconnettersi e usare le credenziali di amministratore globale del tenant di Azure Active Directory Premium per eseguire l'accesso.


## Domande sull'installazione



**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione dell'agente per l'integrità delle identità Microsoft nei server ADFS è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda di rete e archiviazione.

I numeri indicati di seguito sono approssimativi.

- Utilizzo della CPU: ~1% di aumento
- Utilizzo della memoria: fino a al 10% della memoria di sistema totale
- Utilizzo della larghezza di banda di rete: ~1 MB/1000 richieste ADFS
>[AZURE.NOTE]Se l'agente non è in grado di comunicare con Azure, archivierà i dati in locale, fino al limite massimo del 10% della memoria di sistema totale. Se l'agente raggiunge il 10% di tale memoria fisica totale e non è stato in grado di caricare i dati nel servizio, le nuove transazioni di ADFS sovrascriveranno tutte le transazioni memorizzate nella cache in base a quelle gestite meno recentemente.

- Buffer locale per l'agente per l'integrità di AD: ~20 MB
- Archiviazione dei dati necessaria per il canale di controllo


È consigliabile eseguire il provisioning di 1024 MB (1 GB) di spazio su disco per il canale di controllo di ADFS perché gli agenti per l'integrità di AD siano in grado di elaborare tutti i dati.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No, per l'installazione degli agenti non è necessario il riavvio del server. Tuttavia, l'installazione di alcuni passaggi preliminari necessari può richiedere un riavvio del server.

Ad esempio, in Windows Server 2008 R2 l'installazione di .Net 4.5 Framework richiede un riavvio del server.


**Q: Il servizio Azure AD Connect Health funziona tramite un proxy HTTP pass-through?**

Sì, è possibile eseguire il processo di registrazione e le operazioni normali tramite un proxy esplicito configurato per l'inoltro di richieste HTTP in uscita. "Netsh WinHttp set Proxy" non funziona in questo caso, poiché l'agente usa System.Net, invece dei servizi HTTP Microsoft Windows, per creare richieste Web.

Eseguire questa operazione in qualsiasi momento prima dell'esecuzione di Register-AdHealthAgent, ovvero il passaggio finale dell'installazione.


- Passaggio 1: Aggiungere una voce al file machine.config


Individuare il file machine.config. Il file si trova in %windir%\Microsoft.NET\Framework64[versione]\config\machine.config</li>

Aggiungere la voce seguente nell'elemento <configuration></configuration> nel file machine.config.
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

Per altre informazioni su <defaultProxy>, fare clic [qui](https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110)).

Questa impostazione permette di configurare le applicazioni .NET a livello di sistema in modo che usino il proxy definito in modo esplicito quando effettuano richieste .NET HTTP. La modifica dei singoli file app.config non è consigliata, perché verrà annullata durante gli aggiornamenti automatici. È sufficiente modificare un file e le modifiche verranno mantenute anche in caso di applicazione di aggiornamenti, se si modifica solo il file machine.config.

- Passaggio 2: Configurare il proxy in Opzioni Internet

Aprire Internet Explorer -> Impostazioni -> Opzioni Internet -> Connessioni -> Impostazioni LAN.

Selezionare Utilizza un server proxy per le connessioni LAN

Selezionare Avanzate SE sono presenti porte proxy diverse per HTTP e HTTPS/Protetto




**D: I servizi di Azure AD Connect Health supportano l'autenticazione di base durante la connessione a proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.





## Domande sulle operazioni



**Q: È necessario abilitare il controllo nei server proxy applicazione ADFS o nei server proxy applicazione Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione ADFS né nei server proxy applicazione Web, ma solo nei server federati ADFS.



**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. Ovvero, se entro 48 ore dalla generazione dell'avviso viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.




**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

È necessario che siano aperte le porte TCP/UDP 80 e 443 perché l'agente di Azure AD Connect Health possa comunicare con gli endpoint del servizio Azure AD Connect Health.

## Collegamenti correlati

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health per AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)

<!----HONumber=August15_HO9-->