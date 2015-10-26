<properties
	pageTitle="Azure Active Directory: Creare un argomento di supporto al tenant | Microsoft Azure"
	description="Creazione di un tenant di Azure Active Directory o un tenant di Azure Active Directory B2C - problemi e risoluzioni"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Creazione di un Tenant di Azure AD o di un Tenant di Azure AD B2C - Problemi e risoluzioni

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creazione di un Tenant di Azure AD

Se non è possibile creare un tenant di Azure AD la prima volta, riprovare. Se il problema persiste, contattare il supporto tecnico.

## Creazione di un Tenant di Azure AD B2C (anteprima)

Sono stati rilevati alcuni problemi che possono verificarsi durante la [creazione di un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

- Se il tenant di Azure AD B2C non viene visualizzato nell'elenco di tenant, riprovare.
- Se il tenant di Azure AD B2C viene visualizzata nell'elenco dei tenant, ma si visualizza il messaggio di errore "Impossibile completare la creazione del tenant di B2C 'contosob2c'. Visitare questo [collegamento](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) per ulteriori informazioni. ", quindi effettuare una delle operazioni seguenti:
    - Passare al pannello delle funzionalità di B2C nel portale di anteprima di Azure utilizzando questo collegamento [https://portal.azure.com/ {tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com), dove **{tenant}** deve essere sostituita con il nome utilizzato al momento della creazione del tenant (ad esempio, contosob2c) e accedere come amministratore globale. Fare clic su **Abilita funzionalità B2C** nella parte superiore del pannello e fare clic su **OK**. Il tenant di Azure AD B2C dovrebbe essere pronta per l'utilizzo.
	- Eliminare il tenant appena creata e riprovare.
- Se nessuna delle soluzioni elencate sopra risolve il problema, contattare il Supporto. Leggere [questo articolo](active-directory-b2c-support.md) su come presentare richieste di supporto per Azure AD B2C.

<!---HONumber=Oct15_HO3-->