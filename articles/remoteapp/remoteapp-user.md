<properties
    pageTitle="Aggiungere un utente alla raccolta di Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come aggiungere utenti alla raccolta di Azure RemoteApp"
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
    ms.date="03/02/2016"
    ms.author="elizapo" />

# Come aggiungere un utente alla raccolta di Azure RemoteApp

Prima che gli utenti possano visualizzare e usare le app in Azure RemoteApp, è necessario concedere loro l'accesso alla raccolta. Questo è estremamente semplice: nella scheda **Accesso utente** immettere le informazioni relative all'account dell'utente e quindi fare clic sul segno di spunta.

Quali informazioni sull'account sono necessarie? Dipende dal tipo di raccolta creata (cloud o ibrida) e dall'uso o meno di Office 365 ProPlus in tale raccolta.

## Identità degli utenti supportate

I diversi tipi di raccolta (cloud e ibridi) supportano l'uso di diverse identità utente per l'accesso alle applicazioni.

Per una raccolta ibrida di RemoteApp, è necessario configurare un'infrastruttura di dominio Active Directory locale e un tenant di Azure Active Directory con l'integrazione della directory (e, facoltativamente, Single Sign-On). Inoltre, è necessario creare alcuni oggetti Active Directory nella directory locale.

Per una raccolta nel cloud di RemoteApp, a tutti gli utenti che dispongono di identità di supporto per Azure Active Directory è possibile concedere l'accesso utente a RemoteApp in modo che comprenda gli account Microsoft. Vedere la tabella riportata di seguito.

Gli utenti di Office 365 sono utenti di Azure Active Directory. Agli utenti che dispongono di account ibridi con sincronizzazione della directory di Azure Active Directory è possibile concedere l'accesso utente in una distribuzione ibrida di RemoteApp.

È possibile usare questa tabella come riferimento rapido per le identità supportate nella propria raccolta e per i requisiti di Active Directory.

|Account utente |Cloud |Ibrido|
|--------------|--------|------|
|Account Microsoft| 	Sì|	No|
|Azure Active Directory (Azure AD)| | |
|Solo cloud Azure AD |Sì |No |
|Sincronizzazione Active Directory con sincronizzazione password |Sì |Sì |
|Sincronizzazione Active Directory senza sincronizzazione password|	Sì |No |
|Sincronizzazione Active Directory con ADFS |Sì |Sì |
|[Provider di identità di terze parti supportati da Azure ](https://msdn.microsoft.com/library/azure/jj679342.aspx) (ad esempio Ping) |Sì |Sì|
|Autenticazione a più fattori |Sì |Sì |

Per [altre informazioni](remoteapp-ad.md), vedere Configurare Azure Active Directory per RemoteApp.


> [AZURE.NOTE] Gli utenti di Azure Active Directory devono provenire dal tenant associato alla sottoscrizione (è possibile visualizzare e modificare la sottoscrizione nella scheda **Impostazioni** nel portale. Per altre informazioni, vedere l'articolo relativo alla [modifica del tenant di Azure Active Directory usato da RemoteApp](remoteapp-changetenant.md)).

## Informazioni sugli account utente di Office 365 ProPlus
Se si usa l'immagine modello di Office 365 ProPlus nella raccolta *o* se è stata creata un'immagine personalizzata che usa Office 365, è possibile solo aggiungere gli utenti di Azure Active Directory che dispongono di sottoscrizioni a Office 365 per il dominio predefinito della sottoscrizione. Per altre informazioni, vedere l'articolo relativo all'[uso di Office 365 con Azure RemoteApp](remoteapp-o365.md).

<!---HONumber=AcomDC_0309_2016-->