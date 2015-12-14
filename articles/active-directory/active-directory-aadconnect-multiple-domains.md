<properties
	pageTitle="Domini multipli di Azure AD Connect"
	description="Questo documento descrive l'impostazione e la configurazione di più domini di primo livello con Office 365 e Azure AD."
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
	ms.date="12/02/2015"
	ms.author="billmath"/>

#Supporto domini multipli

Molti clienti hanno chiesto come è possibile configurare domini multipli di primo livello di Office 365 o domini di Azure AD e sotto-domini per la federazione. Sebbene quasi tutto ciò possa essere eseguito in modo piuttosto semplice, grazie ad alcune operazioni che effettuiamo dietro le quinte, esistono alcuni suggerimenti e consigli utili per evitare i problemi seguenti

- Messaggi di errore durante il tentativo di configurazione di domini aggiuntivi per la federazione
- Gli utenti nei sotto-domini non sono in grado di effettuare l'accesso dopo la configurazione di più domini di primo livello per la federazione

## Domini multipli di primo livello
Verrà illustrata la configurazione di un’organizzazione di esempio contoso.com che desidera un dominio aggiuntivo denominato fabrikam.com.

Si suppone che nel sistema in locale, sia configurato AD FS con il nome di servizio federativo fs.jenfield.com.

Quando si effettua per la prima volta l'iscrizione a Office 365 o Azure AD, configurare contoso.com come primo dominio di accesso. È possibile farlo tramite Azure AD Connect o Azure AD Powershell utilizzando New-MsolFederatedDomain.

Al termine, esaminare i valori predefiniti per due delle nuove proprietà di configurazione del nuovo dominio di Azure AD (possono essere sottoposti a query tramite Get-MsolDomainFederationSettings):

| Nome proprietà | Valore | Descrizione|
| ----- | ----- | -----|
|IssuerURI | http://fs.jenfield.com/adfs/services/trust| Anche se questa proprietà è simile a un URL, è semplicemente un nome per il sistema di autenticazione in locale e pertanto il percorso non deve corrispondere a nessun elemento. Per impostazione predefinita, Azure AD lo imposta al valore dell'identificatore del servizio federativo nella configurazione locale di AD FS.
|PassiveClientSignInUrl|https://fs.jenfield.com/adfs/ls/|This è il percorso in cui vengono inviate le richieste di accesso passivo e risolve al sistema AD FS effettivo. In realtà esistono diverse proprietà "*Url", ma è sufficiente esaminare un esempio per illustrare la differenza tra questa proprietà e un URI, ad esempio la proprietà IssuerURI.

Supponiamo ora di aggiungere il secondo dominio fabrikam.com. Anche in questo caso è possibile farlo eseguendo una seconda volta la procedura guidata di Azure AD Connect o tramite PowerShell.

Se si tenta di aggiungere il secondo dominio come federato con Azure AD PowerShell, si ottiene un errore.

Il motivo è un vincolo in Azure AD per cui IssuerURI non può avere lo stesso valore in più di un dominio. Per superare questa limitazione, è necessario utilizzare un IssuerURI diverso per il nuovo dominio, tramite il parametro SupportMultipleDomain. Quando utilizzato con i cmdlet per configurare la federazione (New-, Convert e Update-MsolFederatedDomain), questo parametro permette ad Azure AD di configurare IssuerURI in base al nome del dominio che deve essere univoco tra tenant in Azure AD e che pertanto deve essere univoco. È inoltre disponibile una modifica alle regole di attestazione che verrà esaminata più avanti.

Pertanto, in Powershell, se si aggiunge fabrikam.com utilizzando il parametro SupportMultipleDomain,

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

si ottiene la seguente configurazione in Azure AD:

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

Si noti che mentre IssuerURI è stata impostato su un valore basato sul dominio ed è pertanto univoco, i valori dell'url dell'endpoint sono ancora configurati per fare riferimento al servizio federativo in fs.jenfield.com, come per il dominio contoso.com originale. In questo modo tutti i domini punteranno ancora allo stesso sistema di AD FS.

