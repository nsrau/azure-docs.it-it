<properties
	pageTitle="Procedure consigliate per modificare la configurazione predefinita"
	description="Fornisce le procedure consigliate per modificare la configurazione predefinita del servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita

La configurazione creata da Azure AD Connect funziona "così com'è" per la maggior parte degli ambienti che sincronizzano in locale Active Directory con Azure AD.<br> In alcuni casi, tuttavia, è necessario applicare alcune modifiche a una configurazione per soddisfare un'esigenza o un requisito specifico.

Anche se è possibile modificare la configurazione di Azure AD, si consiglia di applicare le modifiche con cautela perché si suppone che Azure AD sia il più possibile simile a un dispositivo.

Di seguito è riportato un elenco di comportamenti previsti:

- Dopo l'aggiornamento di Azure AD Connect a una versione più recente, verrà ripristinata la maggior parte delle impostazioni predefinite.
- Le modifiche apportate alle regole di sincronizzazione predefinite vengono perdute dopo aver applicato un aggiornamento.
- Le regole di sincronizzazione predefinite eliminate vengono ricreate durante un aggiornamento a una versione più recente.
- Le regole di sincronizzazione personalizzate create restano inalterate dopo aver applicato un aggiornamento a una versione più recente.



Quando è necessario modificare la configurazione predefinita, eseguire le operazioni seguenti:

- Evitare di modificare un flusso di attributi di una regola di sincronizzazione predefinita. Creare invece una nuova regola di sincronizzazione con una priorità superiore (valore numerico inferiore) che contenga il flusso di attributi necessario.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. In questo modo, viene fornito uno script di PowerShell che è possibile usare per ricrearle facilmente nel caso di uno scenario di ripristino di emergenza.
- Se è necessario modificare l'ambito o l'impostazione di unione in una regola di sincronizzazione predefinita, documentare quest'esigenza e riapplicare la modifica dopo l'aggiornamento a una versione più recente di Azure AD Sync.



**Altre note importanti:**

- Se è stata configurata una sincronizzazione dei filtri e delle password basata su attributo, assicurarsi che solo gli oggetti che vengono sincronizzati con Azure AD siano inclusi nell'ambito di sincronizzazione delle password. 





## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->