<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Interfaccia utente di Synchronization Service Manager | Microsoft Azure"
	description="Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect."
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
	ms.date="09/07/2016"
	ms.author="andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Synchronization Service Manager

[Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

La scheda Connettori consente di gestire tutti i sistemi a cui il motore di sincronizzazione è connesso.

## Azioni del connettore

Azione | Commento
--- | ---
Create | Non usare. Per la connessione ad altre foreste AD, usare l'installazione guidata.
Proprietà | Si usa per i filtri di unità organizzativa e dominio.
[Eliminazione](#delete) | Si usa per eliminare i dati nello spazio connettore o per eliminare la connessione a una foresta.
[Configura profili di esecuzione](#configure-run-profiles) | Fatta eccezione per i filtri di dominio, qui non è richiesta alcuna configurazione. Questa azione consente di visualizzare i profili di esecuzione già configurati.
Esegui | Si usa per avviare l'esecuzione occasionale di un profilo.
Arresto | Arresta un connettore che sta eseguendo attualmente un profilo.
Esporta connettore | Non usare.
Importa connettore | Non usare.
Aggiorna connettore | Non usare.
Aggiorna schema | Aggiorna lo schema memorizzato nella cache. È preferibile usare l'opzione nell'installazione guidata perché aggiorna anche le regole di sincronizzazione.
[Spazio connettore di ricerca](#search-connector-space) | Consente di trovare oggetti e [seguire un oggetto e i relativi dati attraverso il sistema](#follow-an-object-and-its-data-through-the-system).

### Elimina
L'azione di eliminazione viene usata per due scopi diversi. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L'opzione **Delete connector space only** (Elimina solo lo spazio connettore) rimuove tutti i dati, ma mantiene la configurazione.

L'opzione **Delete Connector and connector space** (Elimina connettore e spazio connettore) rimuove i dati e la configurazione. Questa opzione viene usata quando non si intende più connettersi a una foresta.

Entrambe le opzioni sincronizzano tutti gli oggetti e aggiornano gli oggetti del metaverse. L'esecuzione di questa azione richiede molto tempo.

### Configura profili di esecuzione
Questa opzione consente di visualizzare i profili di esecuzione configurati per un connettore.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### Spazio connettore di ricerca
L’azione Cerca spazio connettore è utile per trovare oggetti e risolvere problemi relativi ai dati.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Iniziare selezionando un **ambito**. È possibile eseguire ricerche in base ai dati (RDN, DN, Ancoraggio, Sottoalbero) o allo stato dell'oggetto (tutte le altre opzioni). ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png) Se ad esempio si esegue una ricerca nel sottoalbero, si ottengono tutti gli oggetti in una OU. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) Da questa griglia è possibile selezionare un oggetto, selezionare **Properties** (Proprietà) e [seguirlo](#follow-an-object-and-its-data-through-the-system) dallo spazio connettore di origine attraverso il metaverse e fino allo spazio connettore di destinazione.

## Seguire un oggetto e i relativi dati attraverso il sistema
In fase di risoluzione di un problema relativo ai dati, seguire un oggetto dallo spazio connettore di origine fino al metaverse e allo spazio connettore di destinazione è una procedura fondamentale per capire perché i dati non hanno i valori previsti.

### Proprietà dell’oggetto spazio connettore
**Import (Importa)** Quando si apre un oggetto cs, nella parte superiore sono presenti diverse schede. La scheda **Import** (Importa) visualizza i dati di gestione temporanea dopo l'importazione. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) **Old Value** (Valore precedente) indica il valore attualmente presente nel sistema, mentre **New Value** (Nuovo valore) indica il valore ricevuto dal sistema di origine e non ancora applicato. In questo caso è impossibile applicare la modifica perché si è verificato un errore di sincronizzazione.

**Error (Errore)** La pagina di errore è visibile solo se si è verificato un problema con l'oggetto. Per altre informazioni su come [risolvere gli errori di sincronizzazione](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab), vedere i dettagli nella pagina Operations (Operazioni).

**Derivazione** La scheda Lineage (Derivazione) visualizza la correlazione fra l'oggetto spazio connettore e l'oggetto metaverse. È possibile vedere l'ultima volta in cui il connettore ha importato una modifica dal sistema connesso e quali regole sono state applicate per popolare i dati nel metaverse. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) Nella colonna **Action** (Azione) è presente una regola di sincronizzazione con direzione **Inbound** (In ingresso) e azione **Provision** (Provisioning). Questo indica che l'oggetto metaverse rimarrà fino a quando sarà presente l'oggetto spazio connettore. Se l'elenco delle regole di sincronizzazione visualizza invece una regola di sincronizzazione con direzione **Outbound** (In uscita) e azione **Provision** (Provisioning), questo oggetto verrà eliminato quando sarà eliminato l'oggetto metaverse. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) Si noti anche che nella colonna **PasswordSync** (Sincronizzazione password) lo spazio connettore in ingresso può apportare modifiche alla password poiché una regola di sincronizzazione ha il valore **True**. Questa password viene poi inviata ad Azure AD attraverso la regola in uscita.

Dalla scheda Lineage (Derivazione) è possibile accedere al metaverse facendo clic su [Metaverse Object Properties](#metaverse-object-properties) (Proprietà oggetto metaverse).

Nella parte inferiore di tutte le schede sono disponibili due pulsanti: **Preview** (Anteprima) e **Log**.

**Anteprima** La pagina Preview (Anteprima) viene usata per sincronizzare un solo oggetto. È utile mentre si risolvono problemi relativi a regole di sincronizzazione di un cliente e si vuole vedere l'effetto di una modifica su un singolo oggetto. È possibile scegliere tra **Full Sync** (Sincronizzazione completa) e **Delta sync** (Sincronizzazione differenziale). È anche possibile scegliere tra **Generate Preview** (Genera anteprima), che manterrà la modifica solo in memoria, e **Commit Preview** (Esegui commit anteprima) che inserirà tutte le modifiche negli spazi connettore di destinazione. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) Si può esaminare l'oggetto e individuare la regola applicata per un particolare flusso di attributi. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Log** La pagina Log visualizza lo stato e la cronologia della sincronizzazione delle password. Per altre informazioni, vedere [Risoluzione dei problemi di sincronizzazione password](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).

### Proprietà oggetto metaverse
**Attributes (Attributi)** Nella scheda Attributes (Attributi) è possibile visualizzare i valori e il connettore che li ha indicati. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png) **Connectors (Connettori)** La scheda Connectors (Connettori) visualizza tutti gli spazi connettore che hanno una rappresentazione dell'oggetto. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Questa scheda consente anche di accedere all'[oggetto spazio connettore](#connector-space-object-properties).

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0907_2016-->