
<properties 
    pageTitle="Azure AD + requisiti di Active Directory per Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come configurare Active Directory per lavorare con Azure RemoteApp." 
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
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Azure AD + requisiti di Active Directory per Azure RemoteApp



Per la raccolta ibrida di RemoteApp di Azure o per un insieme di cloud che si desidera attuare utilizzando una connessione di Active Directory, è necessario eseguire le operazioni seguenti.

### Connettersi AD Azure e ad Active Directory

Se si desidera connettere il tenant di Azure AD e gli ambienti di Active Directory in locale, utilizzare la connessione di Active Directory. Verranno visualizzati solo [4 clic](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) per connettere le due directory.

Nota - la sincronizzazione delle Directory è necessaria per le raccolte ibride.

### Assicurarsi che il "@domain.com" corrisponda
Prima di iniziare, assicurarsi che il nome UPN per la foresta locale corrisponda al suffisso del dominio di Microsoft Azure.

Dopo aver impostato il suffisso del dominio UPN in Azure AD, tutti gli utenti che accedono in Azure RemoteApp accederanno come "utente @<the suffix you set up>". Assicurarsi che gli utenti possano inoltre accedere con lo stesso user@suffix nel dominio locale. In alcuni casi è possibile impostare un nome di dominio in Azure AD quando si specifica un suffisso di dominio diverso per l'utente locale. In questo caso, gli utenti non potranno connettersi a nessun computer appartenente a un dominio o a risorse tramite Azure RemoteApp.

Ad esempio, se si imposta il suffisso di dominio UPN in AAD come contoso.com, ma alcuni utenti in locale o Active Directory sono configurati per l'accesso con @contoso.uk, allora tali utenti non potranno accedere correttamente alla raccolta ARA. Gli utenti UPN in AAD e in Active Directory devono essere gli stessi affinché l'accesso sia possibile"

### Creare gli oggetti per RemoteApp di Azure
Inoltre, è necessario creare i seguenti oggetti di Active Directory locali:

- Un account del servizio per fornire l'accesso alle risorse di dominio ai programmi RemoteApp unendo gli endpoint di Host sessione Desktop remoto al dominio locale.
- Un'unità organizzativa per contenere oggetti computer di RemoteApp. L'uso dell'unità organizzativa è consigliabile (ma non obbligatorio) per isolare gli account e i criteri che si useranno con RemoteApp.

Entrambi questi oggetti sono necessari quando si crea la raccolta di RemoteApp, sarà dunque necessario innanzitutto eseguire questi passaggi.

<!---HONumber=AcomDC_0114_2016-->