<properties
	pageTitle="Ulteriori informazioni sulle credenziale e le autorizzazioni di Azure AD Connect | Microsoft Azure"
	description="Descrizione delle impostazioni personalizzate delle credenziali e delle autorizzazioni di Azure AD Connect."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Ulteriori informazioni sulle credenziale e le autorizzazioni di Azure AD Connect


La procedura guidata Azure AD Connect offre due percorsi diversi con requisiti di autorizzazioni distinti:

* In Impostazioni rapide, sono necessari più privilegi per semplificare la definizione della configurazione, senza richiedere agli utenti di creare o configurare separatamente le autorizzazioni.

* In Impostazioni personalizzate viene offerto un maggior numero di scelte e opzioni, ma esistono alcune situazioni in cui è necessario assicurarsi manualmente di disporre delle autorizzazioni corrette.


## Le credenziali che vengono raccolte e quali operazioni sono usate nell'ambito per un'installazione rapida

Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie| Utilizzo
------------- | ------------- |------------- |------------- |
Connettersi ad Azure AD| Credenziali di directory di Azure AD | Ruolo di amministratore in Azure AD | <li>Abilitazione della sincronizzazione nella directory di Azure AD.</li> <li>Creazione dell'account Azure AD che verrà utilizzato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li>
Connettersi ad AD DS | Credenziali Active Directory locali | Membro del gruppo Enterprise Admins (EA) in Active Directory| L'account viene utilizzato come account di AD Connector locale, vale a dire l'account che legge e scrive le informazioni della directory per la sincronizzazione.
ND|Credenziali di accesso dell'utente che esegue la procedura guidata| Amministratore del server locale|Mediante la procedura guidata viene creato l'account di Active Directory che verrà utilizzato come account di accesso del servizio di sincronizzazione nel computer locale.

<br> <br>


## Le credenziali che vengono raccolte e per quali operazioni sono usate nell'ambito di un'installazione personalizzata


Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie| Utilizzo
------------- | ------------- |------------- |-------------
ND|Credenziali di accesso dell'utente che esegue la procedura guidata|Amministratore del server locale| <li>Per impostazione predefinita, mediante la procedura guidata viene creato l'account di Active Directory che verrà utilizzato come account di accesso del servizio di sincronizzazione nel computer locale</li><li>L'account di accesso del servizio di sincronizzazione viene creato solo se l'amministratore non specifica un account particolare</li> <li>L'account è un utente locale, a meno che non si tratti di un controller di dominio. In questo caso l'account è un utente di dominio</li>
Pagina di installazione dei servizi di sincronizzazione, opzione Account di servizio | Active Directory o credenziali dell'account utente locale | Utente locale|Se l'amministratore specifica un account, questo account viene utilizzato come account di accesso per il servizio di sincronizzazione.
Connettersi ad Azure AD|Credenziali di directory di Azure AD| Ruolo di amministratore in Azure AD|Mediante la procedura guidata viene creato l'account di Active Directory che verrà utilizzato come account di accesso del servizio di sincronizzazione nel computer locale.
Connessione delle directory|Credenziali Active Directory locali per ciascuna foresta che sarà connessa ad Azure AD |<li>Il livello minimo di autorizzazioni richiesto per la procedura guidata è Domain user.</li> <li>Tuttavia, l'account specificato deve disporre delle autorizzazioni necessarie per lo scenario previsto.</li><li>Se si prevede di configurare la sincronizzazione delle password con Azure AD, assicurarsi che questo account disponga delle seguenti autorizzazioni assegnate: Replica modifiche directory - Replica di tutte le modifiche directory</li> <li>Se si prevede di configurare la sincronizzazione in modo da eseguire il 'writeback' delle informazioni da Azure AD all'Active Directory locale, verificare l'account disponga delle autorizzazioni di scrittura per gli oggetti directory e gli attributi di cui si desidera eseguire il writeback.</li> <li>Se si prevede di configurare ADFS per il Sign on, verificare che le credenziali di Active Directory fornite per la foresta in cui risiede il server ADFS disponga dei privilegi di amministratore di dominio.</li><li>Vedere la tabella seguente in cui viene fornito un elenco dei requisiti aggiuntivi per lo scenario in uso.</li>|<li>Questo è l’account che verrà utilizzato per l’account dell’agente di gestione dell’Active Directory locale. Da utilizzare per la lettura e la scrittura di oggetti e attributi in Active Directory locale per l'operazione di sincronizzazione ricorrente.</li>
Server ADFS|Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, se le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione|Amministratore di domino|Installazione e configurazione del ruolo del server ADFS.|
Server Proxy applicazione Web |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, se le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione|Amministratore locale nel computer di destinazione.|Installazione e configurazione del ruolo del server WAP.
Credenziali di attendibilità del proxy |Credenziali di attendibilità del servizio federativo (le credenziali che saranno utilizzate dal proxy per richiedere un certificato di attendibilità da FS) |Account di dominio che è un amministratore locale del server ADFS|Registrazione iniziale del certificato di attendibilità di FS-WAP
Pagina Account del servizio ADFS, "Utilizzare un'opzione account utente di dominio"|Credenziali dell'account utente di Active Directory|Utente di dominio|L'account utente di Active Directory di cui vengono fornite le credenziali verrà utilizzato come account di accesso del servizio AD FS.



<br> <br>
## Autorizzazioni necessarie per lo specifico scenario

Scenario |Autorizzazione
------------- | ------------- |
Sincronizzazione delle password| <li>Replica modifiche directory.</li> <li>Replica di tutte le modifiche directory.</li>
Distribuzione ibrida di Exchange|Vedere [Attributi e autorizzazioni di writeback di AAD Sync per la distribuzione ibrida di Exchange con Office 365](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Writeback delle password | <li>Cambia password</li><li>Reimposta password</li>
Writeback di utenti, gruppi e dispositivi|Autorizzazioni di scrittura per gli oggetti di directory e gli attributi di cui si desidera eseguire il "writeback"
Single Sign-On e AD FS| Autorizzazioni di amministratore di dominio nel dominio in cui si trovano i server federati.
Autorizzazioni di SQL Server|L'account del servizio di sincronizzazione AD Connect di Azure richiede autorizzazioni dbo in SQL. Queste autorizzazioni vengono impostate durante l'installazione di AD Connect di Azure e non sono supportate eventuali modifiche.

<br> <br>
## Riepilogo degli account creati da Azure AD Connect



Account creato |Autorizzazioni assegnate | Utilizzo
------------- | ------------- |------------- |
Account Azure AD per la sincronizzazione|Ruolo di sincronizzazione della Directory dedicato|Operazioni di sincronizzazione ricorrenti (account AD MA di Azure)
Impostazioni rapide: account di Active Directory utilizzato per la sincronizzazione|Autorizzazioni di lettura/scrittura per la directory come richiesto per la sincronizzazione + sincronizzazione password|Operazioni di sincronizzazione ricorrenti (account AD MA di Azure)
Impostazioni rapide: account di accesso al servizio di sincronizzazione | Credenziali di accesso dell'utente che esegue la procedura guidata|Account di accesso al servizio di sincronizzazione
Impostazioni personalizzate: account di accesso al servizio di sincronizzazione |ND|Account di accesso al servizio di sincronizzazione
Account AD FS:GMSA (aadcsvc$)|Utente di dominio|Account di accesso al servizio FS


**Risorse aggiuntive**



* [Autorizzazioni per la sincronizzazione delle password](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Autorizzazioni per Exchange ibrido](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Autorizzazioni per il writeback delle password](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect in MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->