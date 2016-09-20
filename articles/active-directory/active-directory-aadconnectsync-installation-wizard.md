<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: eseguire l'installazione guidata una seconda volta | Microsoft Azure"
	description="Spiega come funziona la procedura di installazione guidata la seconda volta che viene eseguita."
	keywords="L'installazione guidata di Azure AD Connect consente di configurare le impostazioni di manutenzione quando viene eseguita la seconda volta"
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
	ms.date="08/31/2016"
	ms.author="andkjell"/>  


# Servizio di sincronizzazione Azure AD Connect: eseguire l'installazione guidata una seconda volta
La prima volta che si esegue l'installazione guidata di Azure AD Connect, viene illustrata la procedura di configurazione dell'installazione. Se si esegue nuovamente l'installazione guidata, verranno messe a disposizione le opzioni di manutenzione.

L'installazione guidata si trova nel menu Start **Azure AD Connect**.

![Menu Start](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)  

Quando si avvia l'installazione guidata viene visualizzata una pagina con le opzioni seguenti:

![Pagina con un elenco di attività aggiuntive](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)  

Se con Azure AD Connect è stato installato AD FS saranno disponibili ancora più opzioni. Le opzioni aggiuntive per AD FS sono descritte in [Gestione di AD FS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Selezionare una delle attività e fare clic su **Avanti** per continuare.

> [AZURE.IMPORTANT] Mentre l'installazione guidata è aperta, tutte le operazioni nel motore di sincronizzazione vengono sospese. Assicurarsi di chiudere l'installazione guidata subito dopo aver completato le modifiche della configurazione.

## Visualizzazione della configurazione corrente
Questa opzione consente di visualizzare rapidamente le opzioni attualmente configurate.

![Pagina con un elenco di tutte le opzioni e il relativo stato](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)  

Fare clic su **Indietro** per tornare alla pagina precedente. Se si seleziona **Esci**, l'installazione guidata verrà chiusa.

## Personalizzazione delle opzioni di sincronizzazione
Questa opzione consente di apportare modifiche alla configurazione della sincronizzazione. Viene visualizzato un sottoinsieme di opzioni dal percorso di installazione personalizzato della configurazione. Queste opzioni vengono visualizzate anche se inizialmente è stata usata l'installazione rapida.

- [Add more directories](active-directory-aadconnect-get-started-custom.md#connect-your-directories) (Aggiunta di altre directory). Per rimuovere una directory, vedere [Eliminazione di un connettore](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Change Domain and OU filtering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) (Modifica dei filtri di unità organizzativa e dominio).
- Remove Group filtering (Rimozione dei filtri di gruppo).
- [Change optional features](active-directory-aadconnect-get-started-custom.md#optional-features) (Modifica delle funzionalità facoltative).

Le altre opzioni dell'installazione iniziale non possono essere modificate e non sono disponibili. Queste opzioni sono:

- Modifica dell'attributo da usare per userPrincipalName e sourceAnchor.
- Modifica del metodo di abbinamento per gli oggetti di un'altra foresta.
- Abilitazione dei filtri basati sui gruppi.

## Aggiorna lo schema della directory
Questa opzione viene usata se lo schema è stato modificato in una delle foreste AD DS locali. Potrebbe ad esempio essere installato Exchange o l'aggiornamento a uno schema di Windows Server 2012 con oggetti dispositivo. In questo caso è necessario indicare ad Azure AD Connect di leggere nuovamente lo schema da AD DS e aggiornare la propria cache. Con questa azione verranno anche rigenerate le regole di sincronizzazione. Se si aggiunge lo schema di Exchange, ad esempio, le regole di sincronizzazione per Exchange vengono aggiunte alla configurazione.

Quando si seleziona questa opzione, vengono elencate tutte le directory presenti nella configurazione. È possibile mantenere l'impostazione predefinita e aggiornare tutte le foreste oppure deselezionare alcune di esse.

![Pagina con un elenco di tutte le directory nell'ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## Configurazione della modalità di gestione temporanea
Questa opzione consente di abilitare e disabilitare la modalità di gestione temporanea nel server. Per altre informazioni sull'uso della modalità di gestione temporanea, vedere [Operazioni](active-directory-aadconnectsync-operations.md#staging-mode).

L'opzione indicherà se la gestione temporanea è attualmente abilitata o disabilitata: ![Opzione che visualizza anche lo stato attuale della modalità di gestione temporanea](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Per modificare lo stato, selezionare questa opzione e selezionare o deselezionare la casella di controllo. ![Opzione che visualizza anche lo stato attuale della modalità di gestione temporanea](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## Cambia l'accesso utente
Questa opzione consente di passare dalla sincronizzazione password alla federazione e viceversa. Non è possibile passare a **Non configurare**.

Per altre informazioni su questa opzione, vedere [Accesso utente](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## Passaggi successivi

- Per altre informazioni sul modello di configurazione usato dal servizio di sincronizzazione Azure AD Connect, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Argomenti generali**

- [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->