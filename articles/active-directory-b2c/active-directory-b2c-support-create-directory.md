<properties
	pageTitle="Azure Active Directory: Creare un argomento di supporto alla directory | Microsoft Azure"
	description="Creazione di una Directory di Azure Active Directory o una directory di Azure Active Directory B2C - problemi e risoluzioni"
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Creazione di una Directory di Azure AD o una directory Azure AD B2C - problemi e risoluzioni

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare una directory di Azure AD

Se non è possibile creare una directory di Azure AD la prima volta, riprovare. Se il problema persiste, contattare il supporto tecnico.

## Creazione di una Directory di Azure AD B2C (anteprima)

Ci sono alcuni problemi noti che potrebbero verificarsi durante la [creazione di una directory di Azure AD B2C](active-directory-b2c-get-started).

- Se la directory di Azure AD B2C non compare nell'elenco delle directory, riprovare.
- Se la directory di Azure AD B2C viene visualizzata nell'elenco delle directory, ma si visualizza il messaggio di errore "Impossibile completare la creazione della directory di B2C 'contosob2c'. Visitare questo [collegamento](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) per ulteriori informazioni. ", quindi effettuare una delle operazioni seguenti:
    - Passare al pannello delle funzionalità di B2C nel portale di anteprima di Azure utilizzando questo collegamento [https://portal.azure.com/ {directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com), dove **{directory}** deve essere sostituita con il nome utilizzato al momento della creazione della directory (ad esempio, contosob2c) e accedere come amministratore globale. Fare clic su **Abilita funzionalità B2C** nella parte superiore del pannello e fare clic su **OK**. La directory di Azure AD B2C dovrebbe essere pronta per l'utilizzo.
	- Eliminare la directory appena creata e riprovare.
- Se nessuna delle soluzioni elencate sopra risolve il problema, contattare il Supporto. Leggere [questo articolo](active-directory-b2c-support.md) su come presentare richieste di supporto per Azure AD B2C.

<!---HONumber=Sept15_HO4-->