SupportMultipleDomain inoltre garantisce che il sistema di AD FS includa il valore corretto dell'Issuer nei token rilasciati per Azure AD prelevando la porzione del dominio relativa all’upn degli utenti e impostandola come dominio nell’issuerURI, vale a dire https://{upn suffix}/ adfs/services/trust. In questo modo durante l'autenticazione in Azure AD o Office 365, l'elemento Issuer nel token dell'utente viene utilizzato per individuare il dominio in Azure AD. Se non viene rilevata una corrispondenza l'autenticazione avrà esito negativo.

Ad esempio, se l’UPN di un utente è johndoe@fabrikam.com, l'elemento Issuer nei problemi relativi ai token AD FS verrà impostato su http://fabrikam.com/adfs/services/trust. Se questo corrisponde alla configurazione di Azure AD, l'autenticazione avrà esito positivo.

Di seguito è riportata la regola di attestazione personalizzata che implementa questa logica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

A questo punto nella configurazione, si avrà contoso.com prima registrato senza l’opzione supportMultipleDomains e con il valore IssuerURI predefinito. Quando si aggiunge fabrikam.com, è effettivamente necessario assicurarsi che contoso.com venga configurato anche per utilizzare l'opzione SupportMultiple Domains poichè l'aggiornamento della regola di attestazione non invierà mai più il valore predefinito di IssuerURI e l'autenticazione avrà esito negativo a causa di una mancata corrispondenza con IssuerURI. L’utente riceverà una notifica se questo accade, prima di poter utilizzare l'opzione supportMultipleDomain in un dominio diverso.

Per rimediare a questo problema, è necessario aggiornare la configurazione anche per il dominio contoso.com. La procedura guidata di Azure AD Connect permette di capire quando è necessario eseguire quanto sopra descritto e di aggiungere correttamente un secondo dominio. Nel primo passaggio, se si è già nella configurazione SupportMultipleDomain essa non verrà sovrascritta.

In PowerShell, è necessario immettere manualmente l'opzione SupportMultipleDomain.

Vedere di seguito per tutti i passaggi dettagliati per la transizione da un dominio singolo a domini multipli.

Una volta eseguiti tutti i passaggi, si avranno configurati due domini in Azure AD:

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

Ora l'accesso federato per gli utenti da contoso.com e i domini di fabrikam.com sarà funzionante. Rimane ancora un solo problema: l’accesso per gli utenti nei sotto-domini.

##Sotto-domini
Si supponga di aggiungere il sotto-dominio sub.contoso.com ad Azure AD. A causa della modalità in cui Azure AD gestisce i domini, il sotto-dominio erediterà le impostazioni del dominio padre, in questo caso contoso.com. Ciò significa che IssuerURI per user@sub.contoso.com dovrà essere http://contoso.com/adfs/services/trust. Tuttavia la regola standard implementata sopra per

Azure AD, genererà un token con un emittente come http://sub.contoso.com/adfs/services/trust, che non corrisponderà al valore di dominio obbligatorio e l'autenticazione avrà esito negativo. Fortunatamente, è disponibili una soluzione alternativa anche per questo, ma non è stata ancora implementata agli strumenti. È necessario aggiornare il trust della relying party di AD FS per Microsoft Online manualmente.

È necessario configurare la regola di attestazione personalizzata in modo che rimuova tutti i sottodomini dal suffisso UPN dell'utente quando si crea il valore Issuer personalizzato. È possibile trovare la procedura esatta per eseguire questa operazione nella procedura seguente.

In breve, è possibile avere più domini con nomi diversi, nonché sotto-domini tutti federati nello stesso server AD FS. Questo richiede solo pochi passaggi aggiuntivi per verificare che i valori di Issuer siano impostati correttamente per tutti gli utenti.

<!---HONumber=AcomDC_1203_2015-->