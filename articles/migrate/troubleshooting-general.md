---
title: Risolvere problemi relativi ad Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dei problemi noti del servizio Azure Migrate, nonché suggerimenti per la risoluzione di errori comuni.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 12f8f64c051d33ac2518edbe8b937521318a9e71
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284493"
---
# <a name="troubleshoot-azure-migrate"></a>Risolvere i problemi relativi ad Azure Migrate

[Azure migrate](migrate-services-overview.md) offre un hub di strumenti per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti. Questo articolo consente di risolvere i problemi relativi alla Azure Migrate, alla valutazione Azure Migrate server e alla migrazione di Azure Migrate server.


## <a name="find-a-project"></a>Trovare un progetto

Esistono [due versioni](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate.


Se il progetto Azure Migrate è stato creato nella versione corrente di Azure Migrate, eseguire le operazioni seguenti:

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure migrate**.
2. Nel dashboard Azure Migrate > **Server**selezionare **Cambia** nell'angolo superiore destro.

    ![Passa a un progetto di Azure Migrate esistente](./media/troubleshooting-general/switch-project.png)

3. Selezionare la sottoscrizione appropriata e il progetto Azure Migrate.


Se il progetto è stato creato nella versione precedente di Azure Migrate, procedere come segue:

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure migrate**.
2. Nel dashboard Azure Migrate, se è stato creato un progetto nella versione precedente, viene visualizzato un banner che fa riferimento a progetti precedenti. Selezionare il banner.

    ![Accedi a progetti esistenti](./media/troubleshooting-general/access-existing-projects.png)

3. Esaminare l'elenco del progetto precedente.


## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

Creare un nuovo progetto di Azure Migrate come segue:

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure migrate**.
2. Nel dashboard Azure Migrate > **Server**selezionare **Cambia** nell'angolo superiore destro.

   ![Modificare il progetto Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui**.

   ![Creazione di un secondo progetto di Azure Migrate](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Esaminare le aree geografiche supportate

Esaminare le aree geografiche supportate per [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Elimina progetti/aree di lavoro

Quando si eliminano Azure Migrate progetti e Log Analytics aree di lavoro, tenere presente quanto segue:

- Quando si elimina un progetto di Azure Migrate, vengono eliminati il progetto *e* i metadati relativi alle macchine virtuali individuate.
- Se è stata collegata un'area di lavoro Log Analytics allo strumento Server Assessment, l'area di lavoro non viene eliminata automaticamente.
- È possibile utilizzare la stessa area di lavoro Log Analytics per più scenari.
- Se si desidera eliminare l'area di lavoro Log Analytics, è necessario eseguire questa operazione manualmente.


### <a name="delete-a-project"></a>Eliminare un progetto

Per eliminare un progetto nella versione corrente di Azure Migrate:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Nella pagina gruppo di risorse selezionare **Mostra tipi nascosti**.
3. Selezionare il progetto di migrazione che si desidera eliminare. Il tipo di risorsa è Microsoft. migrate/migrateprojects e lo elimina.

Per eliminare un progetto nella versione precedente di Azure Migrate:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Selezionare il progetto di migrazione che si desidera eliminare. Il tipo di risorsa è il progetto di migrazione e lo elimina.


### <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Passare all'area di lavoro Log Analytics collegata al progetto.
* Se il progetto Azure Migrate non è stato eliminato, è possibile trovare il collegamento all'area di lavoro in **Essentials** > **Server Assessment**.
       ![area di lavoro](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Errore "le richieste devono contenere intestazioni di identità utente"

Quando si crea un progetto, questo errore potrebbe indicare che non si ha accesso al tenant di Azure Active Directory (Azure AD) dell'organizzazione.

- Quando si viene aggiunti a un tenant di Azure AD per la prima volta, si riceve un messaggio di posta elettronica di invito per l'aggiunta al tenant.
- Accettare l'invito per poter essere aggiunto correttamente al tenant.
    - Se il messaggio di posta elettronica non è visibile, contattare un utente con accesso al tenant e chiedere di [inviare di nuovo l'invito](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) .
    - Dopo la ricezione del messaggio di posta elettronica di invito, aprirlo e selezionare il collegamento per accettare l'invito. Quindi, disconnettersi dalla portale di Azure ed eseguire di nuovo l'accesso. (l'aggiornamento del browser non funziona). È quindi possibile iniziare a creare il progetto di migrazione.


## <a name="error-invalid-ovf-manifest-entry"></a>Errore "voce del manifesto OVF non valida"

Se viene visualizzato l'errore "il file manifesto specificato non è valido: voce del manifesto OVF non valida", eseguire le operazioni seguenti:

1. Verificare che il file OVA del dispositivo Azure Migrate venga scaricato correttamente controllando il relativo valore hash. [Altre informazioni](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se il valore hash non corrisponde, scaricare di nuovo il file OVA e riprovare la distribuzione.
2. Se la distribuzione ha ancora esito negativo e si usa il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client Web di vSphere. Se la distribuzione non riesce ancora, provare a usare un altro Web browser.
3. Se si usa il client Web vSphere e si prova a distribuirlo in server vCenter 6,5 o 6,7, provare a distribuire gli OVA direttamente nell'host ESXi:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) con il client Web (https://<*indirizzo IP host*>/UI).
   - In **Home** > **Inventory**selezionare **file** > **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

## <a name="appliance-cant-connect-to-the-internet"></a>Appliance non è in grado di connettersi a Internet

Questo problema può verificarsi se il computer dell'appliance si trova dietro un proxy.

- Assicurarsi di specificare le credenziali di autorizzazione se sono necessarie per il proxy.
- Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, aggiungere questi URL a un elenco Consenti:

    - [URL per la valutazione VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URL per la valutazione di Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URL per la migrazione senza agenti VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URL per la migrazione basata su agenti VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URL per la migrazione di Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Se si usa un proxy di intercettazione per connettersi a Internet, importare il certificato proxy nella macchina virtuale dell'appliance seguendo [questa procedura](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Errore: sincronizzazione data/ora

Un errore relativo alla sincronizzazione di data e ora (802) indica che l'orologio del server potrebbe non essere sincronizzato con l'ora corrente da più di cinque minuti. Modificare l'ora di clock della macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire **w32tm/TZ**.
3. Per sincronizzare l'ora, eseguire **w32tm sull'/Resync**.


## <a name="error-unabletoconnecttoserver"></a>Errore: UnableToConnectToServer

Se viene ricevuto questo errore di connessione, potrebbe non essere possibile connettersi a server vCenter *nomeserver*. com: 9443. I dettagli dell'errore indicano che non esiste alcun endpoint in ascolto in https://*ServerName*. com: 9443/SDK che può accettare il messaggio.

- Controllare se si sta eseguendo la versione più recente dell'appliance. In caso contrario, aggiornare l'appliance alla [versione più recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se il problema si verifica ancora nella versione più recente, l'appliance potrebbe non essere in grado di risolvere il nome del server vCenter specificato oppure la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.

    1. Eseguire il ping *ServerName*. com dall'appliance.
    2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter usando l'indirizzo IP.
    3. Identificare il numero di porta corretto per la connessione server vCenter.
    4. Verificare che server vCenter sia attivo e in esecuzione.


## <a name="error-appliance-might-not-be-registered"></a>Errore: l'appliance potrebbe non essere registrata

- Errore 60052, "l'appliance potrebbe non essere stata registrata correttamente nel progetto Azure Migrate" si verifica se l'account Azure usato per registrare l'appliance non dispone di autorizzazioni sufficienti.
    - Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
    - [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.
- Errore 60039: "è possibile che l'appliance non sia stata registrata correttamente nel progetto Azure Migrate" se la registrazione non riesce perché il progetto Azure Migrate usato per registrare l'appliance non è stato trovato.
    - Nel portale di Azure e verificare se il progetto esiste nel gruppo di risorse.
    - Se il progetto non esiste, creare un nuovo progetto di Azure Migrate nel gruppo di risorse e registrare di nuovo l'appliance. [Informazioni su come](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) creare un nuovo progetto.

## <a name="error-key-vault-management-operation-failed"></a>Errore: operazione di gestione Key Vault non riuscita

Se viene visualizzato l'errore 60030 o 60031, "un'operazione di gestione Azure Key Vault non riuscita", eseguire le operazioni seguenti:
- Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
- Verificare che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione.
- Se il problema persiste, contattare il supporto tecnico Microsoft.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

## <a name="fix-discovery-couldnt-be-initiated"></a>Correzione: non è stato possibile avviare l'individuazione

Errore 60028: "Impossibile avviare l'individuazione a causa di un errore. L'operazione non è riuscita per l'elenco specificato di host o cluster "indica che non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema durante l'accesso o il recupero delle informazioni sulla macchina virtuale. Il resto degli host è stato aggiunto correttamente.

- Aggiungere di nuovo gli host elencati nell'errore utilizzando l'opzione **Aggiungi host** .
- Se si verifica un errore di convalida, rivedere le indicazioni per la correzione per correggere gli errori, quindi provare a eseguire di nuovo l'opzione **Salva e avvia individuazione** .

## <a name="fix-azure-ad-operation-failed-60025"></a>Correzione: operazione Azure AD non riuscita (60025)

Errore 60025: "un'operazione Azure AD non è riuscita. L'errore si è verificato durante la creazione o l'aggiornamento dell'applicazione Azure AD "si verifica quando l'account utente di Azure usato per avviare l'individuazione è diverso dall'account usato per registrare l'appliance. Eseguire una delle operazioni seguenti:

- Assicurarsi che l'account utente che avvia l'individuazione sia uguale a quello usato per registrare l'appliance.
- Fornire Azure Active Directory le autorizzazioni di accesso alle applicazioni per l'account utente per il quale l'operazione di individuazione ha esito negativo.
- Eliminare il gruppo di risorse creato in precedenza per il progetto Azure Migrate. Creare un altro gruppo di risorse per riavviarlo.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sulle autorizzazioni per l'applicazione Azure Active Directory.


## <a name="discovered-vms-not-in-portal"></a>Macchine virtuali individuate non nel portale

Se si avvia l'individuazione in modo che la **valutazione del server** e la **migrazione del server** mostrino l' **individuazione in corso**, ma non ancora le VM nel portale, tenere presente quanto segue:

- Dopo l'avvio dell'individuazione dal dispositivo, sono necessari circa 15 minuti per l'individuazione di una macchina virtuale VMware e circa due minuti per ogni host aggiunto per l'individuazione di macchine virtuali Hyper-V.
- Se si continua a visualizzare l' **individuazione in corso** anche dopo questi periodi di attesa, selezionare **Aggiorna** nella scheda **server** . Verrà visualizzato il numero dei server individuati in **valutazione server** e **migrazione server**.


## <a name="deleted-vms-in-the-portal"></a>Macchine virtuali eliminate nel portale

Se è stata distribuita un'appliance che individua continuamente l'ambiente locale, ma le VM eliminate sono ancora visualizzate nel portale, tenere presente quanto segue:  

- Sono necessari fino a 30 minuti affinché i dati di individuazione raccolti dal dispositivo vengano riflessi nel portale.
- Se dopo 30 minuti non vengono visualizzate informazioni aggiornate, aggiornare i dati attenendosi alla procedura seguente:

    1. In **server** > **Azure migrate server Assessment**Selezionare **Panoramica**.
    2. In **Gestisci**selezionare **integrità agente**
    3. Selezionare **Aggiorna agente**.
    1. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="vm-information-isnt-in-the-portal"></a>Le informazioni sulla macchina virtuale non sono presenti nel portale

- Sono necessari fino a 30 minuti affinché i dati di individuazione raccolti dal dispositivo vengano riflessi nel portale.
- Se dopo 30 minuti non vengono visualizzate informazioni aggiornate, aggiornare i dati attenendosi alla procedura seguente:

    1. In **server** > **Azure migrate server Assessment**Selezionare **Panoramica**.
    2. In **Gestisci**selezionare **integrità agente**
    3. Selezionare **Aggiorna agente**.
    1. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Correzione: non è possibile connettersi all'host o al cluster

Errore 50004: "Impossibile connettersi a un host o a un cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 "potrebbe verificarsi se il servizio DNS di Azure per l'appliance non riesce a risolvere il cluster o il nome host specificato.

- Se viene visualizzato questo errore nel cluster, FQDN del cluster.
- Questo errore può essere visualizzato anche per gli host in un cluster. Ciò indica che l'appliance è in grado di connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN. Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
    1. Aprire Blocco note come amministratore.
    2. Aprire il file C:\Windows\System32\Drivers\etc\hosts.
    3. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
    4. Salvare e chiudere il file degli host.
    5. Controllare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, nel portale di Azure verranno visualizzate le informazioni più recenti per questi host.

## <a name="application-discovery-issues"></a>Problemi di individuazione delle applicazioni

L'individuazione delle applicazioni è attualmente supportata solo per le macchine virtuali VMware. Il supporto per le VM Hyper-V e i server fisici verrà abilitato in futuro. Per l'individuazione delle applicazioni è necessario fornire le credenziali della VM nell'appliance. Altre informazioni sui privilegi di accesso necessari per server vCenter e per le macchine virtuali VMware. L'individuazione può avere esito negativo a causa di uno dei problemi seguenti. per risolvere il problema, vedere l'azione consigliata come indicato di seguito:

**Codice errore** | **Messaggio** | **Causa possibile** | **Azione consigliata**
--- | --- | --- | ---
10000 | Impossibile individuare le applicazioni installate nel server. | Questo problema può verificarsi se il sistema operativo in esecuzione nel server non è né Windows né Linux. | L'individuazione delle applicazioni installate è supportata solo per i server Windows e Linux.
10001 | Impossibile recuperare le applicazioni installate nel server. | Questo problema si verifica a causa di un errore interno perché nel dispositivo sono presenti alcuni file mancanti. | Contattare supporto tecnico Microsoft.
10002 | Impossibile recuperare le applicazioni installate nel server. | Questo problema può verificarsi se l'agente di individuazione nell'appliance Azure Migrate non funziona correttamente. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
10003 | Impossibile recuperare le applicazioni installate nel server. | Questo problema può verificarsi se l'agente di individuazione non funziona correttamente. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
10004 | Non è possibile individuare le applicazioni installate per < computer con > Windows/Linux. |  Le credenziali per l'accesso < macchine > Windows/Linux non sono state fornite nell'appliance Azure Migrate | Aggiungere una credenziale nell'appliance Azure Migrate che abbia accesso ai computer > < Windows/Linux.
10005 | Non è possibile accedere al server locale. | Questo problema può verificarsi se le credenziali specificate per il computer per accedere al server non sono corrette. | Aggiornare le credenziali fornite nell'appliance e verificare che il server sia accessibile tramite le credenziali.
10006 | Non è possibile accedere al server locale. | Questo problema può verificarsi se il sistema operativo in esecuzione nel server non è né Windows né Linux. | L'individuazione delle applicazioni installate è supportata solo per i server Windows e Linux.
9000 | Non è possibile individuare le applicazioni installate nella macchina virtuale. | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Installare o reinstallare gli strumenti VMware nella macchina virtuale e verificare se è in esecuzione.
9001 | Non è possibile individuare le applicazioni installate nella macchina virtuale. | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Installare o reinstallare gli strumenti VMware nella macchina virtuale e verificare se è in esecuzione.
9002 | Non è possibile individuare le applicazioni installate nella macchina virtuale. | È possibile che gli strumenti VMware non siano in esecuzione. | Installare o reinstallare gli strumenti VMware nella macchina virtuale e verificare se è in esecuzione.
9003 | Impossibile individuare le applicazioni installate nel server. | Questo problema può verificarsi se il sistema operativo in esecuzione nel server non è né Windows né Linux. | L'individuazione delle applicazioni installate è supportata solo per i server Windows e Linux.
9004 | Impossibile individuare le applicazioni installate nel server. | Questo problema può verificarsi se la macchina virtuale è spenta. | Per individuare le applicazioni installate nel server, verificare che la macchina virtuale sia accesa.
9005 | Non è possibile individuare le applicazioni installate nella macchina virtuale. | Questo problema può verificarsi se il sistema operativo in esecuzione nella macchina virtuale non è né Windows né Linux. | L'individuazione delle applicazioni installate è supportata solo per i server Windows e Linux.
9006 | Impossibile recuperare le applicazioni installate nel server. | Questo problema può verificarsi se l'agente di individuazione non funziona correttamente. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
9007 | Impossibile recuperare le applicazioni installate nel server. | Questo problema può verificarsi se l'agente di individuazione non funziona correttamente. | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
9008 | Impossibile recuperare le applicazioni installate nel server. | Il problema può verificarsi a causa di un errore interno.  | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
9009 | Impossibile recuperare le applicazioni installate nel server. | Il problema può verificarsi se le impostazioni di controllo dell'account utente di Windows nel server sono restrittive e impediscono l'individuazione delle applicazioni installate. | Cercare le impostazioni di controllo dell'account utente nel server e configurare l'impostazione UAC sul server in modo che si trovi in uno dei due livelli inferiori.
9010 | Impossibile recuperare le applicazioni installate nel server. | Il problema può verificarsi a causa di un errore interno.  | Il problema dovrebbe essere risolto automaticamente in 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft.
8084 | Non è possibile individuare le applicazioni a causa di un errore di VMware: <Exception from VMware> | Il Azure Migrate Appliance usa le API VMware per individuare le applicazioni. Questo problema può verificarsi a causa di un'eccezione generata da server vCenter durante il tentativo di individuazione delle applicazioni. Il messaggio di errore di VMware viene visualizzato nel messaggio di errore visualizzato nel portale. | Esaminare la [documentazione di VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), cercare il messaggio di errore e seguire i passaggi per la risoluzione dei problemi dell'articolo VMware per risolvere il problema. Se non si riesce ancora a risolvere il problema, contattare supporto tecnico Microsoft.


## <a name="fix-assessment-readiness"></a>Correzione della conformità della valutazione

Correggere i problemi di conformità della valutazione come indicato di seguito:

**Problema** | **Correzione**
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le VM con un tipo di avvio EFI. È consigliabile convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>È possibile usare Azure Migrate migrazione del server per gestire la migrazione di tali macchine virtuali. Il tipo di avvio della macchina virtuale verrà convertito in BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto ed è necessario un contratto di supporto personalizzato (CSA) per il [supporto in Azure](https://aka.ms/WSosstatement). Prendere in considerazione l'aggiornamento prima di eseguire la migrazione ad Azure.
Sistema operativo Windows non supportato | Azure supporta solo le [versioni del sistema operativo Windows selezionate](https://aka.ms/WSosstatement). Provare ad aggiornare il computer prima di eseguire la migrazione ad Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo le [versioni del sistema operativo Linux selezionate](../virtual-machines/linux/endorsed-distros.md). Provare ad aggiornare il computer prima di eseguire la migrazione ad Azure.
Sistema operativo Linux non approvato | Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto per il sistema operativo. Prendere in considerazione l'aggiornamento a una [versione di Linux approvata](../virtual-machines/linux/endorsed-distros.md) prima di eseguire la migrazione ad Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "altro" in server vCenter. Questo comportamento blocca Azure Migrate dalla verifica della conformità di Azure della macchina virtuale. Prima di eseguire la migrazione del computer, assicurarsi che il sistema operativo sia [supportato](https://aka.ms/azureoslist) da Azure.
Versione bit non supportata | Le macchine virtuali con sistemi operativi a 32 bit possono essere avviate in Azure, ma è consigliabile eseguire l'aggiornamento a 64 bit prima di eseguire la migrazione ad Azure.
Richiede una sottoscrizione di Microsoft Visual Studio | Il computer esegue un sistema operativo client Windows, che è supportato solo tramite una sottoscrizione di Visual Studio.
Macchina virtuale non trovata per le prestazioni di archiviazione richieste | Le prestazioni di archiviazione (operazioni di input/output al secondo [IOPS] e velocità effettiva) richieste per il computer superano il supporto delle macchine virtuali di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Macchina virtuale non trovata per le prestazioni di rete richieste | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
La macchina virtuale non è stata trovata nel percorso specificato | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi non appropriati | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure. Un<br/><br/> Azure Migrate: la valutazione del server non supporta attualmente i dischi Ultra SSD e valuta i dischi in base ai limiti del disco per i dischi gestiti Premium (32 TB).<br/><br/> Per ogni disco collegato alla macchina virtuale, verificare che le dimensioni del disco siano < 64 TB (supportato da dischi Ultra SSD).<br/><br/> In caso contrario, ridurre le dimensioni del disco prima di eseguire la migrazione ad Azure o usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati. Assicurarsi che le prestazioni (IOPS e velocità effettiva) richieste da ogni disco siano supportate dai [dischi delle macchine virtuali gestite](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)da Azure.
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure Migrate: la valutazione del server non supporta attualmente i dischi Ultra SSD e valuta i dischi in base ai limiti del disco Premium (32 TB).<br/><br/> Tuttavia, Azure supporta dischi con dimensioni fino a 64 TB (supportate da dischi Ultra SSD). Compattare i dischi fino a 64 TB prima della migrazione oppure usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non è riuscito a trovare un tipo di macchina virtuale appropriato. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per uno o più dischi a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per una o più schede di rete a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.

## <a name="linux-vms-are-conditionally-ready"></a>Le VM Linux sono "predisposte in modo condizionale"

Server Assessment contrassegna le VM Linux come "predisposte in modo condizionale" a causa di un gap noto nella valutazione del server.

- Il gap impedisce di rilevare la versione secondaria del sistema operativo Linux installato nelle macchine virtuali locali.
- Ad esempio, per RHEL 6,10, attualmente server Assessment rileva solo RHEL 6 come versione del sistema operativo.
-  Poiché Azure approva solo versioni specifiche di Linux, le VM Linux sono attualmente contrassegnate come predisposte in modo condizionale nella valutazione del server.
- È possibile determinare se il sistema operativo Linux in esecuzione nella macchina virtuale locale è approvato in Azure esaminando il supporto per [Linux di Azure](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Dopo aver verificato la distribuzione approvata, è possibile ignorare questo avviso.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Gli SKU di Azure superano il dimensionamento locale

Azure Migrate server Assessment potrebbe consigliare gli SKU di VM di Azure con più core e memoria rispetto all'allocazione locale corrente in base al tipo di valutazione:


- La raccomandazione SKU della macchina virtuale dipende dalle proprietà di valutazione.
- Questo problema è influenzato dal tipo di valutazione eseguito in server assessment: *basato sulle prestazioni*o in *locale*.
- Per le valutazioni basate sulle prestazioni, valutazione server considera i dati di utilizzo delle macchine virtuali locali (CPU, memoria, disco e utilizzo della rete) per determinare lo SKU di VM di destinazione appropriato per le macchine virtuali locali. Viene inoltre aggiunto un fattore di comfort quando si determina un utilizzo efficace.
- Per il dimensionamento locale, i dati sulle prestazioni non vengono considerati e lo SKU di destinazione è consigliato in base all'allocazione locale.

Per illustrare il modo in cui questo può influire sulle raccomandazioni, si prenda un esempio:

Abbiamo una macchina virtuale locale con quattro core e 8 GB di memoria, con un utilizzo della CPU del 50% e un utilizzo della memoria del 50% e un fattore di comfort specificato di 1,3.

-  Se la valutazione è **come in locale**, è consigliabile usare uno SKU di VM di Azure con 4 core e 8 GB di memoria.
- Se la valutazione è basata sulle prestazioni, in base all'utilizzo effettivo di CPU e memoria (50% di 4 core * 1,3 = 2,6 core e 50% di 8 GB di memoria * 1,3 = 5,3-GB di memoria), lo SKU di VM più economico di quattro core (numero di core supportato più vicino) e otto GB di memoria (supportata più vicina). è consigliabile usare le dimensioni della memoria.
- [Altre](concepts-assessment-calculation.md#sizing) informazioni sul dimensionamento della valutazione.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKU dischi di Azure di dimensioni maggiori rispetto a locali

Azure Migrate server Assessment potrebbe consigliare un disco di dimensioni maggiori in base al tipo di valutazione.
- Il dimensionamento del disco nella valutazione del server dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è impostato su **automatico**, i valori di IOPS e velocità effettiva del disco vengono considerati quando si identifica il tipo di disco di destinazione (HDD standard, SDD standard o Premium). Viene quindi consigliato uno SKU del disco dal tipo di disco e la raccomandazione considera i requisiti di dimensioni del disco locale.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è **Premium**, è consigliabile usare uno SKU di dischi Premium in Azure in base ai requisiti di IOPS, velocità effettiva e dimensioni del disco locale. La stessa logica viene usata per eseguire il ridimensionamento del disco quando i criteri di ridimensionamento sono **locali** e il tipo di archiviazione è **HDD standard**, **SDD standard**o **Premium**.

Ad esempio, se si dispone di un disco locale con 32 GB di memoria, ma le operazioni di i/o di lettura e scrittura aggregate per il disco sono 800 IOPS, server Assessment consiglia un disco Premium (a causa dei requisiti di IOPS più elevati), quindi consiglia uno SKU del disco in grado di supportare r ecessario IOPS e dimensioni. In questo esempio la corrispondenza più vicina sarebbe P15 (256 GB, 1.100 operazioni di I/O al secondo). Anche se le dimensioni richieste dal disco locale sono 32 GB, server Assessment consiglia un disco più grande a causa del requisito di IOPS elevato del disco locale.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Correzione: manca la percentuale di memoria o Core utilizzata

Server Assessment segnala "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" quando l'appliance Azure Migrate non è in grado di raccogliere i dati sulle prestazioni per le VM locali pertinenti.

- Questo problema può verificarsi se le macchine virtuali sono spente durante la durata della valutazione. L'appliance non può raccogliere i dati sulle prestazioni per una macchina virtuale quando è spenta.
- Se mancano solo i contatori di memoria e si sta provando a valutare le VM Hyper-V, controllare se la memoria dinamica è abilitata in queste VM. Si verifica un problema noto solo per le macchine virtuali Hyper-V, in cui un'appliance Azure Migrate non è in grado di raccogliere dati di utilizzo della memoria per le macchine virtuali in cui non è abilitata la memoria dinamica.
- Se non è presente alcun contatore delle prestazioni, Azure Migrate server Assessment esegue il fallback alla memoria e ai core allocati e suggerisce le dimensioni della VM corrispondenti.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Il costo della licenza del sistema operativo della macchina virtuale è incluso nella valutazione dei costi?

Azure Migrate server Assessment considera attualmente il costo della licenza del sistema operativo solo per i computer Windows. I costi di licenza per i computer Linux non sono attualmente considerati.

## <a name="performance-history-and-percentile-use"></a>Cronologia prestazioni e utilizzo percentile

Queste proprietà si applicano solo al dimensionamento in base alle prestazioni in Azure Migrate server assessment.

Server Assessment raccoglie continuamente i dati delle prestazioni dei computer locali e li usa per consigliare lo SKU della macchina virtuale e del disco in Azure. Questi dati sulle prestazioni vengono raccolti da valutazione server come indicato di seguito:
- Il Azure Migrate appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi per le macchine virtuali VMware e ogni 30 secondi per le macchine virtuali Hyper-V.
- Il dispositivo esegue il rollup degli esempi di 20 o 30 secondi per creare un singolo punto dati per ogni 10 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi e 30 secondi, quindi lo invia ad Azure.
- Quando si crea una valutazione in Server Assessment, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, viene identificato il valore relativo all'utilizzo rappresentativo. Se, ad esempio, la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è pari a 95, Azure Migrate ordina tutti i punti di campionamento di 10 minuti per l'ultima settimana in ordine crescente e quindi seleziona il 95 ° percentile come valore rappresentativo.
- Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se si sceglie il 99 ° percentile.
- Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, è necessario selezionare il 99 ° percentile per l'utilizzo percentile.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Non è possibile trovare la visualizzazione delle dipendenze per Azure per enti pubblici

Azure Migrate dipende da Mapping dei servizi per la funzionalità di visualizzazione delle dipendenze. Poiché Mapping dei servizi non è attualmente disponibile in Azure per enti pubblici, questa funzionalità non è disponibile in Azure per enti pubblici.

## <a name="dependencies-dont-show-after-installing-agents"></a>Le dipendenze non vengono visualizzate dopo l'installazione degli agenti

Dopo aver installato gli agenti di visualizzazione delle dipendenze nelle VM locali, Azure Migrate in genere impiega 15-30 minuti per visualizzare le dipendenze nel portale. Se sono state attese più di 30 minuti, verificare che il Microsoft Monitoring Agent (MMA) sia in grado di connettersi all'area di lavoro Log Analytics.

Per VM di Windows:
1. Nel pannello di controllo avviare MMA.
2. Nel **Microsoft Monitoring Agent proprietà** > **Azure log Analytics (OMS)** , verificare che lo **stato** dell'area di lavoro sia verde.
3. Se lo stato non è verde, provare a rimuovere l'area di lavoro e aggiungerlo di nuovo a MMA.

      ![Finestra di dialogo Proprietà MMA](./media/troubleshooting-general/mma-status.png)

Per le macchine virtuali Linux, assicurarsi che i comandi di installazione per MMA e Dependency Agent siano riusciti.

## <a name="supported-mma-os"></a>Sistema operativo MMA supportato

 Esaminare i sistemi operativi [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) supportati.

## <a name="supported-dependency-agent-os"></a>Sistema operativo di Dependency Agent supportato

Esaminare i sistemi operativi [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) supportati.

## <a name="dependencies-for-more-than-an-hour"></a>Dipendenze per più di un'ora

Sebbene Azure Migrate consenta di tornare a una data specifica nell'ultimo mese, la durata massima per la quale è possibile visualizzare le dipendenze è di un'ora.

Ad esempio, è possibile usare la funzionalità durata tempo nella mappa delle dipendenze per visualizzare le dipendenze di ieri, ma è possibile visualizzarle solo per un periodo di un'ora.

Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati di dipendenza](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Non è possibile visualizzare le dipendenze per i gruppi con più di 10 macchine virtuali

In Azure Migrate server assessment è possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con un massimo di 10 VM. Per i gruppi più grandi, è consigliabile suddividere le macchine virtuali in gruppi più piccoli per visualizzare le dipendenze.

## <a name="machines-show-install-agent-not-view-dependencies"></a>I computer mostrano "Installa agente" non "Visualizza dipendenze"

Dopo aver eseguito la migrazione dei computer con la visualizzazione delle dipendenze abilitata in Azure, i computer potrebbero visualizzare l'azione "Installa agente" invece di "Visualizza dipendenze" a causa del comportamento seguente:


- Dopo la migrazione ad Azure, i computer locali sono spenti e le VM equivalenti vengono riattivate in Azure. Queste macchine acquisiscono un indirizzo MAC diverso
- I computer possono anche avere un indirizzo IP diverso, a seconda che l'indirizzo IP locale sia stato mantenuto o meno.
- Se gli indirizzi MAC e IP sono diversi da quelli locali, Azure Migrate non associa i computer locali con i dati di dipendenza Mapping dei servizi. In questo caso, verrà visualizzata l'opzione per l'installazione dell'agente anziché per visualizzare le dipendenze.
- Dopo una migrazione di test in Azure, i computer locali rimangono accesi come previsto. Computer equivalenti in Azure acquisiscono un indirizzo MAC diverso e potrebbero acquisire indirizzi IP diversi. A meno che non venga bloccato il traffico dei log di monitoraggio di Azure in uscita da questi computer, Azure Migrate non associa i computer locali con i dati delle dipendenze Mapping dei servizi e quindi visualizzerà l'opzione per installare gli agenti, anziché visualizzare le dipendenze.


## <a name="collect-network-traffic-logs-in-portal"></a>Raccogliere i log del traffico di rete nel portale

Raccogliere i log come segue:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione **Cancella voci per la navigazione** .
3. Selezionare la scheda **rete** e avviare l'acquisizione del traffico di rete:
   - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'acquisizione del traffico. Se il cerchio rosso non viene visualizzato, selezionare il cerchio nero per iniziare.
   - In Microsoft Edge e Internet Explorer la registrazione dovrebbe avviarsi automaticamente. In caso contrario, selezionare il pulsante verde Riproduci.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
   - In Chrome fare clic con il pulsante destro del mouse e scegliere **Salva come har con contenuto**. Questa azione comprime ed Esporta i log come file con estensione har.
   - In Microsoft Edge o Internet Explorer selezionare l'opzione **Esporta il traffico acquisito** . Questa azione comprime ed esporta il log.
6. Selezionare la scheda **console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
   - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome**, per esportare e comprimere il log.
   - In Microsoft Edge o Internet Explorer fare clic con il pulsante destro del mouse sugli errori e selezionare **copia tutto**.
7. Chiudere Strumenti di sviluppo.
