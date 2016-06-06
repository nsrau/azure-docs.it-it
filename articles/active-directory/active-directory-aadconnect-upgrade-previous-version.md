<properties
   pageTitle="Azure AD Connect: Eseguire l'aggiornamento da una versione precedente | Microsoft Azure"
   description="Illustra i diversi metodi per eseguire l'aggiornamento alla versione più recente di Azure Active Directory Connect, tra cui l'aggiornamento sul posto e la migrazione swing."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="05/19/2016"
   ms.author="andkjell"/>

# Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente
Questo argomento descrive i diversi metodi che è possibile usare per aggiornare l'installazione di Azure AD Connect alla versione più recente. È consigliabile mantenersi sempre al passo con le versioni di Azure AD Connect.

Se si vuole eseguire l'aggiornamento da DirSync, vedere invece l'argomento relativo all'[aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Esistono diverse strategie per eseguire l'aggiornamento di Azure AD Connect.

Metodo | Descrizione
--- | ---
[Aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) | Si tratta del metodo più semplice per i clienti con installazione rapida.
[Aggiornamento sul posto](#in-place-upgrade) | Se si ha un singolo server, eseguire un aggiornamento sul posto dell'installazione nello stesso server.
[Migrazione swing](#swing-migration) | Con due server è possibile preparare uno dei due server con la nuova versione e, quando si è pronti, modificare il server attivo.

Per informazioni, vedere l'articolo relativo alle [autorizzazioni necessarie per l'aggiornamento](active-directory-aadconnect-accounts-permissions.md#upgrade).

## Aggiornamento sul posto
Un aggiornamento sul posto è indicato per il passaggio da Azure AD Sync o Azure AD Connect. L'aggiornamento sul posto non è indicato per DirSync o per una soluzione con FIM + Azure AD Connector.

Questo metodo è preferibile se sono presenti un singolo server e meno di 100.000 oggetti. Dopo l'aggiornamento, vengono eseguite un'importazione completa e una sincronizzazione completa se sono state apportate modifiche alle regole di sincronizzazione predefinite. In questo modo, la nuova configurazione verrà applicata a tutti gli oggetti esistenti nel sistema. Questa operazione può richiedere alcune ore, a seconda del numero di oggetti nell'ambito del motore di sincronizzazione. Verrà sospesa la normale sincronizzazione delta pianificata, per impostazione predefinita ogni 30 minuti, mentre continuerà la sincronizzazione delle password. È consigliabile eseguire l'aggiornamento sul posto durante il fine settimana. Se sono state apportate modifiche alla configurazione predefinita con la nuova versione di Azure AD Connect, verrà avviata un'importazione/sincronizzazione differenziale normale.

![Aggiornamento sul posto](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se sono state apportate modifiche alle regole di sincronizzazione predefinite, verranno reimpostate sui valori predefiniti della configurazione al momento dell'aggiornamento. Per assicurarsi che la configurazione venga mantenuta tra un aggiornamento e l'altro, verificare che le modifiche vengano apportate come descritto nell'argomento relativo alle [procedure consigliate per modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## Migrazione swing
Se è presente una distribuzione complessa o molti oggetti, può essere impraticabile eseguire un aggiornamento sul posto nel sistema attivo. Per alcuni clienti questa operazione può richiedere più giorni, durante i quali non verranno elaborate modifiche delta.

Il metodo consigliato è invece una migrazione swing. Per questo metodo sono necessari almeno due server, uno attivo e uno di gestione temporanea. Il server attivo (linee blu continue nell'immagine seguente) sarà responsabile del carico attivo. Il server di gestione temporanea (linee viola tratteggiate nell'immagine seguente) verrà preparato con la nuova versione e, una volta completata la preparazione, verrà reso attivo. Il server precedentemente attivo, su cui ora è installata la versione precedente, diventerà il server di gestione temporanea e verrà aggiornato.

I due server possono usare versioni diverse. Ad esempio il server attivo per il quale è stata pianificata la rimozione delle autorizzazioni può usare Azure AD Sync, mentre il nuovo server di gestione temporanea può usare Azure AD Connect.

![Server di gestione temporanea](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: si è notato che alcuni clienti preferiscono avere tre o quattro server per eseguire questa operazione. Poiché è in corso l'aggiornamento del server di gestione temporanea, durante l'operazione non sarà presente un server di backup in caso di [ripristino di emergenza](active-directory-aadconnectsync-operations.md#disaster-recovery). Con un massimo di quattro server è possibile preparare un nuovo set di server primari o di standby con la nuova versione, per assicurare che sia sempre presente un server di gestione temporanea pronto a sostituire il server attivo.

Questa procedura è indicata anche per il passaggio da Azure AD Sync o da una soluzione con FIM + Azure AD Connector. Questa procedura non è indicata per DirSync, ma è disponibile lo stesso metodo con migrazione swing, chiamato anche distribuzione parallela, con i passaggi per DirSync nell'argomento [Aggiornamento del servizio di sincronizzazione di Microsoft Azure Active Directory (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### Passaggi della migrazione swing

1. Se si usa Azure AD Connect in entrambi i server, assicurarsi che il server attivo e il server di gestione temporanea usino entrambi la stessa versione prima di iniziare l'aggiornamento. Ciò renderà più facile un successivo confronto delle differenze. Se si esegue l'aggiornamento da Azure AD Sync, questi server hanno versioni diverse.
2. Se è stata eseguita una configurazione personalizzata che non è presente nel server di gestione temporanea, seguire i passaggi descritti in [Spostare la configurazione personalizzata dal server attivo al server di gestione temporanea](#move-custom-configuration-from-active-to-staging-server).
3. Se si esegue l'aggiornamento da una versione precedente di Azure AD Connect, aggiornare il server di gestione temporanea alla versione più recente. Se si esegue lo spostamento da Azure AD Sync, installare Azure AD Connect nel server di gestione temporanea.
4. Consentire al motore di sincronizzazione di eseguire un'importazione completa e una sincronizzazione completa nel server di gestione temporanea.
5. Verificare che la nuova configurazione non abbia causato modifiche impreviste seguendo i passaggi descritti nella sezione **Verificare** di [Verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se alcuni elementi non funzionano come previsto, correggerli, eseguire l'importazione e la sincronizzazione, quindi verificare che i dati siano corretti. Questi passaggi sono disponibili nell'argomento collegato.
6. Impostare il server di gestione temporanea come server attivo. Questo è il passaggio finale, dal titolo **Cambiare il server attivo**, di [Verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se si esegue l'aggiornamento di Azure AD Connect, eseguire ora l'aggiornamento del server in modalità di gestione temporanea alla versione più recente. Seguire gli stessi passaggi descritti in precedenza per aggiornare i dati e la configurazione. Se si esegue lo spostamento da Azure AD Sync, è possibile disattivare e rimuovere le autorizzazioni dal server precedente.

### Spostare la configurazione personalizzata dal server attivo al server di gestione temporanea
Se sono state apportate modifiche di configurazione al server attivo, è necessario assicurarsi che le stesse modifiche vengano applicate al server di gestione temporanea.

Le regole di sincronizzazione personalizzate create possono essere spostate con PowerShell. Qualsiasi altra modifica deve essere applicata nello stesso modo in entrambi i sistemi e non è possibile eseguirne la migrazione.

Elementi che devono essere configurati allo stesso modo in entrambi i server:

- Connessione alle stesse foreste.
- Domini e filtri unità organizzativa.
- Stesse funzionalità facoltative, come la sincronizzazione e il writeback delle password.

**Spostare le regole di sincronizzazione** Per spostare una regola di sincronizzazione personalizzata, seguire questa procedura:

1. Aprire l'**editor delle regole di sincronizzazione** nel server attivo.
2. Selezionare la regola personalizzata. Fare clic sul pulsante per l'**esportazione**. Verrà visualizzata una finestra del Blocco note. Salvare il file temporaneo con estensione ps1. In questo modo diventerà uno script PowerShell. Copiare il file con estensione ps1 nel server di gestione temporanea. ![Esportazione delle regole di sincronizzazione](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Il GUID del connettore sarà diverso nel server di gestione temporanea. Per ottenere il GUID, avviare l'**editor di sincronizzazione delle regole**, selezionare una delle regole predefinite che rappresentano lo stesso sistema connesso e fare clic sul pulsante per l'**esportazione**. Sostituire il GUID nel file con estensione ps1 con il GUID del server di gestione temporanea.
4. In un prompt dei comandi di PowerShell eseguire il file con estensione ps1. In questo modo verrà creata la regola di sincronizzazione personalizzata nel server di gestione temporanea.
5. Se sono presenti più regole personalizzate, ripetere la procedura per tutte le regole personalizzate.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0525_2016-->