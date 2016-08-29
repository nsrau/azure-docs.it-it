<properties 
    pageTitle="Tipi di raccolte disponibili per Azure RemoteApp | Microsoft Azure" 
    description="Informazioni sui tipi di raccolte disponibili con Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />  



# Tipi di raccolte disponibili per Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp consente di condividere applicazioni e risorse con utenti su qualsiasi dispositivo. Questo risultato si ottiene con la creazione di raccolte per contenere le applicazioni e le risorse, quindi con la condivisione di tali raccolte con gli utenti. Esistono 2 diverse opzioni per le raccolte con opzioni differenti per la rete e l'autenticazione. Qual è quella appropriata per ogni esigenza?

Verranno esaminate le diverse considerazioni e le scelte che è necessario effettuare per ottenere il massimo da una raccolta di Azure RemoteApp.


## Principali differenze tra i tipi di raccolta

| | Cloud | Ibrido |
|-----------|-------|--------|
|Usa una rete virtuale esistente| Sì| Sì|
|Richiede account connessi ad Active Directory (DirSync)| No| Sì|
|Richiede l'aggiunta al dominio| No| Sì|
|Richiede un controller di dominio accessibile per la rete virtuale| No| Sì|

## Raccolte nel cloud
- Facile da creare. Il provisioning di questa raccolta è veloce e ciò significa che le applicazioni raggiungono più velocemente gli utenti.
- Applicazioni proprie o condivisione delle applicazioni predefinite. È possibile usare un'immagine personalizzata creata da una VM di Azure oppure una delle immagini incluse nella sottoscrizione.
- Non è necessario configurare una connessione tra la raccolta e il dominio locale.
- È comunque possibile usare facoltativamente la rete virtuale di Azure per fornire l'accesso all'ambiente locale per la condivisione dei dati o per usare l'autenticazione non di Windows per risorse come SQL Server (usando l'autenticazione del database).


Come si crea una raccolta?

- Solo cloud? Usare l'opzione **Creazione rapida** nel portale.
- Cloud + rete virtuale? Usare l'opzione **Crea con rete virtuale** ma NON scegliere l'aggiunta a un dominio.

## Raccolte ibride
- Consentire l'accesso completo alla rete locale e alla rete virtuale di Azure.
- Include l'accesso con aggiunta al dominio per applicazioni e dati. Le applicazioni remote possono eseguire l'autenticazione in Active Directory in locale e possono quindi accedere alle risorse nel dominio.
- Abilitare il monitoraggio e la gestione avanzati con soluzioni System Center e Criteri di gruppo di Windows esistenti (tramite un'immagine personalizzata creata in Windows Server 2012 R2)
- Supportare [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per connettere la rete virtuale di Azure alla rete virtuale locale.

Usare l'opzione **Crea con rete virtuale** e SCEGLIERE l'aggiunta a un dominio.

## Opzioni di autenticazione
Azure RemoteApp supporta sia account Microsoft che account Azure Active Directory, ma non tutte le raccolte supportano tutti i metodi.

| Tipo di account | | Cloud | Cloud + rete virtuale | Ibrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Account Microsoft | | Sì | Sì | No |
| Azure Active Directory (Azure AD) | | | | |
| | Solo Azure AD | Sì | Sì | No |
| | AD Connect con sincronizzazione password | Sì | Sì | Sì |
| | AD Connect senza sincronizzazione password | Sì | Sì | No |
| | AD Connect con AD FS | Sì | Sì | Sì |
| | Provider di identità di terze parti supportati da Azure (come Ping) | Sì | Sì | Sì |
| Autenticazione a più fattori | | Sì | Sì | Sì |



### Cloud e Cloud + rete virtuale 
Con le raccolte nel cloud è possibile usare account Microsoft, account Azure AD o una combinazione dei due. Usare gli account più adatti agli utenti.

Non sono previsti requisiti specifici per l'uso di account Microsoft.

Se si vuole usare gli account Azure AD, è necessario assicurarsi che il tenant di Azure AD corrisponda a quello associato alla sottoscrizione. Durante la creazione della sottoscrizione di Azure RemoteApp, il tenant di Azure AD in uso è stato associato automaticamente alla sottoscrizione. Qualsiasi utente di Azure AD a cui vengono concesse le autorizzazioni deve corrispondere allo stesso tenant. Se necessario, è possibile [modificare il tenant di Azure AD](remoteapp-changetenant.md) associato alla sottoscrizione.
 
### Ibrido (o cloud + Azure AD + Active Directory)

L'uso di Azure AD insieme ad Active Directory in locale è un prerequisito per una raccolta ibrida. È necessario usare AD Connect per integrare le due directory. Esistono però alcune possibilità di scelta per quanto riguarda la configurazione di AD Connect.

Esistono due scenari per AD Connect, ovvero l'uso della sincronizzazione delle password o l'uso della federazione di Active Directory. Vedere le [informazioni su AD Connect](../active-directory/active-directory-aadconnect.md) per individuare lo scenario ottimale per le specifiche esigenze.

È inoltre possibile usare Azure AD e Active Directory con una raccolta nel cloud. Assicurarsi di seguire gli stessi passaggi di configurazione.

Vedere i [requisiti di Azure AD e Active Directory per Azure RemoteApp](remoteapp-ad.md) per informazioni sulla procedura per la configurazione di Azure AD e Active Directory.

## A questo punto è possibile procedere
con la creazione della prima raccolta di Azure RemoteApp.

[Creare una raccolta nel cloud](remoteapp-create-cloud-deployment.md) o [creare una raccolta ibrida](remoteapp-create-hybrid-deployment.md).

<!---HONumber=AcomDC_0817_2016-->