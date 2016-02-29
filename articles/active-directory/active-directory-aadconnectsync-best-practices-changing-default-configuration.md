<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita | Microsoft Azure"
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
	ms.date="02/16/2016"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita
Questo argomento descrive le modifiche supportate e non supportate apportate al servizio di sincronizzazione Azure AD Connect.

La configurazione creata da Azure AD Connect funziona "così com'è" per la maggior parte degli ambienti che sincronizzano l'istanza di Active Directory locale con Azure AD. In alcuni casi, tuttavia, è necessario applicare alcune modifiche a una configurazione per soddisfare un'esigenza o un requisito specifico.

## Modifiche apportate all'account del servizio
Il servizio di sincronizzazione Azure AD Connect viene eseguito tramite un account del servizio creato dall'installazione guidata. L'account del servizio contiene le chiavi di crittografia per il database usato dal servizio di sincronizzazione. Viene creato con una password con una lunghezza di 127 caratteri e la password è impostata per non scadere mai.

- La modifica o la reimpostazione della password dell'account del servizio **non è supportata**. Questa operazione comporta l'eliminazione delle chiavi di crittografia impedendo così al servizio di accedere al database e di essere avviato.

## Modifiche apportate all'utilità di pianificazione
A partire dalle versioni rilasciate con la build 1.1 (febbraio 2016), è possibile configurare l'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) per ottenere un ciclo di sincronizzazione diverso dai 30 minuti predefiniti.

## Modifiche apportate alle regole di sincronizzazione
L'installazione guidata fornisce una configurazione valida per gli scenari più comuni. Nel caso in cui sia necessario apportare modifiche alla configurazione, seguire queste regole per disporre ancora di una configurazione supportata.

