<properties
	pageTitle="Azure AD Connect - Requisiti dei certificati SSL | Microsoft Azure"
	description="Requisiti dei certificati SSL di Azure AD Connect da utilizzare con ADFS."
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

# Azure AD Connect - Requisiti dei certificati SSL

**Importante:** è consigliabile utilizzare lo stesso certificato SSL in tutti i nodi della farm ADFS, e in tutti i server proxy applicazione Web.

- Questo certificato deve essere un certificato X509.
- È possibile utilizzare un certificato autofirmato nei server federativi in un ambiente lab di testing. Tuttavia, per un ambiente di produzione, si consiglia di ottenere il certificato da una CA pubblica.
	- Se si utilizza un certificato non attendibile pubblicamente, assicurarsi che il certificato installato su ciascun server Proxy applicazione Web sia attendibile nel server locale e in tutti i server federativi
- I certificati basati su chiavi CNG (CryptoAPI next generation) chiavi e i provider di archiviazione chiavi non sono supportati. Ciò significa che è necessario utilizzare un certificato basato su un provider del servizio di crittografia e non su un provider di archiviazione chiavi.
- L'identità del certificato deve corrispondere al nome del servizio federativo (ad esempio, fs.contoso.com).
	- L'identità è un'estensione nome alternativo del soggetto (SAN) di tipo dNSName o, se non sono presenti voci SAN, il nome del soggetto specificato come nome comune.  
	- Nel certificato possono essere presenti più voci SAN, purché una di esse corrisponda al nome del servizio federativo.
	- Se si pianifica di utilizzare l’aggiunta alla rete aziendale, è necessario un SAN aggiuntivo con il valore "enterpriseregistration." seguito dal suffisso nome dell’entità utente (UPN) dell'organizzazione, ad esempio, enterpriseregistration.contoso.com.

I certificati con caratteri jolly sono supportati.

<!---HONumber=Oct15_HO3-->