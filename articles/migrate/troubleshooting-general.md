---
title: Risolvere problemi relativi ad Azure Migrate | Microsoft Docs
description: Viene fornita una panoramica dei problemi noti del servizio Azure Migrate, nonché suggerimenti per la risoluzione di errori comuni.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142313"
---
# <a name="troubleshoot-azure-migrate"></a>Risolvere i problemi relativi ad Azure Migrate

[Azure migrate](migrate-services-overview.md) offre un hub di strumenti per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti. Questo articolo consente di risolvere gli errori relativi a Azure Migrate, Azure Migrate valutazione del server e Azure Migrate migrazione del server.

## <a name="azure-migrate-project-issues"></a>Problemi di Azure Migrate progetto

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Impossibile trovare il progetto Azure Migrate esistente.

Esistono [due versioni](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate. A seconda della versione in cui è stato creato il progetto, usare uno dei metodi seguenti:

* Se si sta cercando un progetto creato con la versione precedente (esperienza precedente) di Azure Migrate, attenersi alla procedura seguente per trovare il progetto:

    1. Passare alla [portale di Azure](https://portal.azure.com)e cercare **Azure migrate**.
    2. Nel dashboard Azure Migrate verrà visualizzato un banner che cita l'accesso ai progetti precedenti. Questo banner verrà visualizzato solo se è stato creato un progetto con l'esperienza precedente. Selezionare il banner.

       ![Accedi a progetti esistenti](./media/troubleshooting-general/access-existing-projects.png)

    3. Verrà ora visualizzato l'elenco dei progetti esistenti creati con la versione precedente di Azure Migrate.

* Se si sta cercando un progetto creato con la versione corrente (nuova esperienza), seguire questa procedura per trovare il progetto:

    1. Passare alla [portale di Azure](https://portal.azure.com)e cercare **Azure migrate**.
    2. Nel dashboard Azure Migrate andare alla pagina **Server** nel riquadro a sinistra e selezionare **Cambia** nell'angolo superiore destro.

       ![Passa a un progetto di Azure Migrate esistente](./media/troubleshooting-general/switch-project.png)

    3. Selezionare la sottoscrizione appropriata e il progetto Azure Migrate.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Ricerca per categorie creare un secondo progetto Azure Migrate?

Per creare un nuovo progetto di Azure Migrate, attenersi alla procedura seguente:

1. Passare alla [portale di Azure](https://portal.azure.com)e cercare **Azure migrate**.
2. Nel dashboard Azure Migrate andare alla pagina **Server** nel riquadro a sinistra e selezionare **Cambia** nell'angolo superiore destro.

   ![Modificare il progetto Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui**.

   ![Creazione di un secondo progetto di Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quali aree geografiche di Azure supporta Azure Migrate?

Vedere gli elenchi per [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e per [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Ricerca per categorie eliminare i progetti Azure Migrate e le aree di lavoro Log Analytics associate?

Quando si elimina un progetto di Azure Migrate, vengono eliminati il progetto di migrazione *e* i metadati sui computer individuati. Tuttavia, se è stata collegata un'area di lavoro Log Analytics allo strumento Server Assessment, l'area di lavoro Log Analytics non viene automaticamente eliminata. È possibile utilizzare la stessa area di lavoro Log Analytics per più casi di utilizzo. Se si desidera eliminare anche l'area di lavoro Log Analytics, è necessario eseguire questa operazione manualmente:

1. Passare all'area di lavoro Log Analytics collegata al progetto.
     * Se il progetto di migrazione non è ancora stato eliminato, è possibile trovare il collegamento all'area di lavoro dalla pagina Panoramica della valutazione del server nella sezione informazioni di base.

       ![Area di lavoro di Log Analytics](./media/troubleshooting-general/loganalytics-workspace.png)

     * Se è già stato eliminato il progetto di migrazione, selezionare **gruppi di risorse** nel riquadro sinistro del portale di Azure. Andare al gruppo di risorse in cui è stata creata l'area di lavoro e quindi selezionarla.
2. Seguire le istruzioni riportate in [eliminare un'area di lavoro di Azure log Analytics con il portale di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Impossibile creare il progetto di migrazione. errore "le richieste devono contenere intestazioni di identità utente".

Questo problema può verificarsi per gli utenti che non hanno accesso al tenant di Azure Active Directory (Azure AD) dell'organizzazione. Quando un utente viene aggiunto a un tenant di Azure AD per la prima volta, l'utente riceve un invito tramite posta elettronica per aggiungere il tenant. Gli utenti devono accettare l'invito per la corretta aggiunta al tenant. Se il messaggio di posta elettronica non è visibile, contattare un utente che dispone già dell'accesso al tenant e chiedere di inviare di nuovo l'invito tramite la procedura specificata in inviare di nuovo gli [inviti agli utenti Guest](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Dopo la ricezione del messaggio di posta elettronica di invito, è necessario aprire il messaggio di posta elettronica e selezionare il collegamento per accettare l'invito. Quindi, è necessario disconnettersi dal portale di Azure ed eseguire di nuovo l'accesso. (L'aggiornamento del browser non funzionerà). È quindi possibile iniziare a creare il progetto di migrazione.

## <a name="appliance-issues"></a>Problemi relativi all'appliance

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>La distribuzione di un appliance Azure Migrate per VMware non è riuscita con "il file manifesto specificato non è valido: Errore di voce del manifesto OVF non valido.

1. Verificare che il file OVA del dispositivo Azure Migrate venga scaricato correttamente controllando il relativo valore hash. Per istruzioni, vedere [preparare macchine virtuali VMware per la valutazione e la migrazione in Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se il valore hash non corrisponde, scaricare di nuovo il file OVA e riprovare la distribuzione.
2. Se la distribuzione ha ancora esito negativo e si usa il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client Web di vSphere. Se la distribuzione non riesce ancora, provare a usare un altro Web browser.
3. Se si usa il client Web vSphere e si prova a distribuirlo in server vCenter 6,5 o 6,7, provare a distribuire gli OVA direttamente nell'host ESXi attenendosi alla procedura seguente:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) usando il client Web (https://<*indirizzo IP host*>/UI).
   - Passare all'**inventario** **Home** > .
   - Selezionare **file** > **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se la distribuzione ha ancora esito negativo, contattare il supporto Azure Migrate.

### <a name="the-appliance-cant-connect-to-the-internet"></a>L'appliance non è in grado di connettersi a Internet.

Questo comportamento può verificarsi quando il computer in uso è dietro un proxy. Assicurarsi di specificare le credenziali di autorizzazione se sono necessarie per il proxy.
Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di aggiungere gli URL necessari a un elenco Consenti:

- [Valutazione server per VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Valutazione server per Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Migrazione del server per VMware (senza agenti)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Migrazione del server per VMware (basata su agenti)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Migrazione del server per Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Se si usa un proxy di intercettazione per connettersi a Internet, è necessario importare il certificato proxy nella macchina virtuale dell'appliance. È possibile importare il certificato proxy seguendo la procedura descritta in [Azure migrate Appliance](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Errore 802: Errore di sincronizzazione di data e ora.

L'orologio del server potrebbe non essere sincronizzato con l'ora corrente di più di cinque minuti. Modificare l'ora nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente, come illustrato di seguito:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire **w32tm/TZ**.
3. Per sincronizzare l'ora, eseguire **w32tm sull'/Resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Connessione non riuscita. Errore: UnableToConnectToServer.

Potrebbe non essere possibile connettersi a server vCenter *nomeserver*. com: 9443. I dettagli dell'errore indicano che non esiste alcun endpoint in ascolto in https://*ServerName*. com: 9443/SDK che può accettare il messaggio.

In questa situazione, controllare se si sta eseguendo la versione più recente dell'appliance dell'agente di raccolta. In caso contrario, aggiornare l'appliance alla [versione più recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se il problema persiste nella versione più recente, il computer dell'agente di raccolta potrebbe non essere in grado di risolvere il nome del server vCenter specificato oppure la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.

1. Eseguire il ping *ServerName*. com dal computer dell'agente di raccolta.
2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter usando l'indirizzo IP.
3. Identificare il numero di porta corretto per connettersi al server vCenter.
4. Verificare che il server vCenter sia attivo e in esecuzione.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>L'appliance potrebbe non essere stata registrata correttamente nel progetto Azure Migrate (ID errore: 60052).

Questo errore si verifica se l'account Azure usato per registrare l'appliance non dispone di autorizzazioni sufficienti. Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione. [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>L'appliance potrebbe non essere stata registrata correttamente nel progetto Azure Migrate (ID errore: 60039).

Il progetto di Azure Migrate selezionato per registrare l'appliance potrebbe non essere stato trovato. In questa situazione, la registrazione ha esito negativo. Passare alla portale di Azure e verificare se il progetto esiste nel gruppo di risorse. Se il progetto non esiste, creare un nuovo progetto di Azure Migrate nel gruppo di risorse e registrare di nuovo l'appliance. [Altre](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) informazioni sulla creazione di un nuovo progetto Azure migrate.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Operazione di gestione Azure Key Vault non riuscita (ID errore: 60030, 60031).

Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione. Assicurarsi anche che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Non è stato possibile avviare l'individuazione a causa di un errore. Operazione non riuscita per l'elenco di host o cluster specificato (ID errore: 60028).

Non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema durante l'accesso o il recupero delle informazioni sulla macchina virtuale. Il resto degli host è stato aggiunto correttamente. Aggiungere di nuovo gli host elencati nell'errore utilizzando l'opzione **Aggiungi host** . Se si verifica un errore di convalida, rivedere le indicazioni per la correzione per correggere gli errori, quindi provare a eseguire di nuovo l'opzione **Salva e avvia individuazione** .

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Operazione Azure AD non riuscita. Si è verificato un errore durante la creazione o l'aggiornamento dell'applicazione Azure AD (ID errore: 60025).

Questo errore si verifica quando l'account utente di Azure usato per avviare l'individuazione non corrisponde a quello usato per registrare l'appliance. È possibile eseguire una delle operazioni seguenti:
1. Assicurarsi che l'account utente che avvia l'individuazione sia uguale a quello usato per registrare l'appliance.
1. Fornire le autorizzazioni di accesso alle applicazioni di AAD per l'altro account utente per il quale l'operazione di individuazione ha esito negativo.
1. Eliminare il gruppo di risorse creato in precedenza per Azure Migrate progetto e creare un altro gruppo di risorse per riavviarlo.

[Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) informazioni sulle autorizzazioni dell'applicazione AAD.

## <a name="discovery-issues"></a>Problemi di individuazione

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>È stata avviata l'individuazione, ma le macchine virtuali individuate non vengono visualizzate nel portale di Azure. I riquadri **Server Assessment** e **migrazione server** mostrano lo stato "individuazione in corso".
Una volta avviata l'individuazione dal dispositivo, attendere che le macchine individuate vengano visualizzate nel portale di Azure. Sono necessari circa 15 minuti per l'individuazione di VMware e circa 2 minuti per ogni host aggiunto per un'individuazione di Hyper-V. Se si continua a visualizzare "individuazione in corso" anche dopo questi periodi di attesa, selezionare **Aggiorna** nella scheda **Server** . Verrà visualizzato il numero dei server individuati nei riquadri server assessment e migrazione server.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Sto usando un'appliance che individua continuamente l'ambiente locale, ma le macchine virtuali che vengono eliminate nell'ambiente locale vengono ancora visualizzate nel portale.

Sono necessari fino a 30 minuti affinché i dati di individuazione raccolti dal dispositivo vengano riflessi nel portale. Se non vengono visualizzate informazioni aggiornate anche dopo 30 minuti, aggiornare i dati attenendosi alla procedura seguente:

1. Passare a **Server** > **Azure migrate server Assessment**e selezionare **Overview (panoramica**).
2. In **Gestisci**selezionare **integrità agente**
3. Selezionare **Aggiorna agente**. Questa opzione viene visualizzata sotto l'elenco di agenti.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate sulle VM.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Le informazioni più recenti sulle VM locali non sono visualizzate nel portale.

Sono necessari fino a 30 minuti affinché i dati di individuazione raccolti dal dispositivo vengano riflessi nel portale. Se non vengono visualizzate informazioni aggiornate anche dopo 30 minuti, aggiornare i dati attenendosi alla procedura seguente:

1. Passare a **Server** > **Azure migrate server Assessment**e selezionare **Overview (panoramica**).
2. In **Gestisci**selezionare **integrità agente**
3. Selezionare **Aggiorna agente**. Questa opzione viene visualizzata sotto l'elenco di agenti.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate sulle VM.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Non è possibile connettersi a un host o a un cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 (ID errore: 50004).
Questo errore si verifica se il servizio DNS di Azure per l'appliance non riesce a risolvere il cluster o il nome host specificato. Se questo errore viene visualizzato nel cluster, provare a fornire il nome di dominio completo (FQDN) del cluster.

Questo errore può essere visualizzato anche per gli host in un cluster. In questo caso, l'appliance può connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN.

Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
1. Aprire Blocco note come amministratore. Aprire quindi il file C:\Windows\System32\Drivers\etc\hosts.
2. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
3. Salvare e chiudere il file degli host.
4. È possibile verificare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, si dovrebbe essere in grado di visualizzare le informazioni più recenti per questi host nell'portale di Azure.


## <a name="assessment-issues"></a>Problemi di valutazione

### <a name="azure-readiness-issues"></a>Problemi di conformità di Azure

Problema | Correzione
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le VM con un tipo di avvio EFI. È consigliabile convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>È possibile usare Azure Migrate migrazione del server per gestire la migrazione di tali macchine virtuali. Il tipo di avvio della macchina virtuale verrà convertito in BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto ed è necessario un contratto di supporto personalizzato (CSA) per il [supporto in Azure](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Sistema operativo Windows non supportato | Azure supporta solo le [versioni del sistema operativo Windows selezionate](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo le [versioni del sistema operativo Linux selezionate](../virtual-machines/linux/endorsed-distros.md). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux non approvato | Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. Provare ad aggiornare il sistema operativo a una [versione di Linux](../virtual-machines/linux/endorsed-distros.md) approvata prima di eseguire la migrazione ad Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "altro" in server vCenter. Questo comportamento blocca Azure Migrate dalla verifica della conformità di Azure della macchina virtuale. Prima di eseguire la migrazione del computer, assicurarsi che il sistema operativo della macchina virtuale sia [supportato](https://aka.ms/azureoslist) da Azure.
Numero di bit del sistema operativo non supportati | Le VM con un sistema operativo a 32 bit possono essere avviate in Azure, ma è consigliabile aggiornare il sistema operativo della macchina virtuale a 64 bit prima di eseguire la migrazione ad Azure.
Richiede una sottoscrizione di Microsoft Visual Studio | Il computer esegue un sistema operativo client Windows, che è supportato solo tramite una sottoscrizione di Visual Studio.
Macchina virtuale non trovata per le prestazioni di archiviazione richieste | Le prestazioni di archiviazione (operazioni di input/output al secondo [IOPS] e velocità effettiva) richieste per il computer superano il supporto delle macchine virtuali di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Macchina virtuale non trovata per le prestazioni di rete richieste | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
La macchina virtuale non è stata trovata nel percorso specificato | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi non idonei | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure. Azure Migrate: Server Assessment attualmente non supporta i dischi Ultra SSD e valuta i dischi in base ai limiti del disco per Managed disks Premium (32 TB).  Per ogni disco collegato alla macchina virtuale, assicurarsi che la dimensione del disco sia < 64 TB (supportata da dischi Ultra SSD), in caso contrario, ridurre le dimensioni del disco prima di eseguire la migrazione ad Azure o usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati. Assicurarsi che le prestazioni (IOPS e velocità effettiva) richieste da ogni disco siano supportate dai [dischi delle macchine virtuali gestite](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)da Azure.
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure Migrate: Server Assessment attualmente non supporta i dischi Ultra SSD e valuta i dischi in base ai limiti del disco Premium (32 TB). Tuttavia, Azure supporta dischi con dimensioni fino a 64 TB (supportate da dischi Ultra SSD). Compattare i dischi fino a 64 TB prima della migrazione oppure usare più dischi in Azure e [rimuoverli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non è riuscito a trovare un tipo di macchina virtuale appropriato. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per uno o più dischi a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità per una o più schede di rete a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Non è possibile specificare Enterprise Agreement (EA) come offerta di Azure nelle proprietà di valutazione.
Azure Migrate server Assessment non supporta attualmente i prezzi basati su Enterprise Agreement (EA). Per ovviare a questa limitazione, usare l'offerta con **pagamento in base** al consumo come Azure e usare la proprietà **discount** per specificare gli sconti personalizzati ricevuti. [Altre informazioni su come personalizzare una valutazione](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Perché la valutazione del server contrassegna le VM Linux in modo condizionale?
È presente un gap noto in server assessment che impedisce di rilevare la versione secondaria del sistema operativo Linux installato nelle VM locali, ad esempio per RHEL 6,10, attualmente server Assessment rileva solo RHEL 6 come versione del sistema operativo. Poiché Azure approva solo versioni specifiche di Linux, le VM Linux sono attualmente contrassegnate come predisposte in modo condizionale nella valutazione del server. È possibile determinare se il sistema operativo Linux in esecuzione nella macchina virtuale locale è approvato in Azure esaminando la [documentazione del supporto per Linux di Azure.](https://aka.ms/migrate/selfhost/azureendorseddistros) Dopo aver verificato la distribuzione approvata, è possibile ignorare questo avviso.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Perché lo SKU di VM consigliato da server assessment ha più core e più memoria rispetto a quello allocato in locale?
Lo SKU della macchina virtuale consigliato in Server Assessment dipende dalle proprietà della valutazione. È possibile creare due tipi di valutazione in server assessment: *Basata sulle prestazioni* e *come in locale*. Per le valutazioni basate sulle prestazioni, valutazione server considera i dati di utilizzo delle macchine virtuali locali (CPU, memoria, disco e utilizzo della rete) per determinare lo SKU di VM di destinazione appropriato per le macchine virtuali locali. Inoltre, per il dimensionamento in base alle prestazioni, il fattore di comfort viene considerato per determinare un utilizzo efficace. Per il dimensionamento locale, i dati sulle prestazioni non vengono considerati ed è consigliabile usare uno SKU di destinazione in base a quanto allocato in locale.

Si immagini, ad esempio, che esista una macchina virtuale locale con 4 core e 8 GB di memoria con un utilizzo della CPU del 50% e un utilizzo della memoria del 50% e che nella valutazione sia specificato un fattore di comfort 1,3. Se i criteri di ridimensionamento della valutazione sono **locali**, è consigliabile usare uno SKU di VM di Azure con 4 core e 8 gigabyte (GB) di memoria.

Tuttavia, si supponga che i criteri di ridimensionamento siano basati sulle prestazioni. In base all'utilizzo effettivo di CPU e memoria (50% di 4 core * 1,3 = 2,6 core e 50% di 8 GB di memoria * 1,3 = 5,3-GB di memoria), è consigliabile usare lo SKU di VM più economico di quattro core (il numero di core supportato più vicino) e 8 GB di memoria (le dimensioni di memoria supportate più vicine). [Altre informazioni su come Server Assessment esegue il ridimensionamento.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Perché lo SKU del disco consigliato da server assessment è più grande di quello allocato in locale?
Il dimensionamento del disco nella valutazione del server dipende da due proprietà di valutazione: criteri di ridimensionamento e tipo di archiviazione. Se i criteri di ridimensionamento sono **basati sulle prestazioni** e il tipo di archiviazione è impostato su **automatico**, i valori di IOPS e velocità effettiva del disco vengono considerati per identificare il tipo di disco di destinazione (HDD standard, SDD standard o dischi Premium). È quindi consigliabile usare uno SKU del disco dal tipo di disco e questa raccomandazione considera i requisiti di dimensioni del disco locale. Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è **Premium**, è consigliabile usare uno SKU di dischi Premium in Azure in base ai requisiti di IOPS, velocità effettiva e dimensioni del disco locale. La stessa logica viene usata per eseguire il ridimensionamento del disco quando i criteri di ridimensionamento sono **locali** e il tipo di archiviazione è **HDD standard**, **SDD standard**o **Premium**.

Se, ad esempio, si dispone di un disco locale con 32 GB di memoria, ma le operazioni di i/o di lettura e scrittura aggregate per il disco sono 800 IOPS, server Assessment consiglierà un tipo di disco Premium (a causa dei requisiti di IOPS più elevati) e quindi consiglierà uno SKU del disco in grado di supportare IOPS e dimensioni obbligatorie. In questo esempio la corrispondenza più vicina sarebbe P15 (256 GB, 1.100 operazioni di I/O al secondo). Anche se le dimensioni richieste dal disco locale sono 32 GB, server Assessment consiglia un disco più grande a causa del requisito di IOPS elevato del disco locale.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Perché il report di valutazione indica "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" per alcune VM?
Questi problemi vengono segnalati quando l'appliance Azure Migrate non riesce a raccogliere i dati sulle prestazioni per le macchine virtuali locali. Questo comportamento può verificarsi se le macchine virtuali sono spente per la durata della fase di creazione della valutazione (ultimo giorno/una settimana/mese). L'appliance non può raccogliere i dati sulle prestazioni per una macchina virtuale quando è spenta. Se mancano solo i contatori di memoria e si sta provando a valutare le VM Hyper-V, controllare se la memoria dinamica è abilitata in queste VM. Esiste un problema noto in cui un appliance Azure Migrate non è in grado di raccogliere dati di utilizzo della memoria per le macchine virtuali in cui non è abilitata la memoria dinamica. Questo problema riguarda solo le macchine virtuali Hyper-V e non le macchine virtuali VMware. Se non è presente alcun contatore delle prestazioni, Azure Migrate server Assessment esegue il fallback alla memoria e ai core allocati e suggerisce le dimensioni della VM corrispondenti.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Il costo della licenza del sistema operativo della macchina virtuale è incluso nel costo di calcolo stimato da server Assessment?
Server Assessment considera attualmente il costo della licenza del sistema operativo solo per i computer Windows. I costi delle licenze del sistema operativo per i computer Linux non sono attualmente considerati.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'effetto della cronologia delle prestazioni e dell'utilizzo percentile sui consigli sulle dimensioni?
Queste proprietà si applicano solo al dimensionamento basato sulle prestazioni. Server Assessment raccoglie continuamente i dati delle prestazioni dei computer locali e li usa per consigliare lo SKU della macchina virtuale e del disco in Azure. Questi dati sulle prestazioni vengono raccolti da valutazione server come indicato di seguito:
- Il Azure Migrate appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi per le macchine virtuali VMware e ogni 30 secondi per le VM Hyper-V.
- Il dispositivo esegue il rollup degli esempi di 20 e 30 secondi per creare un singolo punto dati per ogni 10 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20 secondi e 30 secondi, quindi lo invia ad Azure.
- Quando si crea una valutazione in Server Assessment, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, viene identificato il valore relativo all'utilizzo rappresentativo. Se, ad esempio, la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è pari a 95, Azure Migrate ordina tutti i punti di campionamento di 10 minuti per l'ultima settimana in ordine crescente e quindi seleziona il 95 ° percentile come valore rappresentativo.
Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se si sceglie il 99 ° percentile. Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, è necessario selezionare il 99 ° percentile per l'utilizzo percentile.

## <a name="dependency-visualization-issues"></a>Problemi di visualizzazione delle dipendenze

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Non è possibile trovare la funzionalità di visualizzazione delle dipendenze per i progetti di Azure per enti pubblici.

Azure Migrate dipende da Mapping dei servizi per la funzionalità di visualizzazione delle dipendenze. Poiché Mapping dei servizi non è attualmente disponibile in Azure per enti pubblici, questa funzionalità non è disponibile in Azure per enti pubblici.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>È stato installato Microsoft Monitoring Agent (MMA) e Dependency Agent nelle macchine virtuali locali, ma le dipendenze sono ora visualizzate nel portale di Azure Migrate.

Dopo aver installato gli agenti, Azure Migrate in genere impiega 15-30 minuti per visualizzare le dipendenze nel portale. Se sono state attese più di 30 minuti, assicurarsi che MMA possa comunicare con l'area di lavoro di OMS attenendosi alla procedura seguente.

Per la macchina virtuale Windows:
1. Passare al pannello di controllo e avviare Microsoft Monitoring Agent.
2. Nella scheda **log Analytics di Azure (OMS)** della finestra di dialogo **proprietà Microsoft Monitoring Agent** verificare che lo **stato** dell'area di lavoro sia verde.
3. Se lo stato non è di colore verde, provare a rimuovere l'area di lavoro e aggiungerla nuovamente a MMA.

      ![Finestra di dialogo Proprietà MMA](./media/troubleshooting-general/mma-status.png)

Per la macchina virtuale Linux, assicurarsi che i comandi di installazione di MMA e Dependency Agent siano riusciti.

### <a name="what-operating-systems-does-mma-support"></a>Quali sistemi operativi sono supportati da MMA?

 [Ecco un elenco dei sistemi operativi Windows supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
[Ecco un elenco dei sistemi operativi Linux supportati da MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Quali sistemi operativi sono supportati da Dependency Agent?

[Ecco un elenco dei [sistemi operativi Windows e Linux supportati da monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Non è possibile visualizzare le dipendenze in Azure Migrate per un periodo di tempo superiore a un'ora.
In Azure Migrate è possibile visualizzare le dipendenze per un periodo di tempo di un'ora. Sebbene Azure Migrate consenta di tornare a una data specifica nell'ultimo mese, la durata massima per la quale è possibile visualizzare le dipendenze è di un'ora.

Ad esempio, è possibile usare la funzionalità durata tempo nella mappa delle dipendenze per visualizzare le dipendenze di ieri, ma è possibile visualizzarle solo per un periodo di un'ora. Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati di dipendenza](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Non è possibile visualizzare le dipendenze per i gruppi che hanno più di 10 macchine virtuali.
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) che dispongono di un massimo di 10 macchine virtuali, se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile suddividere il gruppo in gruppi più piccoli e visualizzare le dipendenze.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Ho installato gli agenti e usato la visualizzazione delle dipendenze per creare gruppi. Ora, dopo il failover, i computer mostrano l'azione "Installa agente" invece di "Visualizza dipendenze".
* Dopo un failover pianificato o non pianificato, i computer locali sono spenti e i computer equivalenti vengono attivati in Azure. Queste macchine acquisiscono un indirizzo MAC diverso Potrebbero acquisire un indirizzo IP diverso a seconda che l'utente abbia scelto o meno di mantenere un indirizzo IP locale.

  Se gli indirizzi MAC e IP sono diversi, Azure Migrate non associa i computer locali a tutti i Mapping dei servizi dati sulle dipendenze. Viene invece richiesto all'utente di installare gli agenti anziché visualizzare le dipendenze.
* Failover di post-test, i computer locali rimangono accesi come previsto. Computer equivalenti in Azure acquisiscono un indirizzo MAC diverso e potrebbero acquisire indirizzi IP diversi. A meno che l'utente non blocchi il traffico dei log di monitoraggio di Azure in uscita da questi computer, Azure Migrate non associa i computer locali con i dati di dipendenza Mapping dei servizi e chiede agli utenti di installare gli agenti anziché visualizzare le dipendenze.

## <a name="collect-azure-portal-logs"></a>Raccogli log portale di Azure

**Ricerca per categorie raccogliere portale di Azure log del traffico di rete?**

1. Aprire il browser, passare [al portale ed eseguire](https://portal.azure.com)l'accesso.
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
