---
title: Risolvere problemi relativi ad Azure Migrate | Microsoft Docs
description: Questo articolo offre una panoramica dei problemi noti relativi al servizio Azure Migrate e alcuni suggerimenti per la risoluzione degli errori comuni.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302320"
---
# <a name="troubleshoot-azure-migrate"></a>Risolvere i problemi relativi ad Azure Migrate

[Azure migrate](migrate-services-overview.md) offre un hub di Microsoft Tools per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti. Questo documento fornisce informazioni sulla risoluzione degli errori con Azure Migrate Azure Migrate: Valutazione del server e Azure Migrate: Migrazione del server.

## <a name="azure-migrate-project-issues"></a>Problemi di Azure Migrate progetto

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Impossibile trovare il progetto Azure Migrate esistente.

Esistono [due versioni](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) di Azure migrate. A seconda della versione in cui è stato creato il progetto, attenersi alla procedura seguente per trovare il progetto:

1. Se si sta cercando un progetto creato con la versione precedente (esperienza precedente) di Azure Migrate, attenersi alla procedura seguente per trovare il progetto.

    1. Passare a [portale di Azure](https://portal.azure.com), cercare **Azure migrate**.
    2. Nel dashboard Azure Migrate viene visualizzato un banner che tratta di accedere ai progetti precedenti. Questo banner verrà visualizzato solo se è stato creato un progetto con l'esperienza precedente. Fare clic sull'intestazione.

    ![Accedi a progetti esistenti](./media/troubleshooting-general/access-existing-projects.png)

    3. Verrà ora visualizzato l'elenco dei progetti esistenti creati con la versione precedente di Azure Migrate.

2. Se si sta cercando un progetto creato con la versione corrente (nuova esperienza), seguire questa procedura per trovare il progetto.

    1. Passare a [portale di Azure](https://portal.azure.com), cercare **Azure migrate**.
    2. Nel dashboard Azure Migrate andare alla pagina **Server** nel riquadro a sinistra e selezionare **modifica** nell'angolo superiore destro:

    ![Passa a un progetto di Azure Migrate esistente](./media/troubleshooting-general/switch-project.png)

    3. Selezionare la **sottoscrizione** appropriata e **migrare il progetto**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Non è possibile creare un secondo progetto di Azure Migrate.

Attenersi ai passaggi seguenti per creare un nuovo progetto Azure Migrate.

1. Passare a [portale di Azure](https://portal.azure.com), cercare **Azure migrate**.
2. Nel dashboard Azure Migrate andare alla pagina **Server** nel riquadro a sinistra e selezionare **modifica** nell'angolo superiore destro:

   ![Modificare il progetto Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui** , come illustrato nella schermata seguente:

   ![Creazione di un secondo progetto di Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Eliminazione dei progetti Azure Migrate e dell'area di lavoro Log Analytics associata

Quando si elimina un progetto di Azure Migrate, viene eliminato il progetto di migrazione insieme ai metadati sui computer individuati. Tuttavia, se è stata collegata un'area di lavoro Log Analytics allo strumento Server Assessment, l'area di lavoro Log Analytics non viene eliminata automaticamente. Questo avviene perché la stessa area di lavoro Log Analytics è applicabile a più casi d'uso. Se si vuole eliminare anche l'area di lavoro Log Analytics, è necessario eseguire questa operazione manualmente.

1. Passare all'area di lavoro Log Analytics collegata al progetto.
     1. Se il progetto di migrazione non è ancora stato eliminato, è possibile trovare il collegamento all'area di lavoro dalla pagina Panoramica della valutazione del server nella sezione informazioni di base.

     ![Area di lavoro di Log Analytics](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Se è già stato eliminato il progetto di migrazione, selezionare **gruppi di risorse** nel riquadro sinistro del portale di Azure. Andare al gruppo di risorse in cui è stata creata l'area di lavoro e quindi selezionarla.
2. Per eliminare l'area di lavoro, seguire le istruzioni riportate [in questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>La creazione del progetto di migrazione non è riuscita con errore *Requests must contain user identity headers* (Le richieste devono contenere le intestazioni di identità dell'utente)

Questo problema può verificarsi per gli utenti che non hanno accesso al tenant di Azure Active Directory (Azure AD) dell'organizzazione. Quando un utente viene aggiunto a un tenant di Azure AD per la prima volta, riceve un invito tramite posta elettronica per entrare nel tenant. Gli utenti devono accedere al messaggio di posta elettronica e accettare l'invito per essere aggiunti correttamente al tenant. Se non si riesce a visualizzare il messaggio di posta elettronica, contattare un utente che già dispone dell'accesso al tenant e chiedere di inviare di nuovo l'invito tramite la procedura specificata [qui](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Una volta ricevuto il messaggio di posta elettronica di invito, è necessario aprirlo e fare clic sul collegamento al suo interno per accettare l'invito. Al termine di questa operazione, è necessario disconnettersi da portale di Azure ed eseguire di nuovo l'accesso. l'aggiornamento del browser non funzionerà. È quindi possibile provare a creare il progetto di migrazione.

## <a name="appliance-issues"></a>Problemi relativi all'appliance

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>La distribuzione di Azure Migrate Appliance per VMware non è riuscita con l'errore: The provided manifest file is invalid: Invalid OVF manifest entry. (Il file manifesto specificato non è valido: voce del manifesto OVF non valida.)

1. Verificare se il file OVA del dispositivo Azure Migrate viene scaricato correttamente controllando il relativo valore hash. Per verificare il valore hash, vedere questo [articolo](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance). Se il valore hash non corrisponde, scaricare nuovamente il file OVA e ripetere il tentativo di distribuzione.
2. Se ancora non riesce e si usa VMware vSphere Client per distribuire il file OVF, provare a eseguire la distribuzione con vSphere Web Client. Se il problema persiste, provare a usare un Web browser diverso.
3. Se si usa vSphere Web Client e si prova a distribuirlo in un server vCenter 6.5 o 6.7, provare a distribuire il file OVA direttamente nell'host ESXi seguendo questi passaggi:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) usando il client Web (https://<*indirizzo IP host*>/UI).
   - Passare all'**inventario** **Home** > .
   - Fare clic su **file** > **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>Il dispositivo non è in grado di connettersi a Internet

Ciò può verificarsi quando il computer in uso è protetto da un proxy. Assicurarsi di fornire le credenziali di autorizzazione, se richiesto dal proxy.
Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di aggiungere gli URL necessari a un elenco Consenti:

Scenario | Elenco URL
--- | ---
Valutazione server per VMware | [Qui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Valutazione server per Hyper-V | [Qui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Migrazione del server per VMware (senza agenti) | [Qui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Migrazione del server per VMware (basata su agenti) | [Qui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Migrazione del server per Hyper-V | [Qui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Se si usa un proxy di intercettazione per connettersi a Internet, è necessario importare il certificato proxy nella macchina virtuale del dispositivo. È possibile importare il certificato del proxy eseguendo i passaggi descritti [qui](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Errore 802: errore di sincronizzazione di data e ora

È possibile che l'orologio del server abbia perso la sincronizzazione con l'ora corrente da più di cinque minuti. Modificare l'ora nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente, come illustrato di seguito:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire w32tm /tz.
3. Per sincronizzare l'ora, eseguire w32tm /resync.


### <a name="error-unabletoconnecttoserver"></a>Errore UnableToConnectToServer

Non è possibile connettersi al server vCenter "Servername.com:9443" a causa dell'errore: Nessun endpoint in ascolto su https://Servername.com:9443/sdk in grado di accettare il messaggio.

Controllare se si sta usando la versione più recente dell'appliance dell'agente di raccolta; in caso contrario, aggiornare l'appliance alla [versione più recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se l'errore si verifica anche con la versione più recente, è possibile che computer dell'agente di raccolta non sia in grado di risolvere il nome del server vCenter specificato o la porta specificata non è corretta. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi alla porta numero 443.

1. Provare a effettuare il ping di Servername.com dal computer dell'agente di raccolta.
2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter sull'indirizzo IP.
3. Identificare il numero di porta corretto per connettersi al server vCenter.
4. Infine controllare che il server vCenter sia attivo e in esecuzione.

## <a name="discovery-issues"></a>Problemi di individuazione

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>È stata avviata l'individuazione, ma le macchine virtuali individuate non vengono visualizzate in portale di Azure. I riquadri server assessment e server migrate mostrano lo stato "individuazione in corso"
Dopo l'avvio dell'individuazione dal dispositivo, attendere il tempo necessario per la visualizzazione dei computer individuati nella portale di Azure. Sono necessari circa 15 minuti per l'individuazione di VMware e circa 2 minuti per ogni host aggiunto per un'individuazione di Hyper-V. Se si continua a visualizzare "individuazione in corso" anche dopo questa volta, fare clic su **Aggiorna** nella scheda **Server** . Verrà visualizzato il numero dei server individuati nei riquadri server assessment e migrazione server.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Si sta usando l'appliance che individua continuamente l'ambiente locale, ma le macchine virtuali che vengono eliminate nell'ambiente locale sono ancora visualizzate nel portale.

Sono necessari fino a 30 minuti per i dati di individuazione raccolti dall'appliance per la reflection nel portale. Se non vengono visualizzate informazioni aggiornate anche dopo 30 minuti, eseguire un aggiornamento sui dati attenendosi alla procedura seguente:

1. In server > Azure Migrate: Server Assessment, fare clic su **Panoramica**.
2. In **Gestisci**fare clic su **integrità agente**
3. Fare clic sull'opzione **Aggiorna agente**. Questa opzione viene visualizzata sotto l'elenco di agenti.
4. Attendere il completamento dell'operazione di aggiornamento. Verificare che sia possibile visualizzare le informazioni aggiornate sulle VM.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Non vengono fornite le informazioni più recenti sulle VM locali nel portale

Sono necessari fino a 30 minuti per i dati di individuazione raccolti dall'appliance per la reflection nel portale. Se non vengono visualizzate informazioni aggiornate anche dopo 30 minuti, eseguire un aggiornamento sui dati attenendosi alla procedura seguente:

1. In server > Azure Migrate: Server Assessment, fare clic su **Panoramica**.
2. In **Gestisci**fare clic su **integrità agente**
3. Fare clic sull'opzione **Aggiorna agente**. Questa opzione viene visualizzata sotto l'elenco di agenti.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate sulle VM.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Non è possibile connettersi a host o cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 (ID errore: 50004)
Questo errore si verifica se il DNS che funge da dispositivo non è in grado di risolvere il nome host o il cluster specificato. Se questo errore viene visualizzato nel cluster, provare a fornire il nome di dominio completo del cluster. 

Questo errore può essere visualizzato anche per gli host in un cluster. In questo caso, l'appliance potrebbe connettersi al cluster. Ma il cluster ha restituito i nomi host che non sono nomi di dominio completi. 

Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host.
1. Aprire Blocco note come utente amministratore. Aprire il file C:\Windows\System32\Drivers\etc\hosts.
2. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
3. Salvare e chiudere il file degli host.
4. È possibile verificare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, si dovrebbe essere in grado di visualizzare le informazioni più recenti su questi host nell'portale di Azure. 


## <a name="assessment-issues"></a>Problemi di valutazione

### <a name="azure-readiness-issues"></a>Problemi di conformità di Azure

Problema | Correzione
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le macchine virtuali con tipo di avvio EFI. È consigliabile convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>È possibile usare la migrazione del server Azure Migrate per eseguire la migrazione di tali macchine virtuali in quanto converte il tipo di avvio della macchina virtuale nel BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto e necessita di un contratto di supporto personalizzato per usufruire del [supporto in Azure](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Sistema operativo Windows non supportato | Azure supporta solo [determinate versioni del sistema operativo Windows](https://aka.ms/WSosstatement). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo [determinate versioni del sistema operativo Linux](../virtual-machines/linux/endorsed-distros.md). Provare ad aggiornare il sistema operativo della macchina virtuale prima di eseguire la migrazione ad Azure.
Sistema operativo Linux non approvato | La macchina virtuale può essere avviata in Azure, ma Azure non fornisce alcun supporto del sistema operativo. Provare ad aggiornare il sistema operativo a una [versione di Linux approvata](../virtual-machines/linux/endorsed-distros.md) prima di eseguire la migrazione ad Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "Altro" nel server vCenter, pertanto Azure Migrate non è in grado di identificare l'idoneità per Azure della macchina virtuale. Verificare che il sistema operativo in esecuzione nella macchina sia [supportato](https://aka.ms/azureoslist) da Azure prima di eseguire la migrazione della macchina.
Numero di bit del sistema operativo non supportati | Le macchine virtuali con sistema operativo a 32 bit possono essere avviate in Azure, ma è consigliabile aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione ad Azure.
Richiede una sottoscrizione di Visual Studio. | Il computer dispone di un sistema operativo client Windows in esecuzione al suo interno, che è supportato solo nella sottoscrizione di Visual Studio.
Non è stata trovata alcuna macchina virtuale per le prestazioni di archiviazione richieste. | Le prestazioni di archiviazione (IOPS/velocità effettiva) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Non è stata trovata alcuna macchina virtuale per le prestazioni di rete richieste. | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
La macchina virtuale non è stata trovata nella località specificata. | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi non idonei. | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure. Per ogni disco collegato alla macchina virtuale, verificare che le dimensioni disco siano < 4 TB; in caso contrario, ridurre le dimensioni disco prima di eseguire la migrazione ad Azure. Assicurarsi che le prestazioni necessarie per ogni disco (IOPS/velocità effettiva) siano supportata dai [dischi gestiti della macchina virtuale](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) di Azure.   
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure supporta dischi di dimensioni fino a 4 TB. Ridurre le dimensioni del disco a meno di 4 TB prima di eseguire la migrazione.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non può trovare un tipo di macchina virtuale idonea. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità di uno o più dischi a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità di una o più schede di rete a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Non è possibile specificare Enterprise Agreement (EA) come offerta di Azure nelle proprietà di valutazione?
Azure Migrate: Server Assessment attualmente non supporta i prezzi basati su Enterprise Agreement (EA). La soluzione alternativa consiste nell'usare "pagamento a consumo" come offerta Azure e usare la proprietà "Discount" per specificare gli sconti personalizzati ricevuti. [Altre informazioni su come è possibile personalizzare una valutazione](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Perché la valutazione del server contrassegna le VM Linux in modo condizionale. È necessario eseguire questa operazione per risolvere il problema?
Si verifica un gap noto in server Assessment, in cui non è possibile rilevare la versione secondaria del sistema operativo Linux installato nelle VM locali (ad esempio, per RHEL 6,10, attualmente server Assessment rileva solo RHEL 6 come versione del sistema operativo). Poiché Azure approva solo versioni specifiche di Linux, le VM Linux sono attualmente contrassegnate come predisposte in modo condizionale nella valutazione del server. È possibile verificare manualmente se il sistema operativo Linux in esecuzione nella macchina virtuale locale è approvato in Azure esaminando la [documentazione del supporto per Linux di Azure.](https://aka.ms/migrate/selfhost/azureendorseddistros) Dopo aver verificato la distribuzione approvata, è possibile ignorare questo avviso.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Lo SKU di VM consigliato da server assessment ha un numero maggiore di core e una dimensione della memoria maggiore rispetto a quella allocata in locale. Per quale motivo?
La raccomandazione SKU della macchina virtuale in server Assessment dipende dalle proprietà di valutazione. È possibile creare due tipi di valutazione in valutazione del server, valutazioni basate sulle prestazioni e come valutazioni locali. Per le valutazioni basate sulle prestazioni, valutazione server considera i dati di utilizzo delle macchine virtuali locali (CPU, memoria, disco e utilizzo della rete) per determinare lo SKU di VM di destinazione appropriato per le macchine virtuali locali. Inoltre, per il dimensionamento in base alle prestazioni, il fattore di comfort viene tenuto in considerazione per identificare l'utilizzo effettivo. Per quanto riguarda il dimensionamento locale, i dati sulle prestazioni non vengono considerati ed è consigliabile usare uno SKU di destinazione in base a quanto allocato in locale.

Si immagini, ad esempio, che esista una macchina virtuale locale con 4 core e 8 GB di memoria con un utilizzo della CPU del 50% e l'utilizzo della memoria del 50% e che nella valutazione sia specificato un fattore di comfort 1,3. Se il criterio di dimensionamento della valutazione è' come locale ', è consigliabile usare uno SKU di VM di Azure con 4 core e 8 GB di memoria, se il criterio di dimensionamento è basato sulle prestazioni, in base all'utilizzo effettivo di CPU e memoria (50% di 4 core * 1,3 = 2,6 core e 50% di 8 GB memoria * 1,3 = 5,3-GB di memoria), è consigliabile usare lo SKU di VM più economico di quattro core (numero di core supportato più vicino) e le dimensioni della memoria di 8 GB (la dimensione di memoria supportata più vicina). [Ulteriori informazioni su come la valutazione del server esegue il ridimensionamento.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>Lo SKU del disco consigliato da server assessment ha una dimensione maggiore rispetto a quella allocata in locale. Per quale motivo?
Il dimensionamento del disco in server Assessment dipende dalle due proprietà di valutazione, ovvero dal criterio di ridimensionamento e dal tipo di archiviazione. Se il criterio di ridimensionamento è "basato sulle prestazioni" e il tipo di archiviazione è impostato su "automatico", vengono considerati i valori di IOPS e velocità effettiva del disco per identificare il tipo di disco di destinazione (HDD Standard, SDD Standard o dischi Premium). Uno SKU del disco all'interno del tipo di disco viene quindi consigliato considerando i requisiti di dimensioni del disco locale. Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è "Premium", è consigliabile usare uno SKU di dischi Premium in Azure in base ai requisiti di IOPS, velocità effettiva e dimensioni del disco locale. La stessa logica viene utilizzata per eseguire il dimensionamento del disco quando il criterio di ridimensionamento è' As locale ' dimensionation and storage type is ' HDD Standard ',' SDD Standard ' o ' Premium '.

Ad esempio, se si dispone di un disco locale con 32 GB di memoria, ma le operazioni di i/o di lettura e scrittura aggregate per il disco sono 800 IOPS, server Assessment consiglierà un tipo di disco Premium (a causa dei requisiti di IOPS più elevati) e quindi consiglierà uno SKU del disco, che può supportare IOPS e dimensioni obbligatorie. La corrispondenza più vicina in questo esempio è P15 (256 GB, 1100 IOPS). Quindi, anche se le dimensioni richieste dal disco locale sono 32 GB, server Assessment consiglia un disco con dimensioni maggiori a causa del requisito di IOPS elevato del disco locale.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Il costo della licenza del sistema operativo della macchina virtuale è incluso nel costo di calcolo stimato da server Assessment?
Server Assessment considera attualmente solo il costo della licenza del sistema operativo per i computer Windows, il costo della licenza del sistema operativo per i computer Linux non è attualmente considerato. 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'impatto della cronologia delle prestazioni e dell'utilizzo percentile sulle dimensioni consigliate?
Queste proprietà sono valide solo per il dimensionamento basato sulle prestazioni. Server assessment raccoglie continuamente i dati sulle prestazioni dei computer locali e li usa per consigliare lo SKU della macchina virtuale e lo SKU del disco in Azure. Di seguito viene illustrato il modo in cui i dati sulle prestazioni vengono raccolti da valutazione server:
- Il Azure Migrate appliance continua a profilare l'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi per le macchine virtuali VMware e ogni 30 secondi per le VM Hyper-V.
- Il dispositivo esegue il rollup degli esempi di 20/30 secondi per creare un singolo punto dati per ogni 10 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore massimo da tutti gli esempi di 20/30 secondi e lo invia ad Azure.
- Quando si crea una valutazione in server Assessment, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, viene identificato il valore di utilizzo rappresentativo. Se, ad esempio, la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è pari a 95, Azure Migrate ordina tutti i punti di campionamento di 10 minuti per l'ultima settimana in ordine crescente e quindi seleziona il 95 ° percentile come valore rappresentativo.
Il valore del 95 ° percentile garantisce che gli outlier vengano ignorati, che possono essere inclusi se si sceglie il 99 ° percentile. Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, è consigliabile selezionare 99 ° percentile come utilizzo percentile.

## <a name="dependency-visualization-issues"></a>Problemi di visualizzazione delle dipendenze

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Non è possibile trovare la funzionalità di visualizzazione delle dipendenze per i progetti di Azure per enti pubblici.

Azure Migrate dipende dal mapping dei servizi per la funzionalità di visualizzazione delle dipendenze e, poiché il mapping dei servizi non è attualmente disponibile in Azure per enti pubblici, anche questa funzionalità non è disponibile in Azure per enti pubblici.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>È stato installato Microsoft Monitoring Agent (MMA) e Dependency Agent nelle macchine virtuali locali, ma le dipendenze sono ora visualizzate nel portale di Azure Migrate.

Dopo aver installato gli agenti, Azure Migrate richiede in genere 15-30 minuti per visualizzare le dipendenze nel portale. Se si è atteso per più di 30 minuti, verificare che l'agente MMA sia in grado di comunicare con l'area di lavoro OMS, seguendo questa procedura:

Per la macchina virtuale Windows:
1. Passare al **Pannello di controllo** e avviare **Microsoft Monitoring Agent**.
2. Passare alla scheda **log Analytics di Azure (OMS)** nel popup delle proprietà di MMA.
3. Verificare che lo **Stato** dell'area di lavoro sia di colore verde.
4. Se lo stato non è di colore verde, provare a rimuovere l'area di lavoro e aggiungerla nuovamente a MMA.
        ![Stato MMA](./media/troubleshooting-general/mma-status.png)

Per la macchina virtuale Linux, verificare che i comandi di installazione per l'agente MMA e per il Dependency Agent abbiano avuto esito positivo.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quali sono i sistemi operativi supportati da MMA?

L'elenco dei sistemi operativi Windows supportati da MMA è reperibile [qui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
L'elenco dei sistemi operativi Linux supportati da MMA è reperibile [qui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quali sono i sistemi operativi supportati dal Dependency Agent?

L'elenco dei sistemi operativi Windows supportati dal Dependency Agent è reperibile [qui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
L'elenco dei sistemi operativi Linux supportati dal Dependency Agent è reperibile [qui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Non riesco a visualizzare le dipendenze in Azure Migrate per più di un'ora.
Azure Migrate consente di visualizzare le dipendenze per la durata di un'ora. Azure Migrate tuttavia consente di tornare a una determinata data nella cronologia al massimo nel mese precedente. Il tempo massimo per cui possibile visualizzare le dipendenze è un'ora. Ad esempio, è possibile usare la funzionalità di durata nella mappa delle dipendenze per visualizzare le dipendenze di ieri ma possono essere visualizzate solo per un'ora. Tuttavia, è possibile usare i log di monitoraggio di Azure per [eseguire query sui dati di dipendenza](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per un periodo di tempo più lungo.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Non riesco a visualizzare le dipendenze per i gruppi con più di 10 macchine virtuali.
È possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) che hanno fino a 10 macchine virtuali. Se si dispone di un gruppo con più di 10 macchine virtuali, è consigliabile dividere il gruppo in gruppi più piccoli e visualizzarne le dipendenze.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Ho installato gli agenti e usato la visualizzazione delle dipendenze per creare gruppi. Ora, in seguito a un failover, sulle macchine viene visualizzata l'azione "Installa agente" anziché "Visualizza dipendenze"
* In seguito a un failover pianificato o non pianificato, le macchine locali vengono disattivate e le macchine equivalenti vengono riattivate in Azure. Queste macchine acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso, in base alla scelta dell'utente di mantenere o meno l'indirizzo IP locale. Se gli indirizzi IP e MAC sono diversi, Azure Migrate non associa le macchine locali ai dati sulle dipendenze di Elenco dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.
* In seguito al failover di test, le macchine locali rimangono attivate come previsto. Le macchine equivalenti riattivate in Azure acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso. A meno che l'utente blocchi il traffico dei log di Azure in uscita da questi computer, Azure Migrate non associa i computer locali con i dati di dipendenza Mapping dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.

## <a name="collect-azure-portal-logs"></a>Raccogli log portale di Azure

**Ricerca per categorie raccogliere portale di Azure log del traffico di rete?**

1. Aprire il browser e accedere [al portale](https://portal.azure.com).
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione **Cancella voci durante l'esplorazione**.
3. Fare clic sulla scheda **Rete** e avviare l'acquisizione del traffico di rete:
   - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'acquisizione del traffico. Se non viene visualizzato, fare clic sul cerchio nero per avviarlo.
   - In Microsoft Edge/Internet Explorer la registrazione viene avviata automaticamente. In caso contrario, fare clic sul pulsante di esecuzione verde.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
   - In Chrome fare clic con il pulsante destro del mouse su **Save as HAR with content** (Salva come HAR con contenuto). Comprime ed Esporta i log come file con estensione har.
   - In Microsoft Edge/Internet Explorer fare clic sull'icona **Esporta traffico catturato**. Questo comprime ed esporta il log.
6. Passare alla scheda **Console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
   - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome**, per esportare e comprimere il log.
   - In Microsoft Edge/Internet Explorer fare clic con il pulsante destro del mouse sugli errori e selezionare **Copia tutto**.
7. Chiudere Strumenti di sviluppo.
