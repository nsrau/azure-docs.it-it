<properties
   pageTitle="Topologie per Azure AD Connect | Microsoft Azure"
   description="Questo argomento illustra in modo dettagliato le topologie supportate e non supportate per Azure AD Connect."
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
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="andkjell"/>

# Topologie per Azure AD Connect

L'obiettivo di questo argomento consiste nell'illustrare le diverse topologie locali e di Azure AD con il servizio di sincronizzazione Azure AD Connect come soluzione di integrazione principale. Illustra le configurazioni supportate e non supportate.

Legenda per le immagini del documento:

| Descrizione | Icona |
|-----|-----|
| Foresta locale di Active Directory | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory con importazione con filtri | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Server di sincronizzazione di Azure AD Connect | ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| Server di sincronizzazione di Azure AD Connect in "Modalità di gestione temporanea" | ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync con FIM2010 o MIM2016 | ![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Dettagli relativi al server di sincronizzazione di Azure AD Connect |![Sincronizzazione](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Directory di Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| Scenario non supportato | ![Non supportato](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## Foresta singola, singola directory di Azure AD
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topologia più comune è costituita da una singola foresta locale, con uno o più domini, e una singola directory di Azure AD, definita anche tenant. L'autenticazione di Azure AD viene eseguita mediante la sincronizzazione delle password. Questa è la topologia supportata dall'installazione rapida di Azure AD Connect.

### Foresta singola, più server di sincronizzazione a una directory di Windows Azure
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Non è supportato lo scenario che prevede più server di sincronizzazione di Azure AD Connect connessi alla stessa directory di Azure AD, anche se sono configurati per sincronizzare set di oggetti che si escludono a vicenda (ad eccezione di un [server di gestione temporanea](#staging-server)). È possibile che si provi a usare questo scenario perché un dominio in una foresta non è raggiungibile da un percorso di rete comune o perché si prova a distribuire il carico di sincronizzazione in più server.

## Più foreste, singola directory di Azure AD
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

In molte organizzazioni sono presenti ambienti che includono più foreste Active Directory locali. Esistono vari motivi per distribuire più di una foresta Active Directory locale. Tra gli esempi tipici sono incluse progettazioni con foreste di risorse di account, foreste relative a fusioni e acquisizioni o foreste usate per la gestione esterna dei dati.

Quando sono presenti più foreste, è necessario che tutte le foreste siano raggiungibili dal singolo server di sincronizzazione di Azure AD. Il server non deve essere aggiunto a un dominio e può essere posizionato in una rete perimetrale, se necessario, per potere raggiungere tutte le foreste.

La procedura guidata di Azure AD Connect offrirà alcune opzioni per il consolidamento degli utenti. Anche se lo stesso utente è rappresentato più volte in diverse foreste, sarà rappresentato solo una volta in Azure AD. Di seguito sono illustrate alcune topologie. Configurare la topologia disponibile usando il percorso di installazione personalizzato nell'installazione guidata, quindi selezionare l'opzione corrispondente nella pagina "Identificazione univoca per gli utenti". Il consolidamento viene eseguito solo per gli utenti. Se duplicati, i gruppi non verranno consolidati con la configurazione predefinita.

Le topologie comuni vengono illustrate nella sezione successive [Topologie separate](#multiple-forests-separate-topologies), [A maglia completa](#multiple-forests-full-mesh-with-optional-galsync) e [Account-Risorse](#multiple-forests-account-resource-forest).

Nella configurazione predefinita disponibile nel servizio di sincronizzazione Azure AD Connect si presuppone quanto segue: 1. Gli utenti hanno solo un account abilitato e la foresta in cui si trova l'account viene usata per l'autenticazione dell'utente. Questo presupposto è applicato sia alla sincronizzazione delle password che alla federazione. I valori di userPrincipalName e sourceAnchor/immutableID avranno origine da questa foresta. 2. Gli utenti dispongono di una sola cassetta postale. 3. La foresta che ospita la cassetta postale dell'utente garantisce la migliore qualità dei dati per gli attributi visibili nell'Elenco indirizzi globale di Exchange. Se non è presente una cassetta postale per l'utente, è possibile usare qualsiasi foresta per recuperare questi valori di attributi. 4. Se è disponibile una cassetta postale collegata, sarà presente anche un altro account in una foresta diversa usato per l'accesso.

Se l'ambiente non soddisfa questi presupposti, si verificherà quanto segue: - Se sono disponibili più account attivi o più cassette postali, il motore di sincronizzazione sceglierà un account o una cassetta postale e ignorerà gli altri. - Se sono disponibili cassette postali collegate ma non sono presenti altri account, gli account non verranno esportati in Azure AD e l'utente non sarà membro di alcun gruppo. Una cassetta postale collegata in DirSync viene rappresentata come una cassetta postale normale. Si tratta quindi di un comportamento intenzionalmente diverso per supportare in modo migliore gli scenari con più foreste.

### Più foreste, più server di sincronizzazione per una directory di Windows Azure
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Non è consentita la connessione di più server del Servizio di sincronizzazione Azure AD Connect a una singola directory di Azure AD (ad eccezione di un [server di gestione temporanea](#staging-server)).

### Più foreste - Topologie separate
"Gli utenti rappresentati solo una volta in tutte le directory"

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

In questo ambiente tutte le foreste locali sono considerate come entità separate e nessun utente di una foresta può essere incluso in un'altra foresta. Ogni foresta presenta un'organizzazione Exchange dedicata e non viene effettuata alcuna sincronizzazione dell'elenco di indirizzi globale (GALSync) tra le foreste. Questa situazione può verificarsi dopo una fusione/acquisizione o in un'organizzazione in cui ogni business unit opera in modo isolato rispetto alle altre. In Azure AD queste foreste si troveranno nella stessa organizzazione e verranno visualizzate con un elenco di indirizzi globale unificato. In questa immagine ogni oggetto di ogni foresta verrà rappresentato una volta sola nello spazio metaverse e aggregato nella directory di Azure AD di destinazione.

### Più foreste - Corrispondenze con utenti
Un aspetto comune per tutti gli scenari con più foreste in cui si seleziona una delle opzioni disponibili in "Le identità utenti sono presenti in più directory" è costituito dal fatto che i gruppi di distribuzione e i gruppi di sicurezza possono essere presenti in ogni foresta e possono contenere una combinazione di utenti, contatti ed entità di protezione esterne.

Le entità di protezione esterne vengono usate in ADDS per rappresentare i membri di altre foreste in un gruppo di sicurezza. Il motore di sincronizzazione risolverà l'entità di protezione esterna nell'utente reale e rappresenterà il gruppo di sicurezza in Azure AD con tutte le entità di protezione esterne risolve nell'oggetto reale.

### Più foreste - A maglia completa con GALSync facoltativo
"Le identità utenti sono presenti in più directory. Trova la corrispondenza usando: Attributo Mail"

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

In una topologia a maglia completa utenti e risorse possono risiedere in qualsiasi foresta e in genere tra le foreste sono presenti trust bidirezionali.

Se Exchange è installato in più di una foresta, potrebbe essere presente una soluzione GALSync locale che rappresenta un utente in una foresta come un contatto in ogni altra foresta. La soluzione GALSync viene comunemente implementata mediante Forefront Identity Manager 2010 o Microsoft Identity Manager 2016. Azure AD Connect non può essere usato per la soluzione GALSync locale.

In questo scenario, gli oggetti relativi all'identità vengono aggiunti tramite l'attributo Mail. Di conseguenza, un utente con una cassetta postale in una foresta viene aggiunto ai contatti nelle altre foreste.

### Più foreste - Foresta di tipo Account-Risorse
"Le identità utenti sono presenti in più directory. Trova la corrispondenza usando: Attributi ObjectSID e msExchMasterAccountSID"

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

In una topologia di foresta account-risorse, sono presenti una o più foreste di account con account utente attivi.

Questo scenario include una foresta che considera attendibili tutte le foreste di account. Questa foresta presenta in genere uno schema Active Directory esteso con Exchange e Lync. Tutti i servizi Exchange e Lync e altri servizi condivisi si trovano in questa foresta. Gli utenti hanno un account utente disabilitato in questa foresta e la cassetta postale è collegata alla foresta account.

## Considerazioni su Office 365 e sulle topologie
Alcuni carichi di lavoro di Office 365 prevedono determinate restrizioni per le topologie supportate. Se si prevede di usarle, vedere le pagine relative alle topologie supportate di ogni carico di lavoro.

| Carico di lavoro | |
| --------- | --------- |
| Exchange Online |	Se sono presenti più organizzazioni di Exchange locali, ovvero Exchange è stato distribuito in più di una foresta, sarà necessario usare Exchange 2013 SP1 o versione successiva. Informazioni dettagliate sono disponibili nella pagina [Distribuzioni ibride con più insiemi di strutture di Active Directory](https://technet.microsoft.com/it-IT/library/jj873754.aspx) |
| Skype for Business Online | Quando si usano più foreste locali, sarà supportata solo la topologia di tipo foresta account-risorse. Informazioni dettagliate per le topologie supportate sono disponibili nella pagina [Requisiti ambientali di Skype for Business Server 2015](https://technet.microsoft.com/it-IT/library/dn933910.aspx) |

## Server di gestione temporanea
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect supporta l'installazione di un secondo server in "Modalità di gestione temporanea". Un server in questa modalità leggerà solo i dati da tutte le directory connesse e avrà quindi una copia aggiornata dei dati sull'identità. In caso di emergenza con errori del server primario, è possibile eseguire con facilità il failover al secondo server mediante la procedura guidata di Azure AD Connect. È preferibile che il secondo server si trovi in un data center diverso, in modo da non condividere alcuna infrastruttura con il server primario. Eventuali modifiche di configurazione apportate al server primario devono essere copiate manualmente nel secondo server.

È anche possibile usare un server di gestione temporanea, se si vuole testare una nuova configurazione personalizzata e il relativo effetto sui dati. È possibile visualizzare l'anteprima delle modifiche e perfezionare la configurazione. Dopo avere ottenuto la configurazione ottimale, sarà possibile rendere attivo il server di gestione temporanea e impostare il server attivo precedente sulla modalità di gestione temporanea.

È possibile usare questo metodo anche se si vuole sostituire il server di sincronizzazione e si vuole preparare il nuovo server prima di arrestare il server attualmente attivo.

Se si vuole disporre di più backup in diversi data center, è possibile creare più server di gestione temporanea.

## Più directory di Azure AD
È consigliabile che in Azure AD sia presente una singola directory per un'organizzazione. Prima di valutare l'uso di più directory di Azure AD, esaminare gli scenari comuni illustrati in questi argomenti, che consentono di usare una singola directory.

| Argomento | |
| --------- | --------- |
| Delega mediante le unità amministrative | [Gestione delle unità amministrative in Azure AD](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Esiste una relazione 1:1 tra un server di sincronizzazione di Azure AD Connect e una directory di Azure AD. Per ogni directory di Azure AD sarà necessaria un'installazione del server di sincronizzazione di Azure AD Connect. Come previsto da progettazione, le istanze delle directory di Azure AD sono isolate e gli utenti in un'istanza non potranno visualizzare gli utenti in un'altra directory. Se questo comportamento è previsto, si tratta di una configurazione supportata. In caso contrario, è consigliabile usare i modelli di Azure AD a directory singola illustrati in precedenza.

### Ogni oggetto una sola volta in una directory di Azure AD
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

In questa topologia un server di sincronizzazione di AAD Connect è connesso a ogni directory di Azure AD. I server di sincronizzazione di Azure AD Connect devono essere configurati per l'applicazione di filtri, in modo che ogni server includa un set a esclusione reciproca di oggetti su cui operare, ad esempio definendo l'ambito di ogni server in modo che corrisponda a un dominio specifico o UO. Un dominio DNS può essere registrato solo in una singola directory di Azure AD, in modo che anche i nomi dell'entità utente (UPN) degli utenti nell'istanza locale di AD possano usare spazi dei nomi separati. Ad esempio, nella figura precedente tre suffissi UPN separati vengono registrati nell'istanza locale di AD: contoso.com, fabrikam.com e wingtiptoys.com. Gli utenti di ogni dominio di AD locale usano uno spazio dei nomi diverso.

In questa topologia non viene effettuata alcuna sincronizzazione dell'elenco di indirizzi globale (GALSync) tra le istanze delle directory di Azure AD. La Rubrica in Exchange Online e Skype for Business mostrerà quindi solo gli utenti che si trovano nella stessa directory.

Questa topologia consente solo a una delle directory di Azure AD di abilitare la distribuzione ibrida di Exchange con l'istanza locale di Active Directory.

Il requisito relativo a un set di oggetti a esclusione reciproca si applica anche al writeback. Alcune funzionalità di writeback non sono quindi supportate con questa topologia, perché presuppongono una singola configurazione locale. Le funzionalità includono, ad esempio, il writeback gruppi con configurazione predefinita e il writeback dispositivi.

### Ogni oggetto più volte in una directory di Azure AD
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Non è supportata la sincronizzazione dello stesso utente in più directory di Azure AD. Non è inoltre supportata la modifica della configurazione per fare in modo che gli utenti di un'istanza di Azure AD vengano visualizzati come contatti in un'altra directory di Azure AD. Non è consentito modificare il servizio di sincronizzazione Azure AD Connect per la connessione a più directory di Azure AD.

### GALsync tramite l’utilizzo di writeback
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Come previsto da progettazione, le directory di Azure AD sono isolate. Non è supportata la modifica della configurazione del servizio di sincronizzazione Azure AD Connect in modo da leggere dati da un'altra directory di Azure AD nel tentativo di creare un elenco di indirizzi globale comune e unificato tra le directory. Non è inoltre supportata l'esportazione di utenti come contatti in un'altra istanza locale di AD mediante il servizio di sincronizzazione Azure AD Connect.

### GALsync con server di sincronizzazione locale
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

È supportato l'uso di FIM2010/MIM2016 locale per eseguire la sincronizzazione dell'elenco di indirizzi globale per gli utenti tra due organizzazioni di Exchange. Gli utenti di un'organizzazione verranno visualizzati come utenti/contatti esterni nell'altra organizzazione. Sarà quindi possibile sincronizzare le due istanze locali diverse di AD con le rispettive directory di Azure AD.

## Passaggi successivi
Per informazioni su come installare Azure AD Connect per questi scenari, vedere [Installazione personalizzata di Azure Ad Connect](active-directory-aadconnect-get-started-custom.md). Per altre informazioni sulla configurazione del servizio di sincronizzazione Azure AD Connect, vedere [Servizio di sincronizzazione Azure AD Connect](active-directory-aadconnectsync-whatis.md).

<!---HONumber=Sept15_HO2-->