<properties title="Add a user in RemoteApp" pageTitle="Aggiungere un utente in RemoteApp" description="Informazioni su come aggiungere utenti in RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Come aggiungere un utente in RemoteApp

Prima che gli utenti possano visualizzare e usare le app in RemoteApp, è necessario concedere loro l'accesso. Questo è estremamente semplice: Nella scheda **Accesso utente** immettere le informazioni relative all'account dell'utente a cui concedere l'accesso al servizio.

Quali informazioni sull'account sono necessarie? Dipende dal tipo di raccolta creata (cloud o ibrida) e dall'uso o meno di Office 365 ProPlus in tale raccolta.

##Informazioni sugli account utente per raccolte cloud e ibride
La raccolta cloud supporta gli account Microsoft e gli account aziendali Azure Active Directory con sincronizzazione della directory (che sono anche account di Office 365). 

La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati (mediante uno strumento come DirSync) da una distribuzione di Windows Server Active Directory, e, più in particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata. Per altre informazioni sui requisiti di Azure AD, vedere l'articolo relativo alla [configurazione di Active Directory per Azure RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-ad/).

**Nota:** gli utenti di Azure Active Directory devono essere inclusi nel tenant associato alla sottoscrizione. È possibile visualizzare e modificare la sottoscrizione nella scheda **Impostazioni** del portale. Per altre informazioni, vedere l'articolo relativo alla [modifica del tenant di Azure Active Directory usato da RemoteApp](http://msdn.microsoft.com/it-it/3d6c4fd1-c981-4c57-9402-59fe31b11883).

##Informazioni sugli account utente di Office 365 ProPlus
Se si usa l'immagine modello di Office 365 ProPlus nella raccolta *oppure* se è stata creata un'immagine personalizzata che usa Office 365, è possibile solo aggiungere gli utenti di Azure Active Directory che dispongono di abbonamenti a Office 365 per il dominio predefinito della sottoscrizione. Per altre informazioni, vedere l'articolo relativo all'[uso di Office 365 con Azure RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-o365/).

<!--HONumber=35.2-->
