<properties
	pageTitle="Procedure consigliate per modificare la configurazione predefinita | Microsoft Azure"
	description="Fornisce le procedure consigliate per modificare la configurazione predefinita del servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita

Questo argomento descrive le modifiche supportate e non supportate apportate al servizio di sincronizzazione Azure AD Connect.

La configurazione creata da Azure AD Connect funziona "così com'è" per la maggior parte degli ambienti che sincronizzano l'istanza di Active Directory locale con Azure AD. In alcuni casi, tuttavia, è necessario applicare alcune modifiche a una configurazione per soddisfare un'esigenza o un requisito specifico.

## Modifiche apportate all'account del servizio
Il servizio di sincronizzazione Azure AD Connect viene eseguito tramite un account del servizio creato dall'installazione guidata. L'account del servizio contiene le chiavi di crittografia per il database usato dal servizio di sincronizzazione. Viene creato con una password con una lunghezza di 127 caratteri e la password è impostata per non scadere mai.

- La modifica o la reimpostazione della password dell'account del servizio **non è supportata**. Questa operazione comporta l'eliminazione delle chiavi di crittografia impedendo così al servizio di accedere al database e di essere avviato.

## Modifiche apportate all'utilità di pianificazione
Il servizio di sincronizzazione Azure AD Connect è impostato per sincronizzare i dati relativi alle identità ogni 3 ore. Durante l'installazione viene creata un'attività pianificata che viene eseguita tramite un account del servizio con le autorizzazioni per eseguire operazioni sul server di sincronizzazione.

- La modifica dell'attività pianificata **non è supportata**. La password per l'account del servizio non è nota. Vedere [Modifiche apportate all'account del servizio](#changes-to-the-service-account)
- La sincronizzazione in base a una frequenza maggiore delle 3 ore predefinite **non è supportata**.

## Modifiche apportate alle regole di sincronizzazione

Anche se è possibile modificare la configurazione del servizio di sincronizzazione Azure AD Connect, si consiglia di applicare le modifiche con cautela perché si suppone che il servizio di sincronizzazione Azure AD Connect sia il più possibile simile a un dispositivo.

Di seguito è riportato un elenco di comportamenti previsti:

- Dopo l'aggiornamento di Azure AD Connect a una versione più recente, verrà ripristinata la maggior parte delle impostazioni predefinite.
- Le modifiche apportate alle regole di sincronizzazione predefinite vengono perdute dopo aver applicato un aggiornamento.
- Le regole di sincronizzazione predefinite eliminate vengono ricreate durante un aggiornamento a una versione più recente.
- Le regole di sincronizzazione personalizzate create restano inalterate dopo aver applicato un aggiornamento a una versione più recente.



Quando è necessario modificare la configurazione predefinita, eseguire le operazioni seguenti:

- Evitare di modificare un flusso di attributi di una regola di sincronizzazione predefinita. Creare invece una nuova regola di sincronizzazione con una priorità superiore (valore numerico inferiore) che contenga il flusso di attributi necessario.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. In questo modo, viene fornito uno script di PowerShell che è possibile usare per ricrearle facilmente nel caso di uno scenario di ripristino di emergenza.
- Se è necessario modificare l'ambito o l'impostazione di unione in una regola di sincronizzazione predefinita, documentare quest'esigenza e riapplicare la modifica dopo l'aggiornamento a una versione più recente di Azure AD Sync.



## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=August15_HO9-->