- È possibile [modificare i flussi degli attributi](#change-attribute-flows) se i flussi degli attributi diretti predefiniti non sono adatti alla propria organizzazione.
- Per [non trasmettere un attributo](#do-not-flow-an-attribute) e rimuovere i valori degli attributi esistenti in Azure AD, è necessario creare una regola specifica.
- [Disabilitare una regola di sincronizzazione indesiderata](#disable-an-unwanted-sync-rule) invece di eliminarla. Una regola eliminata verrà ricreata durante un aggiornamento.
- Per [modificare una regola predefinita](#change-an-out-of-box-rule), effettuare una copia della regola originale e disabilitare la regola predefinita. A tale scopo, avviare e usare l'editor delle regole di sincronizzazione.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. In questo modo, viene fornito uno script di PowerShell che è possibile usare per ricrearle facilmente nel caso di uno scenario di ripristino di emergenza.

>[AZURE.WARNING] Le regole di sincronizzazione predefinite hanno un'identificazione personale associata. Se si apporta una modifica a queste regole, l'identificazione personale non corrisponderà più e potrebbero verificarsi problemi quando si tenta di applicare una nuova versione di Azure AD Connect. Apportare modifiche solo nel modo descritto in questo articolo.

### Modificare i flussi degli attributi
In alcuni casi i flussi degli attributi predefiniti non sono adatti a un'organizzazione.

È consigliabile seguire queste regole:

- Creare una nuova regola di sincronizzazione con i propri flussi degli attributi. Assegnandole una precedenza più elevata (valore numerico più basso), le proprie regole eseguiranno l'override dei flussi degli attributi predefiniti.
- Non aggiungere altri flussi a una regola predefinita. Queste modifiche andranno perse in caso di aggiornamento.

In Fabrikam esiste una foresta in cui l'alfabeto locale viene usato per un determinato nome, cognome e nome visualizzato. La rappresentazione in caratteri latini di questi attributi viene archiviata negli attributi di estensione. Quando si compila l'elenco indirizzi globale in Azure AD e Office 365, l'organizzazione vuole invece usarli.

Con una configurazione predefinita, un oggetto della foresta locale sarà simile al seguente:

![Flusso dell'attributo 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

Per creare una regola con altri flussi di attributi, eseguire queste operazioni:

- Avviare **Synchronization Rule Editor** dal menu Start.
- Con **Inbound** selezionato a sinistra, fare clic sul pulsante **Add new rule**.
- Assegnare alla regola un nome e una descrizione. Selezionare la directory Active Directory locale e i tipi di oggetto pertinenti. In **Link Type** selezionare **Join**. Per la precedenza scegliere un numero non usato da un'altra regola. Le regole predefinite iniziano con 100, quindi in questo esempio si può usare il valore 50. ![Flusso dell'attributo 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- Lasciare vuoto l'ambito (perché deve essere applicabile a tutti gli oggetti utente della foresta).
- Lasciare vuote le regole di join (per consentire alla regola predefinita di gestire tutti i join).
- In Transformations creare i flussi seguenti. ![Flusso dell'attributo 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- Fare clic su **Add** per salvare la regola.
- Andare a **Synchronization Service Manager**. In **Connectors** selezionare il connettore in cui è stata aggiunta la regola. Selezionare **Run** e **Full Synchronization**. Una sincronizzazione completa ricalcolerà tutti gli oggetti usando le regole correnti.

Il seguente è il risultato finale per lo stesso oggetto con questa regola personalizzata:

![Flusso dell'attributo 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### Non trasmettere un attributo
Sono disponibili due modalità per non trasmettere un attributo. Il primo è disponibile nell'installazione guidata e consente di [rimuovere gli attributi selezionati](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Questa opzione funziona se l'attributo non è mai stato sincronizzato in precedenza. Tuttavia se si è iniziato a sincronizzare questo attributo e lo si rimuoverà in seguito con questa funzionalità, il motore di sincronizzazione interromperà la gestione dell'attributo e i valori esistenti verranno lasciati in Azure AD.

Se si desidera rimuovere il valore di un attributo e assicurarsi che non venga trasmesso in futuro, sarà necessario creare una regola personalizzata.

In Fabrikam è stato notato che alcuni degli attributi sincronizzati nel cloud non dovrebbero trovarsi in tale posizione. Si desidera assicurarsi che questi attributi vengano rimossi da Azure AD.

![Attributi di estensione](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Creare una nuova regola di sincronizzazione in ingresso e popolare la descrizione ![Descrizioni](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Creazione flussi di attributi di tipo **Expression** e con origine **AuthoritativeNull**. Il valore letterale **AuthoritativeNull** indica che il valore dovrebbe essere vuoto in MV anche se una regola di sincronizzazione di precedenza inferiore tenta di popolare il valore. ![Attributi di estensione](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Salvare la regola di sincronizzazione Avviare il **servizio di sincronizzazione**, trovare il connettore, selezionare **Esegui** e avviare una **sincronizzazione completa**. Questo consentirà di ricalcolare tutti i flussi degli attributi.
- Verificare che le modifiche richieste stiano per essere esportate mediante la ricerca dello spazio connettore. ![Eliminazione di gestione temporanea](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### Disabilitare una regola di sincronizzazione indesiderata
Non eliminare una regola di sincronizzazione predefinita; verrà ricreata durante l'aggiornamento successivo.

In alcuni casi l'installazione guidata ha generato una configurazione non adatta alla topologia dell'utente. Ad esempio, se si dispone di una topologia di foresta di account-risorse ma lo schema nella foresta degli account è stato esteso con lo schema Exchange, le regole per lo schema verranno create per la foresta degli account e per quella delle risorse. In questo caso è necessario disabilitare la regola di sincronizzazione per Exchange.

![Regola di sincronizzazione disabilitata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Nell'esempio precedente l'installazione guidata ha rilevato un vecchio schema Exchange 2003 nella foresta degli account. Questo è stato aggiunto prima dell'introduzione della foresta delle risorse nell'ambiente di Fabrikam. Per assicurarsi che non vengano sincronizzati gli attributi dall'implementazione di Exchange precedente, la regola deve essere disabilitata, come illustrato.

### Modificare una regola predefinita
Se è necessario apportare modifiche a una regola predefinita, effettuarne una copia e disabilitare la regola originale. Quindi apportare le modifiche alla regola clonata. A tale scopo è possibile usare l'editor delle regole di sincronizzazione. Quando si apre una regola predefinita viene visualizzata questa finestra di dialogo:

![Avviso regola predefinita](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selezionare **Sì** per creare una copia della regola. Viene quindi aperta la regola clonata.

![Regola clonata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

In questa regola clonata, apportare le modifiche necessarie all'ambito, al join e alle trasformazioni.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->