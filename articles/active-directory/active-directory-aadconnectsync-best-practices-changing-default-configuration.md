<properties
	pageTitle="Procedure consigliate per modificare la configurazione predefinita | Microsoft Azure"
	description="Fornisce le procedure consigliate per modificare la configurazione predefinita del servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
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

L'installazione guidata fornisce una configurazione valida per gli scenari più comuni. Nel caso in cui sia necessario apportare modifiche alla configurazione, seguire queste regole per disporre ancora di una configurazione supportata.

- L'unica modifica supportata per una regola di sincronizzazione predefinita è una modifica per la disabilitazione. Qualsiasi altra modifica potrebbe andare perse durante un aggiornamento.
- Se è necessario apportare qualsiasi altra modifica a una regola predefinita, effettuarne una copia e disabilitare la regola originale. A tale scopo, avviare e usare l'editor delle regole di sincronizzazione.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. In questo modo, viene fornito uno script di PowerShell che è possibile usare per ricrearle facilmente nel caso di uno scenario di ripristino di emergenza.

>[AZURE.WARNING]Le regole di sincronizzazione predefinite hanno un'identificazione personale associata. Se si apporta una modifica a queste regole, l'identificazione personale non corrisponderà più e potrebbero verificarsi problemi quando si tenta di applicare una nuova versione di Azure AD Connect. Apportare modifiche solo nel modo descritto in questo articolo.

### Eliminare una regola di sincronizzazione indesiderata
Non eliminare una regola di sincronizzazione predefinita; verrà ricreata durante l'aggiornamento successivo.

In alcuni casi l'installazione guidata ha generato una configurazione non adatta alla topologia dell'utente. Ad esempio, se si dispone di una topologia di foresta di account-risorse ma lo schema nella foresta degli account è stato esteso con lo schema Exchange, le regole per lo schema verranno create per la foresta degli account e per quella delle risorse. In questo caso è necessario disabilitare la regola di sincronizzazione per Exchange.

![Regola di sincronizzazione disabilitata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Nell'esempio precedente l'installazione guidata ha rilevato un vecchio schema Exchange 2003 nella foresta degli account. Questo è stato aggiunto prima dell'introduzione della foresta delle risorse nell'ambiente di Fabrikam. Per assicurarsi che non vengano sincronizzati gli attributi dall'implementazione di Exchange precedente, la regola deve essere disabilitata, come illustrato.

### Modifica delle regole predefinite
Se è necessario apportare modifiche a una regola predefinita, effettuarne una copia e disabilitare la regola originale. Quindi apportare le modifiche alla regola clonata. A tale scopo è possibile usare l'editor delle regole di sincronizzazione. Quando si apre una regola predefinita viene visualizzata questa finestra di dialogo:

![Avviso regola predefinita](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selezionare **Sì** per creare una copia della regola. Viene quindi aperta la regola clonata.

![Regola clonata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

In questa regola clonata, apportare le modifiche necessarie all'ambito, al join e alle trasformazioni.

### Non trasmettere un attributo
Sono disponibili due modalità per non trasmettere un attributo. Il primo è disponibile nell'installazione guidata e consente di [rimuovere gli attributi selezionati](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Questa opzione funziona se l'attributo non è mai stato sincronizzato in precedenza. Tuttavia se si è iniziato a sincronizzare questo attributo e lo si rimuoverà in seguito con questa funzionalità, il motore di sincronizzazione interromperà la gestione dell'attributo e i valori esistenti verranno lasciati in Azure AD.

Se si desidera rimuovere il valore di un attributo e assicurarsi che non venga trasmesso in futuro, sarà necessario creare una regola personalizzata.

In Fabrikam è stato notato che alcuni degli attributi sincronizzati nel cloud non dovrebbero trovarsi in tale posizione. Si desidera assicurarsi che questi attributi vengano rimossi da Azure AD.

![Attributi di estensione](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Creare una nuova regola di sincronizzazione in ingresso e popolare la descrizione ![Descrizioni](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Creazione flussi di attributi di tipo **Expression** e con origine **AuthoritativeNull**. Il valore letterale **AuthoritativeNull** indica che il valore dovrebbe essere vuoto in MV anche se una regola di sincronizzazione di precedenza inferiore tenta di popolare il valore. ![Attributi di estensione](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Salvare la regola di sincronizzazione Avviare il **servizio di sincronizzazione**, trovare il connettore, selezionare **Esegui** e avviare una **sincronizzazione completa**. Questo consentirà di ricalcolare tutti i flussi degli attributi.
- Verificare che le modifiche richieste stiano per essere esportate mediante la ricerca dello spazio connettore. ![Eliminazione di gestione temporanea](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## Passaggi successivi
Altre informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO4-->