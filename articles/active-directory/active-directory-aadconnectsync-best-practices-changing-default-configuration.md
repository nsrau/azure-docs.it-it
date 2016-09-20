<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita | Microsoft Azure"
	description="Fornisce le procedure consigliate per modificare la configurazione predefinita del servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="markvi;andkjell"/>  


# Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita
Questo argomento descrive le modifiche supportate e non supportate del servizio di sincronizzazione Azure AD Connect.

La configurazione creata da Azure AD Connect funziona "così com'è" per la maggior parte degli ambienti che sincronizzano l'istanza di Active Directory locale con Azure AD. In alcuni casi, tuttavia, è necessario applicare alcune modifiche a una configurazione per soddisfare un'esigenza o un requisito specifico.

## Modifiche apportate all'account del servizio
Il servizio di sincronizzazione Azure AD Connect viene eseguito tramite un account del servizio creato dall'installazione guidata. L'account del servizio contiene le chiavi di crittografia per il database usato dal servizio di sincronizzazione. Viene creato con una password con una lunghezza di 127 caratteri e la password è impostata per non scadere mai.

- La modifica o la reimpostazione della password dell'account del servizio **non è supportata**. Questa operazione comporta l'eliminazione delle chiavi di crittografia impedendo così l'avvio del servizio e il relativo accesso al database.

## Modifiche apportate all'utilità di pianificazione
A partire dalle versioni rilasciate con la build 1.1 (febbraio 2016), è possibile configurare l'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) per ottenere un ciclo di sincronizzazione diverso dai 30 minuti predefiniti.

## Modifiche apportate alle regole di sincronizzazione
L'installazione guidata fornisce una configurazione valida per gli scenari più comuni. Nel caso in cui sia necessario apportare modifiche alla configurazione, seguire queste regole per disporre ancora di una configurazione supportata.

- È possibile [modificare i flussi degli attributi](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se quelli diretti e predefiniti non sono adatti alla propria organizzazione.
- Per [non trasmettere un attributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e rimuovere i valori degli attributi esistenti in Azure AD, è necessario creare una regola per questo scenario.
- [Disabilitare una regola di sincronizzazione indesiderata](#disable-an-unwanted-sync-rule) invece di eliminarla. Una regola eliminata viene ricreata durante un aggiornamento.
- Per [modificare una regola predefinita](#change-an-out-of-box-rule), creare una copia della regola originale e disabilitare quella predefinita. L'editor delle regole di sincronizzazione visualizza istruzioni e fornisce informazioni.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. L'editor fornisce uno script di PowerShell che è possibile usare per ricrearle facilmente in uno scenario di ripristino di emergenza.

>[AZURE.WARNING] Le regole di sincronizzazione predefinite hanno un'identificazione personale associata. Se si apporta una modifica a queste regole, l'identificazione personale non sarà più corrispondente. È possibile che si verifichino problemi quando in futuro si proverà ad applicare una nuova versione di Azure AD Connect. Apportare modifiche solo nel modo descritto in questo articolo.

### Disabilitare una regola di sincronizzazione indesiderata
Non eliminare una regola di sincronizzazione predefinita. Verrà ricreata durante l'aggiornamento successivo.

In alcuni casi l'installazione guidata ha generato una configurazione che non funziona per la topologia dell'utente. Ad esempio, se è disponibile una topologia di foresta di account o di risorse, ma lo schema nella foresta di account è stato esteso con lo schema di Exchange, per le foresta di account e per quella di risorse vengono quindi create regole per Exchange. In questo caso è necessario disabilitare la regola di sincronizzazione per Exchange.

![Regola di sincronizzazione disabilitata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)  

Nella figura precedente l'installazione guidata ha rilevato un vecchio schema di Exchange 2003 nella foresta di account. Questa estensione dello schema è stata aggiunta prima dell'introduzione della foresta di risorse nell'ambiente di Fabrikam. Per assicurarsi che non vengano sincronizzati gli attributi dall'implementazione di Exchange precedente, la regola di sincronizzazione deve essere disabilitata come illustrato.

### Modificare una regola predefinita
Se è necessario apportare modifiche a una regola predefinita, effettuarne una copia e disabilitare la regola originale. Quindi apportare le modifiche alla regola clonata. L'editor delle regole di sincronizzazione facilita l'esecuzione di questa procedura. Quando si apre una regola predefinita, viene visualizzata questa finestra di dialogo: ![Avviso regola predefinita](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selezionare **Sì** per creare una copia della regola. Viene quindi aperta la regola clonata. ![Regola clonata](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

In questa regola clonata, apportare le modifiche necessarie all'ambito, al join e alle trasformazioni.

## Passaggi successivi

**Argomenti generali**

- [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->